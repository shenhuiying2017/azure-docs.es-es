---
title: "Reprotección desde Azure a un sitio local | Microsoft Docs"
description: "Después de que se produzca una conmutación por error de máquinas virtuales en Azure, puede iniciar una conmutación por recuperación para volver a poner las máquinas virtuales en el entorno local. Aprenda a los pasos para llevar a cabo una reprotección antes de una conmutación por recuperación."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 7b7177faa9fa571d3a62ee15b4a0fbfdab3a097f
ms.lasthandoff: 03/24/2017


---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Reprotección desde Azure a un sitio local

## <a name="overview"></a>Información general
En este artículo se describe cómo reproteger máquinas virtuales desde Azure al sitio local. Siga las instrucciones que se describen en este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que se hayan conmutado por error del sitio local a Azure según [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-failover.md).

Una vez que la reprotección haya finalizado y las máquinas virtuales protegidas se estén replicando, puede iniciar una conmutación por recuperación en las máquinas virtuales para llevarlas al sitio local.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

El siguiente vídeo es una breve introducción a la conmutación por error de Azure a un sitio local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Requisitos previos
A continuación, se describen los requisitos previos que debe llevar a cabo o considerar cuando se prepare para una reprotección.

* Si las máquinas virtuales que desea conmutar por recuperación las administra un servidor vCenter, debe asegurarse de tener los permisos necesarios para la detección de máquinas virtuales en servidores vCenter. [Más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Si existen instantáneas en la máquina virtual local, la reprotección dará error. Puede eliminar las instantáneas antes de llevar a cabo la reprotección.
* Antes de conmutar por recuperación, deberá crear dos componentes adicionales:
  * **Crear un servidor de procesos**. El servidor de procesos recibe datos de la máquina virtual protegida en Azure y los envía al sitio local. Se requiere una red de baja latencia entre el servidor de procesos y la máquina virtual protegida. Por lo tanto, puede tener un servidor de procesos local si está utilizando una conexión Azure ExpressRoute o un servidor de procesos de Azure si está usando una VPN.
  * **Crear un servidor de destino principal**: este servidor recibe datos de conmutación por recuperación. El servidor de administración local que creó tiene instalado de forma predeterminada un servidor de destino maestro. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino principal distinto para esta operación.
        * [Una máquina virtual Linux necesita un servidor de destino maestro Linux](site-recovery-how-to-install-linux-master-target.md).
        * Una máquina virtual Windows necesita un servidor de destino maestro Windows. Puede usar el servidor de procesos local y las máquinas de destino maestro de nuevo.
* Para la conmutación por recuperación se necesita un servidor de configuración local. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. En caso contrario, la conmutación por recuperación no será correcta. Asegúrese de realizar una copia de seguridad programada periódica del servidor. En caso de desastre, tiene que restaurar el servidor con la misma dirección IP para que funcione la conmutación por recuperación.
* Asegúrese de establecer la configuración disk.EnableUUID=true en los parámetros de configuración de la máquina virtual de destino maestra en VMware. Si la fila no existe, agréguela. Esta configuración es necesaria a fin de proporcionar un UUID uniforme al disco de máquina virtual (VMDK) para que se monte correctamente.
* *No se puede utilizar vMaster de almacenamiento para el servidor de destino maestro*. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se iniciará porque los discos no estarán disponibles para ella.
* Necesita agregar una nueva unidad al servidor de destino maestro. Esta unidad se denomina "unidad de retención". Agregue un nuevo disco y formatee la unidad.
* El destino maestro tiene otros requisitos previos que se enumeran en la sección [Comprobaciones habituales tras completar la instalación del servidor de destino maestro](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>¿Por qué se necesita una VPN S2S o una conexión ExpressRoute para replicar datos en un sitio local?
Si la replicación desde el entorno local a Azure puede producirse a través de Internet o una conexión ExpressRoute con emparejamiento público, la reprotección y la conmutación por recuperación requieren una VPN de sitio a sitio (S2S) configurada para replicar datos. La red debe proporcionarse para que las máquinas virtuales que hayan conmutado por error en Azure puedan alcanzar (ping) el servidor de configuración local. Es posible que también vaya a implementar un servidor de procesos en la red de Azure de la máquina virtual que se conmutó por error. Este servidor de procesos también debería ser capaz de comunicarse con el servidor de configuración local.

### <a name="when-should-i-install-a-process-server-in-azure"></a>¿Cuándo se debe instalar un servidor de procesos en Azure?


Las máquinas virtuales en Azure que desea volver a proteger envían datos de replicación a un servidor de procesos. Su red debe configurarse para que las máquinas virtuales en Azure puedan acceder al servidor de procesos.

Puede implementar un servidor de procesos en Azure o usar el servidor de procesos existente que usó durante la conmutación por error. El aspecto importante que se debe tener en cuenta es la latencia para enviar los datos desde las máquinas virtuales en Azure al servidor de procesos.

Si tiene configurada una conexión ExpressRoute, se puede usar un servidor de procesos local para enviar datos porque la latencia entre la máquina virtual y el servidor de procesos es baja.

 ![Diagrama de la arquitectura de ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Sin embargo, si solo dispone de una VPN S2S, se recomienda implementar el servidor de procesos en Azure.

 ![Diagrama de la arquitectura de VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Recuerde que solo se realizará la replicación a través de la VPN S2S o el emparejamiento privado de la red ExpressRoute. Asegúrese de que hay suficiente ancho de banda disponible a través de ese canal de red.

Lea más sobre cómo instalar un [servidor de procesos de Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

### <a name="what-are-the-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>¿Cuáles son los puertos que se deben abrir en distintos componentes para que pueda funcionar la reprotección?

![Conmutación por error o recuperación: todos los puertos](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-should-be-used-for-reprotect"></a>¿Qué servidor de destino maestro se debería usar para la reprotección?
Se requiere un servidor de destino maestro local para recibir datos del servidor de procesos y después escribir en el VMDK de la máquina virtual local. Si va a proteger máquinas virtuales Windows, necesita un servidor de destino maestro Windows. Puede reutilizar el servidor de procesos local y el destino maestro <!-- !todo component -->. Para las máquinas virtuales Linux, debe configurar un destino maestro Linux local adicional.


Haga clic en los vínculos siguientes para leer sobre cómo instalar un servidor de destino maestro:

* [Instalación del servidor de destino maestro Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Instalación del servidor de destino maestro de Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Comprobaciones habituales tras completar la instalación del servidor de destino maestro

* Si la máquina virtual está presente de forma local en el servidor vCenter, el servidor de destino maestro necesita acceso al VMDK de la máquina virtual local. Se necesita acceso para escribir los datos replicados en los discos de la máquina virtual. Asegúrese de que el almacén de datos de la máquina virtual local esté montado en el host del destino maestro con acceso de lectura y escritura.

* Si la máquina virtual no está presente de forma local en el servidor vCenter, debe crear una durante la reprotección. Esta máquina virtual se creará en el host ESX donde se crea el destino maestro. Elija con cuidado el host ESX para que se cree la máquina virtual de conmutación por recuperación en el host que desee.

* *No se puede utilizar vMotion de almacenamiento para el servidor de destino maestro*. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se iniciará porque los discos no estarán disponibles para ella.

* Necesita agregar una nueva unidad al servidor de destino maestro Windows existente. Esta unidad se denomina "unidad de retención". Agregue un nuevo disco y formatee la unidad. La unidad de retención se utiliza para detener los momentos dados cuando la máquina virtual se replica de vuelta en el sitio local. A continuación, se indican algunos criterios para una unidad de retención, sin los cuales no aparecerá en la lista para el servidor de destino maestro.

   * El volumen no puede utilizarse para ningún otro propósito, como destino de replicación, etc.

   * El volumen no debe estar en modo de bloqueo.

   * El volumen no debe ser un volumen de memoria caché. La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizada del servidor de procesos y el destino maestro no es apto para el volumen de retención. Cuando el servidor de procesos y el destino maestro se instalan en un volumen, este es un volumen de memoria caché del destino maestro.

   * El tipo de sistema de archivos del volumen no debe ser FAT ni FAT32.

   * La capacidad del volumen debe ser distinta de cero.

   * El volumen de retención predeterminado para Windows es el volumen R.

   * El volumen de retención predeterminado para Linux es /mnt/retention.

* Una máquina virtual Linux conmutada por error necesita un servidor de destino maestro Linux. Una máquina virtual Windows conmutada por error necesita un servidor de destino maestro Windows.

* Instale las herramientas de VMware en el servidor de destino principal. Sin las herramientas de VMware, los almacenes de datos del host ESXi del destino maestro no se pueden detectar.

* Habilite el parámetro disk.EnableUUID=true en la máquina virtual del destino maestro mediante las propiedades de vCenter. <!-- !todo Needs link. -->

* El destino maestro debe tener al menos un almacén de datos de sistema de archivos de máquina virtual (VMFS) conectado. Si no hay ninguno, la entrada **Almacén de datos** en la página de reprotección estará vacía y no podrá continuar.

* El servidor de destino maestro no puede tener instantáneas en los discos. Si las hay, se producirá un error en la reprotección y la conmutación por recuperación.

* El destino maestro no puede tener ninguna controladora SCSI paravirtual. La controladora solo puede ser una controladora de LSI Logic. Sin ella, se producirá un error en la reprotección.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Pasos para la reprotección

Antes de volver a realizar la protección, asegúrese de instalar el [servidor de procesos](site-recovery-vmware-setup-azure-ps-resource-manager.md) en Azure y el [destino maestro Linux](site-recovery-how-to-install-linux-master-target.md) o Windows local.

> [!NOTE]
> Después del arranque de una máquina virtual en Azure, el agente tarda algún tiempo en registrar de nuevo en el servidor de configuración (hasta 15 minutos). Durante este tiempo, la reprotección producirá errores y un mensaje de error indica que el agente no está instalado. Espere unos minutos y vuelva a intentarlo.



1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que ha sido objeto de la conmutación por error y seleccione **Reproteger**. También puede hacer clic en la máquina y seleccionar **Reproteger** con los botones de comando.
2. En la hoja, observe que la dirección de protección **De Azure a local** ya está seleccionada.
3. En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos.
4. Seleccione en **Almacén de datos** aquel en el que desee recuperar los discos en el entorno local. Se usa esta opción cuando se elimina la máquina virtual local y es necesario crear discos. Esta opción se omite si los discos ya existen, pero debe especificar un valor.
5. Elija la unidad de retención.
6. La directiva de conmutación por recuperación será seleccionada automáticamente.
7. Tras hacer clic en **Aceptar** para comenzar la reprotección, comienza un trabajo para replicar la máquina virtual de Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** .

Si desea recuperar en una ubicación alternativa (cuando se elimine la máquina virtual local), seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino maestro. Cuando se conmuta por recuperación al sitio local, las máquinas virtuales VMware del plan de protección de conmutación por recuperación utilizarán el mismo almacén de datos que el servidor de destino maestro. Se creará una máquina virtual en vCenter.
Si desea recuperar la máquina virtual en Azure en una máquina virtual local existente, los almacenes de datos de la máquina virtual local deben montarse con acceso de lectura y escritura en el host ESXi del servidor de destino maestro.
    ![Cuadro de diálogo Reproteger](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

También puede volver a proteger en el nivel de plan de recuperación. Solo se puede reproteger un grupo de replicación mediante un plan de recuperación. Cuando vuelva a proteger mediante un plan de recuperación, necesitará proporcionar los valores para cada máquina protegida.

> [!NOTE]
> Un grupo de replicación debe volver a protegerse con el mismo destino maestro. Si se ha vuelto a proteger con un servidor de destino maestro distinto, el servidor no puede proporcionar un momento dado común.


Una vez que la reprotección se haya completado correctamente, la máquina virtual entrará en un estado protegido.

## <a name="next-steps"></a>Pasos siguientes

Una vez que la máquina virtual haya entrado en un estado protegido, puede iniciar una conmutación por recuperación. La conmutación por recuperación apagará la máquina virtual en Azure y arrancará la máquina virtual local. Por lo tanto, hay un breve tiempo de inactividad para la aplicación. Así pues, elija para la conmutación por recuperación un momento en que la aplicación pueda tolerar tiempo de inactividad.

[Pasos para iniciar la conmutación por recuperación de la máquina virtual](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back)

## <a name="common-issues"></a>Problemas comunes

* Si usó una plantilla para crear las máquinas virtuales, asegúrese de que cada una tenga un UUID único para los discos. Si el UUID de la máquina virtual local entra en conflicto con el del destino maestro porque ambos se crearon con la misma plantilla, la reprotección producirá un error. Debe implementar otro destino maestro que no se haya creado con la misma plantilla.
* Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, la protección se ejecutará correctamente y la conmutación por error funcionará. Durante la reprotección, se produce un error de conmutación por error porque no se pueden detectar los almacenes de datos. El síntoma de este problema es que no se ven los almacenes de datos enumerados durante la reprotección. Para resolverlo, puede actualizar las credenciales de vCenter con la cuenta adecuada que tenga permisos y tratar de realizar el trabajo de nuevo. Para obtener más información, consulte [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Cuando conmuta por recuperación una máquina virtual Linux y la ejecuta localmente, verá que se ha desinstalado el paquete del administrador de red de la máquina. Esto se debe a que el paquete del administrador de red se elimina cuando se recupera la máquina virtual en Azure.
* Cuando una máquina virtual Linux se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP desde DHCP. Al conmutar por error a un entorno local, la máquina virtual seguirá utilizando DHCP para obtener la dirección IP. Inicie sesión manualmente en la máquina y vuelva a establecer la dirección IP en una dirección estática si es necesario. Una máquina virtual Windows puede adquirir su dirección IP estática de nuevo.
* Si usa las ediciones gratuitas de ESXi 5.5 o vSphere 6 Hypervisor, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Para habilitar la conmutación por recuperación, tendrá que actualizar los programas con una licencia de evaluación.
* Si no puede ponerse en contacto con el servidor de configuración desde el servidor de procesos, use Telnet para comprobar la conectividad con el servidor de configuración en el puerto 443. También puede tratar de hacer ping al servidor de configuración desde el servidor de procesos. Un servidor de procesos debe tener también un latido cuando esté conectado al servidor de configuración.
* Si está tratando de realizar la conmutación por recuperación en un vCenter alternativo, asegúrese de que se detectan tanto el nuevo vCenter como el servidor de destino maestro. Un síntoma típico es que los almacenes de datos no están accesibles ni visibles en el cuadro de diálogo de **Reprotect** (Reproteger).
* No se puede llevar a cabo la conmutación por recuperación de un servidor Windows Server 2008 R2 SP1 que esté protegido como servidor local físico desde Azure a un sitio local.

