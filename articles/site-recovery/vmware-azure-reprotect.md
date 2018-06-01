---
title: Reprotección de máquinas virtuales de Azure a un sitio local | Microsoft Docs
description: Después de que se produzca una conmutación por error de máquinas virtuales en Azure, puede iniciar una conmutación por recuperación para volver a poner las máquinas virtuales en el entorno local. Aprenda a reproteger antes de una conmutación por recuperación.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: 0946d5234292cfb69a7e9b5bc7846e6acf94dff4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072630"
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Reprotección de máquinas de Azure a un sitio local

Tras la [conmutación por error](site-recovery-failover.md) de máquinas virtuales de VMware locales o de servidores físicos a Azure, el primer paso en la conmutación por recuperación en el sitio local es reproteger las máquinas virtuales de Azure que se crearon durante la conmutación por error. En este artículo se describe cómo hacerlo. 

El siguiente vídeo es una breve introducción a la conmutación por error de Azure a un sitio local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Antes de comenzar

Si ha usado una plantilla para crear las máquinas virtuales, asegúrese de que cada una tenga su propio UUID para los discos. Si el UUID de la máquina virtual local entra en conflicto con el del destino maestro porque ambos se han creado a partir de la misma plantilla, se produce un error de reprotección. Implemente otro destino maestro que no se haya creado a partir de la misma plantilla.
- Si está intentando conmutar por recuperación en un vCenter alternativo, asegúrese de que se detectan tanto el nuevo vCenter como el servidor de destino maestro. Un síntoma típico es que los almacenes de datos no están accesibles ni visibles en el cuadro de diálogo de **Reprotect** (Reproteger).
- Para volver a replicar en el entorno local, necesitará una directiva de conmutación por recuperación. Esta directiva se crea automáticamente al crear una directiva de avance. Observe lo siguiente:
    - Esta directiva se asocia automáticamente con el servidor de configuración durante la creación.
    - Además, no es editable.
    - Los valores establecidos de la directiva son los siguientes: umbral de RPO = 15 min; retención de punto de recuperación = 24 horas; y frecuencia de instantáneas de coherencia de la aplicación = 60 min.
- Durante la reprotección y la conmutación por recuperación, el servidor de configuración local debe estar en ejecución y conectado.
- Si una instancia de vCenter Server administra las máquinas virtuales a las que quiere conmutar por recuperación, asegúrese de tener los [permisos necesarios](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) para la detección de máquinas virtuales en las instancias de vCenter Server.
- Antes de realizar la reprotección, elimine las instantáneas en el servidor de destino maestro. Si existen instantáneas en el destino maestro local o la máquina virtual, la reprotección no se realiza correctamente. Las instantáneas de la máquina virtual se combinan automáticamente durante un trabajo de reprotección.
- Todas las máquinas virtuales de un grupo de replicación deben ser del mismo tipo de sistema operativo (todas Windows o todas Linux). Un grupo de replicación con sistemas operativos mezclados no se admite actualmente para reprotección y conmutación por recuperación a local. Esto se debe a que el destino maestro debe ser del mismo sistema operativo que la máquina virtual y todas las máquinas virtuales de un grupo de replicación deben tener el mismo destino maestro. 
- Se necesita un servidor de configuración en local al conmutar por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. En caso contrario, la conmutación por recuperación no será correcta. Asegúrese de realizar una copia de seguridad programada periódica del servidor de configuración. En caso de desastre, restaure el servidor con la misma dirección IP para que la conmutación por recuperación funcione.
- Para replicar los datos, se requiere una VPN de sitio a sitio durante la reprotección y la conmutación por recuperación. Proporcione la red para que las máquinas virtuales que hayan conmutado por error en Azure puedan alcanzar (ping) el servidor de configuración local. También podría implementar un servidor de procesos en la red de Azure de la máquina virtual conmutada por error. Este servidor de procesos también debería ser capaz de comunicarse con el servidor de configuración local.
- Asegúrese de abrir los puertos siguientes durante la conmutación por error y la conmutación por recuperación.

    ![Puertos para conmutación por error y conmutación por recuperación](./media/vmware-azure-reprotect/failover-failback.png)

- Puede leer todos los requisitos previos sobre puertos y creación de listas blancas de direcciones URL [aquí](vmware-azure-deploy-configuration-server.md#prerequisites)

## <a name="deploy-a-process-server-in-azure"></a>Implementación de un servidor de procesos en Azure

Puede que necesite un servidor de procesos en Azure antes de conmutar por recuperación a su sitio local:
- El servidor de procesos recibe datos de la máquina virtual protegida en Azure y los envía al sitio local.
- Se requiere una red de baja latencia entre el servidor de procesos y la máquina virtual protegida. En general, al decidir si necesita un servidor de proceso de Azure debe tener en cuenta la latencia:
    - Si tiene configurada una conexión ExpressRoute, puede usar un servidor de procesos local para enviar datos, ya que la latencia entre la máquina virtual y el servidor de procesos es baja.
    - Pero si solo dispone de una VPN S2S, se recomienda implementar el servidor de procesos en Azure.
    - Se recomienda usar un servidor de procesos basado en Azure durante la conmutación por recuperación. El rendimiento de replicación es mayor si el servidor de procesos está más próximo a la máquina virtual que se está replicando (la máquina conmutada por error en Azure). Como prueba de concepto, puede usar el servidor de procesos local junto con ExpressRoute con emparejamiento privado.

Realice la implementación de la manera siguiente:

1. Si necesita implementar un servidor de procesos de Azure, siga [estas instrucciones](vmware-azure-set-up-process-server-azure.md).
2. Las máquinas virtuales de Azure envían los datos de replicación al servidor de procesos. Configure redes para que las máquinas virtuales de Azure puedan establecer comunicación con el servidor de procesos.
3. Recuerde que solo se realizará la replicación de Azure a local a través de la VPN S2S o el emparejamiento privado de la red ExpressRoute. Asegúrese de que hay suficiente ancho de banda disponible a través de ese canal de red.

## <a name="deploy-a-separate-master-target-server"></a>Implementación de un servidor de destino maestro independiente

El servidor de destino maestro recibe los datos de conmutación por recuperación. De forma predeterminada, el servidor de destino maestro se ejecuta en el servidor de configuración local. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino principal distinto para esta operación. Aquí se muestra cómo crear uno:

    * [Cree un servidor de destino maestro de Linux](vmware-azure-install-linux-master-target.md) para la conmutación por recuperación de las máquinas virtuales Linux. Este es un paso necesario.
    * Opcionalmente, puede crear un servidor de destino maestro independiente para la conmutación por recuperación de las máquinas virtuales Windows. Para ello, ejecute de nuevo la instalación unificada y seleccione crear un servidor de destino maestro. [Más información](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

Después de crear un servidor de destino maestro, haga lo siguiente:

- Si la máquina virtual está presente en local en el servidor vCenter, el servidor de destino maestro necesita acceso al VMDK de la máquina virtual local. Se necesita acceso para escribir los datos replicados en los discos de la máquina virtual. Asegúrese de que el almacén de datos de la máquina virtual local esté montado en el host del destino maestro con acceso de lectura y escritura.
- Si la máquina virtual no está presente en local en el servidor vCenter, el servicio Site Recovery tiene que crear una nueva máquina virtual durante la reprotección. Esta máquina virtual se crea en el host ESX en el que se crea el destino maestro. Elija con cuidado el host ESX para que se cree la máquina virtual de conmutación por recuperación en el host que desee.
- No se puede usar Storage vMotion en el servidor de destino maestro*. Esto puede provocar un error en la conmutación por recuperación. La máquina virtual no se puede iniciar porque los discos no están disponibles para ella. Para evitar este problema, excluya los servidores de destino maestros de la lista de vMotion.
- Si un destino maestro se somete a una tarea de Storage vMotion después de la reprotección, los discos protegidos de las máquinas virtuales asociados al destino maestro migran al destino de la tarea de vMotion. Si intenta conmutar por recuperación después de esto, se produce un error de desasociación de los discos porque estos no se encuentran. Entonces se vuelve difícil encontrar los discos en las cuentas de almacenamiento. Debe encontrarlos manualmente y asociarlos a la máquina virtual. Después de eso se puede arrancar la máquina virtual local.
- Agregue una unidad de retención al servidor de destino maestro Windows existente. Agregue un nuevo disco y formatee la unidad. La unidad de retención se utiliza para detener los momentos dados cuando la máquina virtual se replica de vuelta en el sitio local. Estos son algunos criterios de una unidad de retención. Sin estos criterios, la unidad no se mostrará para el servidor de destino maestro.
    - El volumen no se usa para ningún otro propósito, como un destino de replicación.
    - El volumen no está en modo de bloqueo.
    - El volumen no es un volumen de caché. La instalación del destino maestro no debe existir en dicho volumen. El volumen de instalación personalizado del servidor de procesos y el destino maestro no es apto para un volumen de retención. Cuando el servidor de procesos y el destino maestro se instalan en un volumen, este es un volumen de memoria caché del destino maestro.
    - El tipo de sistema de archivos del volumen no es FAT ni FAT32.
    - La capacidad del volumen es distinta de cero.
    - El volumen de retención predeterminado para Windows es el volumen R.
    - El volumen de retención predeterminado para Linux es /mnt/retention.
- Si está usando una máquina existente de servidor de procesos o de servidor de configuración o una escala o una máquina de servidor de procesos o de servidor de destino maestro, tiene que agregar una nueva unidad. La nueva unidad debe cumplir los requisitos anteriores. Si la unidad de retención no está presente, no aparece en la lista de selección desplegable en el portal. Después de agregar una unidad al destino maestro local, pasa un máximo de quince minutos hasta que la unidad aparece en la selección en el portal. También puede actualizar el servidor de configuración si la unidad no aparece pasados quince minutos.
- Instale las herramientas de VMware o de open-vm-tools en el servidor de destino maestro. Sin las herramientas, los almacenes de datos del host ESXi del destino maestro no se pueden detectar.
- Establezca `disk.EnableUUID=true` en los parámetros de configuración de la máquina virtual del destino maestro en VMware. Si la fila no existe, agréguela. Esta configuración es necesaria a fin de proporcionar un UUID uniforme al disco de máquina virtual (VMDK) para que se monte correctamente.
- El host ESX en el que se creó el destino maestro debe tener al menos un almacén de datos VMFS asociado. Si no hay ninguno, la entrada **Almacén de datos** de la página reprotegida estará vacía y no podrá continuar.
- El servidor de destino maestro no puede tener instantáneas en los discos. Si hay instantáneas, se produce un error en la reprotección y la conmutación por recuperación.
- El destino maestro no puede tener ninguna controladora SCSI paravirtual. La controladora solo puede ser una controladora de LSI Logic. Sin ella, se produce un error en la reprotección.
- En cualquier instancia dada, el destino maestro puede tener 60 discos como máximo conectados a él. Si el número de máquinas virtuales que se va a volver a proteger en el destino maestro local suma más de 60 discos, las reprotecciones en el destino maestro empezarán a generar errores. Asegúrese de que tiene suficientes ranuras de disco de destino maestro o implemente servidores de destino maestro adicionales.
    

## <a name="enable-reprotection"></a>Habilitación de la reprotección

Después del arranque de una máquina virtual en Azure, el agente tarda algún tiempo en registrarse de nuevo en el servidor de configuración (hasta quince minutos). Durante este tiempo, no podrá realizar la reprotección y aparece un mensaje de error para indicar que el agente no está instalado. Si esto sucede, espere unos minutos y vuelva a intentar la reprotección.


1. En **Almacén** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que ha sido objeto de la conmutación por error y seleccione **Reproteger**. También puede hacer clic en la máquina y seleccionar **Reproteger** con los botones de comando.
2. Compruebe que la dirección de la reprotección, **Azure a local**, ya esté seleccionada.
3. En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos.  
4. En **Almacén de datos**, seleccione aquel en el que quiera recuperar los discos en local. Se usa esta opción cuando se elimina la máquina virtual local y es necesario crear discos. Esta opción se omite si los discos ya existen, pero debe especificar un valor.
5. Elija la unidad de retención.
6. La directiva de conmutación por recuperación se selecciona automáticamente.
7. Haga clic en **Aceptar** para comenzar con la reprotección. Comienza un trabajo para replicar la máquina virtual desde Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** . Cuando la reprotección se haya realizado correctamente, la máquina virtual entrará en un estado protegido.

Observe lo siguiente:
- Si quiere recuperar en una ubicación alternativa (cuando se elimine la máquina virtual local), seleccione la unidad de retención y el almacén de datos configurados para el servidor de destino maestro. Cuando se conmuta por recuperación en el sitio local, las máquinas virtuales VMware del plan de protección de conmutación por recuperación usan el mismo almacén de datos que el servidor de destino maestro. Entonces se crea una nueva máquina virtual en vCenter.
- Si quiere recuperar la máquina virtual en Azure en una máquina virtual local existente, monte los almacenes de datos de la máquina virtual local con acceso de lectura y escritura en el host ESXi del servidor de destino maestro.
    ![Cuadro de diálogo Reproteger](./media/vmware-azure-reprotect/reprotectinputs.png)

- También puede volver a proteger en el nivel de plan de recuperación. Solo se puede reproteger un grupo de replicación mediante un plan de recuperación. Cuando reproteja mediante un plan de recuperación, tiene que proporcionar los valores para cada máquina protegida.
- Use el mismo servidor de destino maestro para reproteger un grupo de replicación. Si usa otro servidor de destino maestro para reproteger un grupo de replicación, el servidor no puede proporcionar un momento dado común.
- La máquina virtual local se apaga durante la reprotección. Esto ayuda a garantizar la coherencia de datos durante la replicación. No encienda la máquina virtual una vez finalizada la reprotección.



## <a name="common-issues"></a>Problemas comunes


- Actualmente, Site Recovery admite la conmutación por recuperación únicamente a un almacén de datos VMFS (sistema de archivos de máquina virtual) o vSAN. No se admiten los almacenes de datos NFS. Debido a esta limitación, la entrada de selección de almacén de datos en la pantalla de reprotección está vacía en el caso de los almacenes de datos NFS o muestra el almacén de datos vSAN, pero produce un error durante el trabajo. Si piensa conmutar por recuperación, puede crear un almacén de datos VMFS local y conmutar por recuperación en él. Esta conmutación por recuperación ocasionará una descarga completa del VMDK.
- Si realiza la detección de usuarios de solo lectura de vCenter y protege las máquinas virtuales, la protección se ejecutará correctamente y la conmutación por error funcionará. Durante la reprotección, se produce un error de conmutación por error porque no se pueden detectar los almacenes de datos. Un síntoma es que los almacenes de datos no aparecen durante la reprotección. Para solucionar este problema, puede actualizar las credenciales de vCenter con una cuenta adecuada que tenga permisos y volver a intentar el trabajo. 
- Al conmutar por recuperación una máquina virtual Linux y ejecutarla en local, puede ver que el paquete del administrador de red se ha desinstalado de la máquina. Esto se debe a que el paquete del administrador de red se elimina cuando se recupera la máquina virtual en Azure.
- Cuando una máquina virtual Linux se configura con una dirección IP estática y se conmuta por error a Azure, se adquiere la dirección IP desde DHCP. Al conmutar por error en local, la máquina virtual sigue usando DHCP para obtener la dirección IP. Inicie sesión manualmente en la máquina y vuelva a establecer la dirección IP en una dirección estática si es necesario. Una máquina virtual Windows puede adquirir su dirección IP estática de nuevo.
- Si usa las ediciones gratuitas de ESXi 5.5 o vSphere 6 Hypervisor, la conmutación por error se llevará a cabo correctamente, pero la conmutación por recuperación, no. Para habilitar la conmutación por recuperación, tendrá que actualizar los programas con una licencia de evaluación.
- Si no puede ponerse en contacto con el servidor de configuración desde el servidor de procesos, use Telnet para comprobar la conectividad con el servidor de configuración en el puerto 443. También puede tratar de hacer ping al servidor de configuración desde el servidor de procesos. Un servidor de procesos debe tener también un latido cuando esté conectado al servidor de configuración.
- No se puede llevar a cabo la conmutación por recuperación de un servidor Windows Server 2008 R2 SP1 que esté protegido como servidor local físico desde Azure a un sitio local.
- No podrá conmutar por recuperación en las siguientes circunstancias:
    - Ha migrado máquinas a Azure. [Más información](migrate-overview.md#what-do-we-mean-by-migration).
    - Ha movido una máquina virtual a otro grupo de recursos.
    - Ha eliminado la máquina virtual de Azure.
    - Ha deshabilitado la protección de la máquina virtual.
    - Ha creado manualmente la máquina virtual en Azure. La máquina se debe haber protegido inicialmente en local y haber conmutador por error a Azure antes de la reprotección.
    - Solo puede conmutar por recuperación a un host de ESXi. No puede conmutar por recuperación máquinas virtuales de VMware o servidores físicos a hosts de Hyper-V, máquinas físicas o estaciones de trabajo de VMware.


## <a name="next-steps"></a>Pasos siguientes

Una vez que la máquina virtual haya entrado en un estado protegido, puede [iniciar una conmutación por recuperación](vmware-azure-failback.md). La conmutación por recuperación apagará la máquina virtual en Azure y arrancará la máquina virtual local. Debe prever cierto tiempo de inactividad de la aplicación. Elija una hora para la conmutación por recuperación a la que la aplicación pueda tolerar tiempo de inactividad.

