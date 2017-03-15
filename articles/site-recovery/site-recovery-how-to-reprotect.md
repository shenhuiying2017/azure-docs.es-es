---
title: "Reprotección desde Azure a un entorno local | Microsoft Docs"
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
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 39e91dea775cf03f726db29f27f30142b69f9dfe
ms.lasthandoff: 02/22/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>Reprotección desde Azure a un entorno local

## <a name="overview"></a>Información general
En este artículo se describe cómo reproteger máquinas virtuales desde Azure al sitio local. Siga las instrucciones que se describen en este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que han conmutado por error desde el sitio local a Azure mediante este [tutorial](site-recovery-vmware-to-azure-classic.md).

Una vez que la reprotección se haya completado y las máquinas virtuales protegidas se estén replicando, puede iniciar una conmutación por recuperación en la máquina local para llevarlas al entorno local.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Aquí puede ver un vídeo introductorio rápido.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

Conozca el proceso completo de conmutación por recuperación aquí.

## <a name="pre-requisites"></a>Requisitos previos
A continuación se describen los requisitos previos pasos que debe llevar a cabo o considerar cuando se esté preparando para una reprotección.

* Si las máquinas virtuales que desea conmutar por recuperación las administra un servidor vCenter, debe asegurarse de tener los permisos necesarios para la detección de máquinas virtuales en servidores vCenter. [Más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Si existen instantáneas en una máquina virtual local, la reprotección dará error. Puede eliminar las instantáneas antes de llevar a cabo la reprotección.
* Antes de conmutar por recuperación, deberá crear dos componentes adicionales:
  * **Crear un servidor de procesos**. El servidor de procesos se usa para recibir los datos de la máquina virtual protegida en Azure y enviar los datos de forma local. Esto requiere disponer de una red de baja latencia entre el servidor de procesos y la máquina virtual protegida. Por lo tanto, el servidor de procesos puede estar en un entorno local (si está utilizando una conexión de ExpressRoute) o en Azure si está usando una VPN.
  * **Crear un servidor de destino principal**: este servidor recibe datos de conmutación por recuperación. El servidor de administración que creó de forma local tiene instalado de forma predeterminada un servidor de destino maestro. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino maestro distinto para esta operación. 
        * [Una máquina virtual Linux necesita un servidor de destino principal de Linux](site-recovery-how-to-install-linux-master-target.md). 
        * Una máquina virtual con Windows necesita un destino principal de Windows. Puede reutilizar la máquina PS+MT local.
* El servidor de configuración es necesario localmente cuando realiza una conmutación por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. De lo contrario, la conmutación por recuperación no se realizará correctamente. Por lo tanto, asegúrese de realizar una copia de seguridad programada periódica del servidor. En caso de desastre, tendrá que restaurarla con la misma dirección IP para que funcione la conmutación por recuperación.
* Asegúrese de establecer la opción disk.enableUUID=true de los parámetros de configuración de la máquina virtual de destino maestra en VMware. Si la fila no existe, agréguela. Este paso es necesario a fin de proporcionar un UUID uniforme al VMDK, para que se monte correctamente.
* **El servidor de destino maestro no puede migrarse con Storage vMotion**. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se iniciará puesto que los discos no estarán disponibles.
* Necesita una nueva unidad en el servidor de destino maestro. Esta unidad se denomina "unidad de retención". Agregue un nuevo disco y formatee la unidad.
* El destino maestro tiene otros requisitos previos que se enumeran en la sección [Cosas habituales que debe comprobar en un destino maestro antes de efectuar la reprotección](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>¿Por qué se necesita una VPN S2S o conexión ExpressRoute para replicar datos en un entorno local?
Si la replicación desde local a Azure puede producirse a través de Internet o una conexión ExpressRoute con emparejamiento público, la reprotección y la conmutación por recuperación requieren una VPN S2S configurada para replicar los datos. **La red debe proporcionarse de forma que las máquinas virtuales que hayan conmutado por error en Azure puedan alcanzar (ping) el servidor de configuración local**. También puede que esté implementando un servidor de procesos en la red de Azure de la máquina virtual que ha conmutado por error: este servidor de procesos también debe tener la capacidad de comunicarse con el servidor de configuración local.

### <a name="when-should-i-install-a-process-server-in-azure"></a>¿Cuándo se debe instalar un servidor de procesos en Azure?


Las máquinas virtuales de Azure que desea volver a proteger envían los datos de replicación a un servidor de procesos. La red se debe configurar de modo que el servidor de procesos sea accesible desde la máquina virtual de Azure.

Puede implementar un servidor de procesos en Azure o usar el servidor de procesos existente que usó durante la conmutación por error. El punto importante que se debe tener en cuenta es la latencia para enviar los datos desde las máquinas virtuales de Azure al servidor de procesos. 

* Si tiene configurada una conexión ExpressRoute, se puede utilizar un servidor de procesos local para enviar los datos. Esto es porque se produciría baja la latencia entre la máquina virtual y el servidor de procesos.
    
    ![Diagrama de la arquitectura de ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* Sin embargo, si solo dispone de una VPN S2S, se recomienda implementar el servidor de procesos en Azure.

    ![Diagrama de la arquitectura de VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Recuerde que solo se realizará la replicación a través de VPN S2S o el emparejamiento privado de la red ExpressRoute. Asegúrese de que hay suficiente ancho de banda disponible a través de ese canal de red.

Lea más sobre cómo instalar un [servidor de proceso de Azure aquí](site-recovery-vmware-setup-azure-ps-resource-manager.md).

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>¿Cuáles son los puertos diferentes que se deben abrir en distintos componentes para que pueda funcionar la reprotección?

![Conmutación por error/recuperación de todos los puertos](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>¿Qué servidor de destino maestro se usará para la reprotección?
Se requiere un servidor de destino principal en el entorno local para recibir los datos desde el servidor de procesos y, a continuación, escribir en el VMDK de la máquina virtual local. Si va a proteger máquinas virtuales de Windows, necesitará un servidor de destino principal de Windows y, en este caso, puede reutilizar el servidor de procesos y el destino principal locales <!-- !todo component -->. En el caso de máquinas virtuales de Linux, debe configurar un destino principal de Linux adicional a nivel local.


Haga clic en los vínculos siguientes para conocer los pasos necesarios para instalar un servidor de destino maestro.

* [How to install Windows Master Target server](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup) (Instalación del servidor de destino principal de Windows)
* [Instalación del servidor de destino principal de Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>Comprobaciones habituales tras completar la instalación del destino principal

* Si la máquina virtual está presente en local en el servidor vCenter, el servidor de destino principal necesita acceso al VMDK de la máquina virtual local. Es necesario para escribir los datos replicados en discos de la máquina virtual. Para ello, debe asegurarse de que **el almacén de datos de la máquina virtual local se monte en el host del destino principal con acceso de lectura y escritura**.

* Si la máquina virtual no está presente en local en el servidor vCenter, necesitará crear una nueva máquina virtual durante la reprotección. Esta máquina virtual se creará en el host ESX en que se crea el destino principal. Así pues, elija con cuidado el host ESX a fin de que la máquina virtual de la conmutación por recuperación se cree en el host que desea.
    
* **El servidor de destino maestro no puede migrarse con Storage vMotion**. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se iniciará puesto que los discos no estarán disponibles.

* Necesita una nueva unidad en el servidor de destino maestro de Windows existente. Esta unidad se denomina "unidad de retención". Agregue un nuevo disco y formatee la unidad. La unidad de retención se utiliza para detener los puntos en el tiempo cuando la máquina virtual se replica en local. A continuación se indican algunos de los criterios de una unidad de retención, sin los cuales la unidad no aparecerá para el servidor de destino maestro.
   
   * El volumen no puede utilizarse para ningún otro propósito (destino de replicación, etc.).

   * El volumen no debe estar en modo de bloqueo.

   * El volumen no debe ser el volumen de memoria caché. (La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizada del servidor de procesos y el destino maestro no es apto para el volumen de retención. Aquí el volumen del servidor de procesos y el destino maestro es el volumen de memoria caché del destino maestro.

   * El tipo de sistema de archivos de volumen no debe ser FAT ni FAT32.

   * La capacidad del volumen debe ser distinta de cero.

   * El volumen de retención predeterminado para Windows es el volumen R.

   * El volumen de retención predeterminado para Linux es /mnt/retention.

* Una máquina virtual de Linux que haya conmutado por error necesita un servidor de destino principal de Linux. Una máquina virtual de Windows que haya conmutado por error necesita un servidor de destino principal de Windows.

* Instale las herramientas de VMware en el servidor de destino principal. Sin las herramientas de VMware, los almacenes de datos del host de ESXi de destino principal no se pueden detectar.

* habilite el parámetro disk.EnableUUID = True en la máquina virtual del destino principal a través de las propiedades de vCenter. <!-- !todo Needs link. -->

* El destino maestro debe tener al menos un almacén de datos de VMFS conectado. Si no hay ninguno, la entrada de almacén de datos de la página de reprotección estará vacía y no podrá continuar.

* El servidor de destino maestro no puede tener ninguna instantánea en los discos. Si hay instantáneas, se producirá un error en la conmutación por recuperación o en la reprotección.

* El destino maestro no puede tener ninguna controladora SCSI paravirtual. Solo puede tener una controladora lógica de LSI. Sin ella, se producirá un error en la reprotección.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Pasos para la reprotección

Antes de volver a realizar la protección, asegúrese de que ha instalado el [servidor de proceso](site-recovery-vmware-setup-azure-ps-resource-manager.md) y el [destino maestro](site-recovery-how-to-install-linux-master-target.md) local de Windows o Linux.

> [!NOTE]
> Después de que una máquina virtual arranca en Azure, el agente tarda algún tiempo en registrar de nuevo en el servidor de configuración (hasta 15 minutos). Durante este tiempo, verá que la reprotección produce errores y recibirá un mensaje de error que indica que el agente no está instalado. Espere unos minutos y vuelva a intentarlo.
 
 

1. En Almacén > Elementos replicados, seleccione la máquina virtual que ha sido objeto de la conmutación por error y haga clic con el botón derecho en **Reproteger**. También puede hacer clic en la máquina y seleccionar la opción para reproteger con los botones de comando.
2. En la hoja, puede ver que la dirección de protección "De Azure a local" ya está seleccionada.
3. En **Servidor de destino principal** y **Servidor de procesos**, seleccione el servidor de destino principal local y el servidor de procesos.
4. Seleccione el **almacén de datos** en el que desea recuperar los discos en el entorno local. Esta opción se utiliza cuando se elimina la máquina virtual local y es necesario crear discos nuevos. Esta opción se omite si los discos ya existen, pero debe especificar un valor.
5. Elija la unidad de retención. 
6. La directiva de conmutación por recuperación será seleccionada automáticamente.
7. Tras hacer clic en **Aceptar** para comenzar la reprotección, se inicia un trabajo para replicar la máquina virtual de Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** .

Si desea recuperar en una ubicación alternativa (cuando se elimine la máquina virtual local), seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino principal. Cuando se conmuta por recuperación al sitio local, las máquinas virtuales de VMware del plan de protección de conmutación por recuperación utilizarán el mismo almacén de datos que el servidor de destino principal y se creará una nueva máquina virtual en vCenter. Si desea recuperar la máquina virtual de Azure en una máquina virtual local, los almacenes de datos de la máquina virtual local deben montarse con acceso de lectura y escritura en el host de ESXi del servidor de destino principal.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

También puede volver a proteger a nivel de un plan de recuperación. Si tiene un grupo de replicación, este solo se puede volver a proteger con un plan de recuperación. Al volver a proteger con un plan de recuperación, debe proporcionar los valores anteriores para cada máquina protegida.

> [!NOTE]
> Un grupo de replicación debe volver a protegerse con el mismo destino maestro. Si se ha vuelto a proteger con un servidor de destino maestro distinto, no se puede obtener para él un momento dado común.
 

Una vez que la reprotección se haya completado correctamente, la máquina virtual entrará en un estado protegido.

## <a name="next-steps"></a>Pasos siguientes

Cuando la máquina virtual se encuentre en estado protegido, puede iniciar una conmutación por recuperación. La conmutación por recuperación apagará la máquina virtual en Azure y arrancará la máquina virtual local. Por lo tanto, hay un breve tiempo de inactividad para la aplicación. Asó pues, elija para la conmutación por recuperación un momento en que la aplicación pueda hacer frente a un tiempo de inactividad.

[Pasos para iniciar la conmutación por recuperación de la máquina virtual](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>Problemas comunes 

* Si las máquinas virtuales se crearon mediante una plantilla, debe asegurarse antes de realizar la protección de que cada máquina virtual tiene un UUID único para los discos. Si el UUID de las máquinas virtuales locales entra en conflicto con el del destino maestro (porque ambos se hayan creado a partir de la misma plantilla), la reprotección producirá un error. Deberá implementar otro destino maestro que no se haya creado a partir de la misma plantilla.
* Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, se ejecutará correctamente y la conmutación por error funcionará. Durante la reprotección, se produce un error de conmutación por error porque no se pueden detectar los almacenes de datos. El síntoma de este problema es que no se ven los almacenes de datos enumerados durante la reprotección. Para resolverlo, puede actualizar las credenciales de vCenter con la cuenta adecuada que tenga permisos y tratar de realizar el trabajo de nuevo. Para obtener más información, consulte [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Cuando conmuta por recuperación una máquina virtual de Linux y la ejecuta localmente, verá que se ha desinstalado el paquete del administrador de red de la máquina. Esto se debe a que el paquete del administrador de red se elimina cuando se recupera la VM en Azure.
* Cuando una máquina virtual Linux se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP mediante DHCP. Al conmutar por error a un entorno local, la VM seguirá utilizando DHCP para obtener la dirección IP. Inicie sesión manualmente en el equipo y vuelva a establecer la dirección IP en una dirección estática si es necesario. Una máquina virtual Windows puede readquirir su dirección IP estática.
* Si utiliza las ediciones gratuitas de ESXi 5.5 o vSphere Hypervisor 6, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Para habilitar la conmutación por recuperación, tendrá que actualizar los programas con una licencia de evaluación.
* Si no puede ponerse en contacto con el servidor de configuración desde el servidor de procesos, compruebe la conectividad con el servidor de configuración por Telnet con el equipo del servidor de configuración en el puerto 443. También puede tratar de hacer ping en el servidor de configuración desde la máquina del servidor de procesos. Un servidor de procesos debe tener también un latido cuando esté conectado al servidor de configuración.
* Si está tratando de realizar la conmutación por recuperación en un vCenter alternativo, asegúrese de que se detectan tanto el nuevo vCenter como el servidor de destino maestro. Un síntoma típico es que los almacenes de datos no están accesibles ni visibles en el cuadro de diálogo de **Reprotect** (Reproteger).
* Las máquinas WS2008R2SP1 protegidas como máquinas físicas locales no pueden conmutar por recuperación desde Azure a un entorno local.
