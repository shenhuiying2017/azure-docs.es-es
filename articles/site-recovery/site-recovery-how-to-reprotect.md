---
title: "Reprotección desde Azure en un sitio local | Microsoft Docs"
description: "Después de que se produzca una conmutación por error de máquinas virtuales en Azure, puede iniciar una conmutación por recuperación para volver a poner las máquinas virtuales en el entorno local. Aprenda a reproteger antes de una conmutación por recuperación."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: 17a43de3faaa3a146fa9d8f43d36545d6d82b274
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Reprotección desde Azure a un sitio local



## <a name="overview"></a>Información general
En este artículo se explica cómo reproteger máquinas virtuales de Azure desde Azure en un sitio local. Siga las instrucciones de este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que se hayan conmutado por error desde el sitio local en Azure (como se explica en [Replicación de máquinas virtuales VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> Si ha [finalizado la migración](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), ha trasladado la máquina virtual a otro grupo de recursos o ha eliminado la máquina virtual de Azure, no puede entonces realizar la conmutación por recuperación. Si deshabilita la protección de la máquina virtual, no es posible realizar la conmutación por recuperación. Si la máquina virtual se creó por primera vez en Azure (nacido en la nube) no se puede volver a proteger en local. La máquina se debe haber protegido inicialmente en local y haber conmutador por error a Azure antes de la reprotección.


Cuando termine la reprotección y las máquinas virtuales protegidas se estén replicando, puede iniciar una conmutación por recuperación en las máquinas virtuales para llevarlas al sitio local.

> [!NOTE]
> Solo puede realizar la reprotección y efectuar la conmutación por recuperación de un host ESXi. No puede realizar la conmutación por recuperación de la máquina virtual en hosts de Hyper-v, estaciones de trabajo de VMware o cualquier otra plataforma de virtualización.

Publique cualquier comentario o pregunta que tenga al final del artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

El siguiente vídeo es una breve introducción a la conmutación por error de Azure a un sitio local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Durante la conmutación por error en Azure, es posible que no se pueda acceder al sitio local y, por lo tanto, el servidor de configuración puede estar no disponible o apagado. Durante la reprotección y la conmutación por recuperación, el servidor de configuración local debe estar ejecutándose y en un estado correcto de conexión.


Cuando se prepare para reproteger máquinas virtuales, tome o considere las siguientes medidas de requisitos previos:

* Si un servidor vCenter administra las máquinas virtuales en las que quiere conmutar por recuperación, asegúrese de tener los [permisos necesarios](site-recovery-vmware-to-azure-classic.md) para la detección de máquinas virtuales en servidores vCenter.

  > [!WARNING]
  > Si existen instantáneas en el destino maestro local o la máquina virtual, la reprotección no se realiza correctamente. Puede eliminar las instantáneas del destino maestro antes de continuar con la reprotección. Las instantáneas de la máquina virtual se combinan automáticamente durante un trabajo de reprotección.

* Antes de conmutar por recuperación, cree dos componentes adicionales:

  * **Servidor de procesos**: el [servidor de procesos](site-recovery-vmware-setup-azure-ps-resource-manager.md) recibe datos de la máquina virtual protegida en Azure y los envía al sitio local. Se requiere una red de baja latencia entre el servidor de procesos y la máquina virtual protegida. Por lo tanto, puede tener un servidor de procesos local si está usando una conexión Azure ExpressRoute o un servidor de procesos basado en Azure y una VPN.
  
  * **Servidor de destino maestro**: este servidor recibe datos de conmutación por recuperación. El servidor de administración local que creó tiene instalado de forma predeterminada un servidor de destino maestro. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino principal distinto para esta operación.
    * [Una máquina virtual Linux necesita un servidor de destino maestro Linux](site-recovery-how-to-install-linux-master-target.md).
    * Una máquina virtual Windows necesita un servidor de destino maestro Windows. Puede usar el servidor de procesos local y las máquinas de destino maestro de nuevo.
    * El destino maestro tiene otros requisitos previos que se enumeran en [Comprobaciones habituales en un destino maestro antes de reproteger](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

> [!NOTE]
> Todas las máquinas virtuales de un grupo de replicación deben ser del mismo tipo de sistema operativo (todas Windows o todas Linux). Un grupo de replicación con sistemas operativos mezclados no se admite actualmente para reprotección y conmutación por recuperación a local. Esto se debe a que el destino maestro debe ser del mismo sistema operativo que la máquina virtual y todas las máquinas virtuales de un grupo de replicación deben tener el mismo destino maestro. 

    

* Se necesita un servidor de configuración en local al conmutar por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. En caso contrario, la conmutación por recuperación no será correcta. 

> [!IMPORTANT]
> Asegúrese de realizar una copia de seguridad programada periódica del servidor de configuración. En caso de desastre, restaure el servidor con la misma dirección IP para que la conmutación por recuperación funcione.

> [!WARNING]
> Un grupo de replicación solamente debe tener VM Windows o VM Linux, y no una combinación de ambas porque todas las VM de un grupo de replicación utilizan el mismo servidor de destino maestro y la VM Linux requiere un servidor de destino maestro Linux y, lo mismo sucede para una VM Windows.

* Establezca `disk.EnableUUID=true` en los parámetros de configuración de la máquina virtual del destino maestro en VMware. Si la fila no existe, agréguela. Esta configuración es necesaria a fin de proporcionar un UUID uniforme al disco de máquina virtual (VMDK) para que se monte correctamente.

* *No se puede usar Storage vMotion en el servidor de destino maestro*. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se puede iniciar porque los discos no están disponibles para ella. Para evitar este problema, excluya los servidores de destino maestros de la lista de vMotion.

* Agregue una nueva unidad al servidor de destino maestro: una unidad de retención. Agregue un nuevo disco y formatee la unidad.


### <a name="frequently-asked-questions"></a>Preguntas más frecuentes

#### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>¿Por qué se necesita una VPN S2S o una conexión ExpressRoute para replicar datos en un sitio local?
Aunque la replicación desde el entorno local en Azure puede producirse a través de Internet o una conexión ExpressRoute con emparejamiento público, la reprotección y la conmutación por recuperación requieren una VPN de sitio a sitio (S2S) para replicar datos. Proporcione la red para que las máquinas virtuales que hayan conmutado por error en Azure puedan alcanzar (ping) el servidor de configuración local. También podría implementar un servidor de procesos en la red de Azure de la máquina virtual conmutada por error. Este servidor de procesos también debería ser capaz de comunicarse con el servidor de configuración local.

#### <a name="when-should-i-install-a-process-server-in-azure"></a>¿Cuándo se debe instalar un servidor de procesos en Azure?


Las máquinas virtuales en Azure que desea volver a proteger envían datos de replicación a un servidor de procesos. Configure la red de modo que las máquinas virtuales en Azure puedan acceder al servidor de procesos.

Puede implementar un servidor de procesos en Azure o usar el servidor de procesos existente que usó durante la conmutación por error. El aspecto importante que se debe tener en cuenta es la latencia para enviar los datos desde las máquinas virtuales en Azure al servidor de procesos.

Si tiene configurada una conexión ExpressRoute, puede usar un servidor de procesos local para enviar datos, ya que la latencia entre la máquina virtual y el servidor de procesos es baja.

 ![Diagrama de la arquitectura de ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Pero si solo dispone de una VPN S2S, se recomienda implementar el servidor de procesos en Azure.

 ![Diagrama de la arquitectura de VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Recuerde que solo se realizará la replicación de Azure a local a través de la VPN S2S o el emparejamiento privado de la red ExpressRoute. Asegúrese de que hay suficiente ancho de banda disponible a través de ese canal de red.

Para más información sobre cómo instalar un servidor de procesos basado en Azure, vea [Administración de un servidor de procesos que se ejecuta en Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Se recomienda usar un servidor de procesos basado en Azure durante la conmutación por recuperación. El rendimiento de replicación es mayor si el servidor de procesos está más próximo a la máquina virtual que se está replicando (la máquina conmutada por error en Azure). Pero durante la prueba de concepto (POC) o las demostraciones, puede usar el servidor de procesos local con ExpressRoute con emparejamiento privado para completar la POC más rápido.

> [!NOTE]
> La replicación de local a Azure se puede producir solo a través de Internet o ExpressRoute con emparejamiento público. La replicación de Azure a local se puede producir solo a través de VPN S2S o ExpressRoute con emparejamiento privado.


#### <a name="what-ports-should-i-open-on-different-components-so-that-reprotection-can-work"></a>¿Qué puertos se deben abrir en distintos componentes para que la reprotección funcione?

![Puertos para conmutación por error y conmutación por recuperación](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### <a name="which-master-target-server-should-i-use-for-reprotection"></a>¿Qué servidor de destino maestro se debería usar para la reprotección?
Se requiere un servidor de destino maestro local para recibir datos del servidor de procesos y después escribir en el VMDK de la máquina virtual local. Si va a proteger máquinas virtuales Windows, necesita un servidor de destino maestro Windows. Puede volver a usar el servidor de procesos local y el destino maestro <!-- !todo component -->. Para las máquinas virtuales Linux, debe configurar un destino maestro Linux local adicional.


Para más información sobre cómo instalar un servidor de destino maestro, vea:

* [Instalación del servidor de destino maestro Windows](site-recovery-vmware-to-azure.md)
* [Instalación del servidor de destino maestro de Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>¿Qué tipos de almacén de datos se admiten en el host ESXi local durante la conmutación por recuperación?

Actualmente, Azure Site Recovery admite la conmutación por recuperación únicamente en un almacén de datos de sistema de archivos de máquina virtual (VMFS) o vSAN. No se admiten los almacenes de datos NFS. Debido a esta limitación, la entrada de selección de almacén de datos en la pantalla de reprotección está vacía en el caso de los almacenes de datos NFS o muestra el almacén de datos vSAN, pero produce un error durante el trabajo. Si piensa conmutar por recuperación, puede crear un almacén de datos VMFS local y conmutar por recuperación en él. Esta conmutación por recuperación ocasionará una descarga completa del VMDK.

### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Comprobaciones habituales tras completar la instalación del servidor de destino maestro

* Si la máquina virtual está presente en local en el servidor vCenter, el servidor de destino maestro necesita acceso al VMDK de la máquina virtual local. Se necesita acceso para escribir los datos replicados en los discos de la máquina virtual. Asegúrese de que el almacén de datos de la máquina virtual local esté montado en el host del destino maestro con acceso de lectura y escritura.

* Si la máquina virtual no está presente en local en el servidor vCenter, el servicio Site Recovery tiene que crear una nueva máquina virtual durante la reprotección. Esta máquina virtual se crea en el host ESX en el que se crea el destino maestro. Elija con cuidado el host ESX para que se cree la máquina virtual de conmutación por recuperación en el host que desee.

* *No se puede utilizar vMotion de almacenamiento para el servidor de destino maestro*. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se puede iniciar porque los discos no están disponibles para ella.

  > [!WARNING]
  > Si un destino maestro se somete a una tarea de Storage vMotion después de la reprotección, los discos protegidos de las máquinas virtuales asociados al destino maestro migran al destino de la tarea de vMotion. Si intenta conmutar por recuperación después de esto, se produce un error de desasociación de los discos porque estos no se encuentran. Entonces se vuelve difícil encontrar los discos en las cuentas de almacenamiento. Debe encontrarlos manualmente y asociarlos a la máquina virtual. Después de eso se puede arrancar la máquina virtual local.

* Agregue una unidad de retención al servidor de destino maestro Windows existente. Agregue un nuevo disco y formatee la unidad. La unidad de retención se utiliza para detener los momentos dados cuando la máquina virtual se replica de vuelta en el sitio local. Estos son algunos criterios de una unidad de retención. Sin estos criterios, la unidad no se mostrará para el servidor de destino maestro.

   * El volumen no se usa para ningún otro propósito, como un destino de replicación.

   * El volumen no está en modo de bloqueo.

   * El volumen no es un volumen de caché. La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizado del servidor de procesos y el destino maestro no es apto para un volumen de retención. Cuando el servidor de procesos y el destino maestro se instalan en un volumen, este es un volumen de memoria caché del destino maestro.

   * El tipo de sistema de archivos del volumen no es FAT ni FAT32.

   * La capacidad del volumen es distinta de cero.

   * El volumen de retención predeterminado para Windows es el volumen R.

   * El volumen de retención predeterminado para Linux es /mnt/retention.

  > [!IMPORTANT]
  > Si está usando una máquina existente de servidor de procesos o de servidor de configuración o una escala o una máquina de servidor de procesos o de servidor de destino maestro, tiene que agregar una nueva unidad. La nueva unidad debe cumplir los requisitos anteriores. Si la unidad de retención no está presente, no aparece en la lista de selección desplegable en el portal. Después de agregar una unidad al destino maestro local, pasa un máximo de quince minutos hasta que la unidad aparece en la selección en el portal. También puede actualizar el servidor de configuración si la unidad no aparece pasados quince minutos.

* Una máquina virtual Linux conmutada por error necesita un servidor de destino maestro Linux. Una máquina virtual Windows conmutada por error necesita un servidor de destino maestro Windows.

* Instale las herramientas de VMware en el servidor de destino principal. Sin las herramientas de VMware, los almacenes de datos del host ESXi del destino maestro no se pueden detectar.

* Habilite el parámetro `disk.EnableUUID=true` en la máquina virtual del destino maestro mediante las propiedades de vCenter. <!-- !todo Needs link. -->

* El destino maestro debe tener al menos un almacén de datos de VMFS asociado. Si no hay ninguno, la entrada **Almacén de datos** de la página reprotegida estará vacía y no podrá continuar.

* El servidor de destino maestro no puede tener instantáneas en los discos. Si hay instantáneas, se produce un error en la reprotección y la conmutación por recuperación.

* El destino maestro no puede tener ninguna controladora SCSI paravirtual. La controladora solo puede ser una controladora de LSI Logic. Sin ella, se produce un error en la reprotección.

* En cualquier instancia dada, el destino maestro puede tener 60 discos como máximo conectados a él. Si el número de máquinas virtuales que se va a volver a proteger en el destino maestro local suma más de 60 discos, las reprotecciones en el destino maestro empezarán a generar errores. Asegúrese de que tiene suficientes ranuras de disco de destino maestro o implemente servidores de destino maestro adicionales.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Pasos para la reprotección

> [!NOTE]
> Después del arranque de una máquina virtual en Azure, el agente tarda algún tiempo en registrarse de nuevo en el servidor de configuración (hasta quince minutos). Durante este tiempo se producen errores de reprotección y un mensaje de error indica que el agente no está instalado. Espere unos minutos y vuelva a intentar la reprotección.



1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que ha sido objeto de la conmutación por error y seleccione **Reproteger**. También puede hacer clic en la máquina y seleccionar **Reproteger** con los botones de comando.
2. En la hoja, observe que la dirección de protección **De Azure a local** ya está seleccionada.
3. En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos.
4. En **Almacén de datos**, seleccione aquel en el que quiera recuperar los discos en local. Se usa esta opción cuando se elimina la máquina virtual local y es necesario crear discos. Esta opción se omite si los discos ya existen, pero debe especificar un valor.
5. Elija la unidad de retención.
6. La directiva de conmutación por recuperación se selecciona automáticamente.
7. Haga clic en **Aceptar** para comenzar con la reprotección. Comienza un trabajo para replicar la máquina virtual desde Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** .

Si quiere recuperar en una ubicación alternativa (cuando se elimine la máquina virtual local), seleccione la unidad de retención y el almacén de datos configurados para el servidor de destino maestro. Cuando se conmuta por recuperación en el sitio local, las máquinas virtuales VMware del plan de protección de conmutación por recuperación usan el mismo almacén de datos que el servidor de destino maestro. Entonces se crea una nueva máquina virtual en vCenter.

Si quiere recuperar la máquina virtual en Azure en una máquina virtual local existente, monte los almacenes de datos de la máquina virtual local con acceso de lectura y escritura en el host ESXi del servidor de destino maestro.
    ![Cuadro de diálogo Reproteger](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

También puede volver a proteger en el nivel de plan de recuperación. Solo se puede reproteger un grupo de replicación mediante un plan de recuperación. Cuando reproteja mediante un plan de recuperación, tiene que proporcionar los valores para cada máquina protegida.

> [!NOTE]
> Use el mismo servidor de destino maestro para reproteger un grupo de replicación. Si usa otro servidor de destino maestro para reproteger un grupo de replicación, el servidor no puede proporcionar un momento dado común.

> [!NOTE]
> La máquina virtual local se apaga durante la reprotección. Esto ayuda a garantizar la coherencia de datos durante la replicación. No encienda la máquina virtual una vez finalizada la reprotección.

Cuando la reprotección se haya realizado correctamente, la máquina virtual entrará en un estado protegido.

## <a name="next-steps"></a>Pasos siguientes

Una vez que la máquina virtual haya entrado en un estado protegido, puede [iniciar una conmutación por recuperación](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

La conmutación por recuperación apagará la máquina virtual en Azure y arrancará la máquina virtual local. Debe prever cierto tiempo de inactividad de la aplicación. Elija una hora para la conmutación por recuperación a la que la aplicación pueda tolerar tiempo de inactividad.

## <a name="common-problems"></a>Problemas comunes

* Si ha usado una plantilla para crear las máquinas virtuales, asegúrese de que cada una tenga su propio UUID para los discos. Si el UUID de la máquina virtual local entra en conflicto con el del destino maestro porque ambos se han creado a partir de la misma plantilla, se produce un error de reprotección. Implemente otro destino maestro que no se haya creado a partir de la misma plantilla.

* Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, la protección se ejecutará correctamente y la conmutación por error funcionará. Durante la reprotección, se produce un error de conmutación por error porque no se pueden detectar los almacenes de datos. Un síntoma es que los almacenes de datos no aparecen durante la reprotección. Para solucionar este problema, puede actualizar las credenciales de vCenter con una cuenta adecuada que tenga permisos y volver a intentar el trabajo. Para obtener más información, consulte [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* Al conmutar por recuperación una máquina virtual Linux y ejecutarla en local, puede ver que el paquete del administrador de red se ha desinstalado de la máquina. Esto se debe a que el paquete del administrador de red se elimina cuando se recupera la máquina virtual en Azure.

* Cuando una máquina virtual Linux se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP desde DHCP. Al conmutar por error en local, la máquina virtual sigue usando DHCP para obtener la dirección IP. Inicie sesión manualmente en la máquina y vuelva a establecer la dirección IP en una dirección estática si es necesario. Una máquina virtual Windows puede adquirir su dirección IP estática de nuevo.

* Si usa las ediciones gratuitas de ESXi 5.5 o vSphere 6 Hypervisor, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Para habilitar la conmutación por recuperación, tendrá que actualizar los programas con una licencia de evaluación.

* Si no puede ponerse en contacto con el servidor de configuración desde el servidor de procesos, use Telnet para comprobar la conectividad con el servidor de configuración en el puerto 443. También puede tratar de hacer ping al servidor de configuración desde el servidor de procesos. Un servidor de procesos debe tener también un latido cuando esté conectado al servidor de configuración.

* Si está intentando conmutar por recuperación en un vCenter alternativo, asegúrese de que se detectan tanto el nuevo vCenter como el servidor de destino maestro. Un síntoma típico es que los almacenes de datos no están accesibles ni visibles en el cuadro de diálogo de **Reprotect** (Reproteger).

* No se puede llevar a cabo la conmutación por recuperación de un servidor Windows Server 2008 R2 SP1 que esté protegido como servidor local físico desde Azure a un sitio local.

### <a name="common-error-codes"></a>Códigos de error comunes

#### <a name="error-code-95226"></a>Código de error 95226

*No se pudo aplicar la reprotección porque la máquina virtual de Azure no pudo acceder al servidor de configuración local.*

Aparece cuando 
1. La máquina virtual de Azure no ha podido acceder al servidor de configuración local y, por tanto, no se ha podido detectar y registrar en el servidor de configuración. 
2. Es posible que el servicio InMage Scout Application en la máquina virtual de Azure que debe estar en ejecución para poder comunicarse con al servidor de configuración local no ejecute la conmutación por error posterior.

Para resolver este problema
1. Es preciso asegurarse de que la red de la máquina virtual de Azure se configura de modo que la máquina virtual pueden comunicarse con el servidor de configuración local. Para ello, configure una VPN de sitio a sitio en el centro de datos local o una conexión de ExpressRoute con pares privados en la red virtual de la máquina virtual de Azure. 
2. Si ya tiene una red configurada de modo que la máquina virtual de Azure pueda comunicarse con el servidor de configuración local, inicie sesión en la máquina virtual y marque "Servicio InMage Scout Application". Si observa que el servicio InMage Scout Application no se ejecuta, inícielo manualmente y asegúrese de que el tipo de inicio del servicio está establecido Automático.

### <a name="error-code-78052"></a>Código de error 78052
No se puede realizar la reprotección y aparece el mensaje de error: *No se pudo completar la protección de la máquina virtual.*

Dicho error puede aparecer por dos motivos
1. La máquina virtual que va a volver a proteger tiene Windows Server 2016. Actualmente este sistema operativo no es compatible con la conmutación por recuperación, pero lo será muy pronto.
2. Ya existe una máquina virtual con el mismo nombre en el servidor de destino maestro al que se va a realizar la conmutación por recuperación.

Para resolver este problema puede seleccionar otro servidor de destino de un host diferente, con el fin de que la reprotección cree la máquina en otro host, donde los nombres no entren en conflicto. También puede mover el servidor de destino maestro con vMotion a un host en el que no se produzca la colisión de nombres. Si la máquina virtual existente es una máquina aislada, puede cambiarle el nombre para que se pueda crear la nueva máquina virtual en el mismo host ESXi.

### <a name="error-code-78093"></a>Código de error 78093

*La VM no se está ejecutando, no responde o no está accesible.*

Para volver a proteger una máquina virtual en la que se ha realizado la conmutación por error en la ubicación local, la máquina virtual de Azure debe estar en ejecución. Esto es para que el servicio de movilidad se registre con el servidor de configuración local y puede empezar a realizar la replicación mediante la comunicación con el servidor de procesos. Si el equipo está en una red incorrecta o no se ejecuta (bloqueado o apagado), el servidor de configuración no puede acceder al servicio de movilidad de la máquina virtual para iniciar la reprotección. Puede reiniciar la máquina virtual para que pueda volver a la comunicación con el entorno local. Reinicie el trabajo de reprotección después de iniciar la máquina virtual de Azure

### <a name="error-code-8061"></a>Código de error 8061

*El almacén de datos no es accesible desde el host ESXi.*

Vea los [requisitos previos del destino principal](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) y los [almacenes de datos de soporte](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) para realizar la conmutación por recuperación.

