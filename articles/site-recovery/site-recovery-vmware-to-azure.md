---
title: "Replicación de máquinas virtuales de VMware en Azure | Microsoft Docs"
description: "Resume los pasos necesarios para replicar cargas de trabajo que se ejecutan en máquinas virtuales de VMware en almacenamiento de Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: d811cdabe35f28ab8f2496b08c959107c10ef1be
ms.lasthandoff: 03/14/2017


---
# <a name="replicate-vmware-virtual-machines-to-azure-with--site-recovery"></a>Replicación de máquinas virtuales de VMware en Azure con Site Recovery

> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-vmware-to-azure.md)
> * [Azure clásico](site-recovery-vmware-to-azure-classic.md)


En este artículo, se describe cómo replicar máquinas virtuales de VMware locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Si quiere migrar máquinas virtuales de VMware a Azure (solo recuperación por error), lea [este artículo](site-recovery-migrate-to-azure.md) para obtener más información.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="deployment-steps"></a>Pasos de implementación

Esto es lo que debe hacer:

1. Compruebe los requisitos previos y las limitaciones.
2. Configure las cuentas de red y almacenamiento de Azure.
3. Prepare la máquina local que desee implementar como servidor de configuración.
4. Prepare las cuentas de VMware que se usarán para la detección automática de máquinas virtuales y, opcionalmente, para la instalación de inserción del servicio de movilidad.
4. Cree un almacén de Recovery Services. El almacén contiene valores de configuración y organiza la replicación.
5. Especifique la configuración de origen, destino y replicación.
6. Implemente el servicio de movilidad en las máquinas virtuales que desee replicar.
7. Habilite la replicación para las máquinas virtuales.
7. Ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.

## <a name="prerequisites"></a>Requisitos previos

**Requisito de compatibilidad** | **Detalles**
--- | ---
**Las tablas de Azure** | Aprenda acerca de los [requisitos de Azure](site-recovery-prereq.md#azure-requirements)
**Servidor de configuración local** | Necesita una máquina virtual de VMware con Windows Server 2012 R2 o posterior. Configure este servidor durante la implementación de Site Recovery.<br/><br/> De forma predeterminada, el servidor de procesos y el servidor de destino maestro también están instalados en esta máquina virtual. Al escalar una verticalmente, podría necesitar un servidor de procesos independiente, con los mismos requisitos que el servidor de configuración.<br/><br/> Aprenda más sobre estos componentes [aquí](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**Servidores de VMware locales** | Uno o más servidores de VMware vSphere, con 6.0, 5.5 o 5.1 y las últimas actualizaciones. Los servidores se deberían encontrar en la misma red que el servidor de configuración (o en un servidor de procesos independiente).<br/><br/> Se recomienda un servidor vCenter para administrar hosts, con 6.0 o 5.5 con las últimas actualizaciones. Solo se admiten las características que estén disponibles en 5.5 cuando se implementa la versión 6.0.
**Máquinas virtuales locales** | Las máquinas virtuales que desee replicar deben ejecutar un [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Las máquinas virtuales deberían ejecutar las herramientas de VMware.
**URLs** | El servidor de configuración necesita acceso a estas direcciones URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.<br/></br> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/></br> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).<br/><br/> Permita esta dirección URL para la descarga de MySQL: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Servicio de movilidad** | Instalado en cada máquina virtual replicada.




## <a name="limitations"></a>Limitaciones

**Limitación** | **Detalles**
--- | ---
**Las tablas de Azure** | Las cuentas de almacenamiento y de red deben estar en la misma región que el almacén.<br/><br/> Si usa una cuenta de Premium Storage, también necesita una cuenta de almacenamiento estándar para almacenar los registros de replicación.<br/><br/> No se puede replicar en cuentas premium en el centro y sur de la India.
**Servidor de configuración local** | El tipo de adaptador de máquina virtual de VMware debe ser VMXNET3. Si no lo es, [instale esta actualización](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Se debe instalar vSphere PowerCLI 6.0.<br/><br> La máquina no debe ser un controlador de dominio. La máquina debe tener una dirección IP estática.<br/><br/> El nombre de host debe tener 15 caracteres o menos, y el sistema operativo debe estar en inglés.
**VMware** | Solo se admiten las características de la versión 5.5 en vCenter 6.0. Site Recovery no admite las nuevas características de vCenter y vSphere 6.0, como Cross vCenter vMotion, volúmenes virtuales y DRS de almacenamiento.
**VM** | Compruebe las [limitaciones de máquinas virtuales de Azure](site-recovery-prereq.md#azure-requirements).<br/><br/> No se pueden replicar máquinas virtuales con discos cifrados ni tampoco con arranque UEFI/EFI.<br/><br> No se admiten los clústeres de disco compartido. Si la máquina virtual de origen tiene formación de equipos NIC, se convierte en una sola NIC después de la conmutación por error.<br/><br/> Si las máquinas virtuales tienen un disco iSCSI, Site Recovery lo convierte en un archivo de disco duro virtual después de la conmutación por error. Si la máquina virtual de Azure puede acceder al destino iSCSI, se conecta a él y ve tanto el destino iSCSI como el disco duro virtual. Si sucede esto, desconecte el destino iSCSI.<br/><br/> Si desea habilitar la coherencia de múltiples máquinas virtuales, lo cual permite que las máquinas virtuales que ejecuten la misma carga de trabajo se recuperen juntas en un punto de datos coherente, abra el puerto 20004 en la máquina virtual.<br/><br/> Windows debe instalarse en la unidad C. El disco del sistema operativo debe ser básico y no dinámico. El disco de datos puede ser dinámico.<br/><br/> Los archivos /etc/hosts de Linux de las máquinas virtuales deben contener entradas que asignen el nombre de host local a direcciones IP asociadas con todos los adaptadores de red. El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema y los nombres de archivo (/etc/, /usr) deben estar en inglés únicamente.<br/><br/> Se admiten tipos específicos de [almacenamiento Linux](site-recovery-support-matrix-to-azure.md#support-for-storage).<br/><br/>Cree o establezca **disk.enableUUID=true** en la configuración de máquina virtual. Esto proporciona un UUID coherente para el VMDK, para que se monte correctamente, y se asegura de que solamente se transfieran los cambios delta de vuelta a la máquina local durante la conmutación por recuperación, sin replicación completa.

## <a name="set-up-azure"></a>Configuración de Azure

1. [Configure una red de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - Las máquinas virtuales de Azure se colocarán en esta red cuando se creen después de la conmutación por error.
    - Puede configurar una red en el modo de [Resource Manager](../resource-manager-deployment-model.md) o en el clásico.

2. Configure una [cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account) para los datos replicados.
    - La cuenta puede ser estándar o [premium](../storage/storage-premium-storage.md).
    - Puede configurar una red en el modo de Resource Manager o en el clásico.

3. [Prepare una cuenta](#prepare-for-automatic-discovery-and-push-installation) en el servidor vCenter o en los hosts de vSphere para que Site Recovery pueda detectar automáticamente máquinas virtuales de VMware.

## <a name="prepare-the-configuration-server"></a>Preparar el servidor de configuración

1. Instale Windows Server 2012 R2 o posterior en una máquina virtual de VMware.
2. Asegúrese de que la máquina virtual tenga acceso a las direcciones URL que aparecen en los [requisitos previos](#prerequisites).
3. Instale [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0).


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>Preparación de la detección automática y la instalación de inserción

- **Prepare una cuenta para la detección automática**: el servidor de procesos de Site Recovery detecta automáticamente máquinas virtuales. Para ello, Site Recovery necesita credenciales que puedan acceder a servidores vCenter y hosts de vSphere ESXi.

    1. Para usar una cuenta dedicada, cree un rol (en el nivel de vCenter, con estos [permisos](#vmware-account-permissions)). Asígnele un nombre como **Azure_Site_Recovery**.
    2. Después, cree un usuario en el servidor vCenter o en el host de vSphere y asigne el rol al usuario. Especifique esta cuenta de usuario durante la implementación de Site Recovery.

- **Prepare una cuenta para insertar el servicio de movilidad**: si desea insertar el servicio de movilidad en máquinas virtuales, necesita una cuenta que el servidor de procesos pueda usar para acceder a la máquina virtual. La cuenta solo se utiliza para la instalación de inserción. Puede usar una cuenta local o de dominio:

    - Para Windows, si no utiliza una cuenta de dominio, debe deshabilitar el control de acceso de usuario remoto en la máquina local. Para hacerlo, en el Registro, en **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, agregue la entrada DWORD **LocalAccountTokenFilterPolicy** con un valor de 1.
    - Si desea agregar la entrada del Registro de Windows desde una CLI, escriba:   ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - Para Linux, la cuenta debe ser una raíz en el servidor Linux de origen.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>Selección del objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.

1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. En **Objetivo de protección**, seleccione **To Azure (En Azure)** > **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor).

    ![Elegir objetivos](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el servidor de configuración, regístrelo en el almacén y detecte máquinas virtuales.

1. Haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Origen**.
2. Si no tiene un servidor de configuración, haga clic en **+Servidor de configuración**.

    ![Configurar origen](./media/site-recovery-vmware-to-azure/set-source1.png)
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración** en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

   ![Configurar origen](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Ejecución de la instalación unificada de Site Recovery

Haga lo siguiente antes de empezar y, después, ejecute la instalación unificada para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro.
    - Vea un vídeo introductorio rápido

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - En la máquina virtual del servidor de configuración, asegúrese de que el reloj del sistema está sincronizado con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deben ser iguales. Si hay una diferencia de 15 minutos, antes o después, se podría producir un error en la instalación.
    - Ejecute la instalación como administrador Local en la máquina virtual del servidor de configuración.
    - Asegúrese de que TLS 1.0 está habilitada en la máquina virtual.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración también se puede instalar [desde la línea de comandos](http://aka.ms/installconfigsrv).



### <a name="add-the-account-for-automatic-discovery"></a>Incorporación de la cuenta para detección automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="connect-to-vmware-servers"></a>Conexión a servidores de VMware

Conéctese a hosts de vSphere ESXi o servidores vCenter para detectar máquinas virtuales de VMware.

- Si agrega el servidor vCenter o los hosts de vSphere con una cuenta sin privilegios de administrador en el servidor, la cuenta necesita que se habiliten estos privilegios:
    - Centro de datos, Almacén de datos, Carpeta, Host, Red, Recurso, Máquina virtual, vSphere Distributed Switch.
    - El servidor vCenter necesita el permiso Vistas de almacenamiento.
- Cuando agregue servidores de VMware, pueden tardar 15 minutos o más en aparecer en el portal.
Para permitir que Azure Site Recovery detecte las máquinas virtuales que se ejecutan en el entorno local, debe conectar los hosts ESXi de vSphere o servidor vCenter de VMware con Site Recovery.

Seleccione **+vCenter** para comenzar a conectar un servidor vCenter de VMware o un host ESXi de vSphere de VMware.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

Site Recovery se conecta a los servidores de VMware con la configuración especificada y detecta máquinas virtuales.

## <a name="set-up-the-target"></a>Configuración del destino

Antes de configurar el entorno de destino, compruebe que tenga una [cuenta de almacenamiento y una red de Azure](#set-up-azure)

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique si el modelo de implementación de destino está basado en Resource Manager o si es el clásico.
3. Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

   ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)
4. Si no ha creado una cuenta de almacenamiento ni una red, haga clic en **+Cuenta de almacenamiento** o **+Red** para crear una cuenta o red de Resource Manager en línea.

## <a name="set-up-replication-settings"></a>Configuración de las opciones de replicación

Vea un vídeo introductorio rápido antes de empezar:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Para crear una directiva de replicación, haga clic en **Site Recovery Infrastructure (Infraestructura de Site Recovery)** > **Directivas de replicación** > **+Directiva de replicación**.
2. En **Crear directiva de replicación**, especifique un nombre de directiva.
3. En **Umbral de RPO**, especifique el límite de RPO. Este valor especifica la frecuencia con que se crean puntos de recuperación de datos. Se genera una alerta cuando la replicación continua supera este límite.
4. En **Retención de punto de recuperación**, especifique (en horas) la duración del período de retención para cada punto de recuperación. Las máquinas virtuales replicadas se pueden recuperar a cualquier momento de un período. Se admite una retención de hasta 24 horas para máquinas replicadas en Premium Storage y 72 horas para almacenamiento estándar.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Haga clic en **Aceptar** para crear la directiva.

    ![Directiva de replicación](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. Cuando se crea una nueva directiva, esta se asocia automáticamente con el servidor de configuración. De forma predeterminada, se crea automáticamente una directiva correspondiente para la conmutación por recuperación. Por ejemplo, si la directiva de replicación es **rep-policy**, la directiva de conmutación por recuperación será **rep-policy-failback**. Esta directiva no se usa hasta que se inicie una conmutación por recuperación desde Azure.  



## <a name="plan-capacity"></a>Planeamiento de la capacidad

1. Ahora que tiene la infraestructura básica configurada, puede planear la capacidad y averiguar si necesitará recursos adicionales. [Más información](site-recovery-plan-capacity-vmware.md).
2. Seleccione **Sí** en **¿Completó el planeamiento de capacidad?** cuando haya terminado de planear la capacidad.

   ![Planificación de capacidad](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>Preparación de las máquinas virtuales para la replicación

El servicio de movilidad se deberá instalar en todas las máquinas virtuales de VMware que desee replicar. Puede instalarlo de varias maneras:

1. Instálelo con una instalación de inserción desde el servidor de procesos. Debe preparar las máquinas virtuales para usar este método.
2. Instálelo con herramientas de implementación como System Center Configuration Manager o DSC de Azure Automation.
3.  Instálelo manualmente.

[Más información](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>Habilitar replicación

Antes de comenzar:

- Cuando agregue o modifique máquinas virtuales, se puede tardar 15 minutos o más en que los cambios surtan efecto y aparezcan en el portal.
- Puede comprobar la hora de la última detección de máquinas virtuales en **Servidores de configuración** > **Último contacto a las**.
- Para agregar máquinas virtuales sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en **Actualizar**.
* Si se prepara una máquina virtual para la instalación de inserción, el servidor de procesos instala automáticamente el servicio de movilidad cuando habilita la replicación.


### <a name="exclude-disks-from-replication"></a>Excluir discos de la replicación

De manera predeterminada, se replican todos los discos de una máquina. Puede excluir discos de la replicación. Por ejemplo, es posible que no desee replicar los discos con datos temporales, o los datos que se actualizan cada vez que una máquina o aplicación se reinicia (por ejemplo, pagefile.sys o tempdb de SQL Server).

### <a name="replicate-vms"></a>Replicación de máquinas virtuales

Vea un vídeo introductorio rápido antes de empezar

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas virtuales**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host de vSphere, o bien seleccione el host.
5. Seleccione el servidor de procesos. Si no ha creado ningún servidor de procesos adicional, este será el servidor de configuración. y, a continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.


7. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación. Si no desea usar una cuenta que ya haya configurado, puede crear una.

8. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. Si no desea usar una red existente, puede crear una.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic en cada máquina que desea replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. y, a continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que utilizará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.
11. De manera predeterminada se replican todos los discos. Haga clic en **Todos los discos** y desactive todos los discos que no desea replicar. y, a continuación, haga clic en **Aceptar**. Puede establecer otras propiedades de las máquinas virtuales más adelante.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta. Si modifica una directiva, los cambios se aplicarán a la máquina que se replica y a las nuevas máquinas.
12. Habilite la **coherencia de múltiples VM** si desea recopilar las máquinas en un grupo de replicación y especifique un nombre para el grupo. y, a continuación, haga clic en **Aceptar**. Observe lo siguiente:

    * Todas las máquinas de un grupo de replicación se replican al mismo tiempo y comparten puntos de recuperación coherentes con los bloqueos y con la aplicación cuando conmutan por error.
    * Se recomienda que recopile las máquinas virtuales y los servidores físicos juntos para que reflejen las cargas de trabajo. Habilitar la coherencia de múltiples máquinas virtuales puede afectar al rendimiento de la carga de trabajo y solo debe utilizarse si las máquinas ejecutan la misma carga de trabajo y necesita coherencia.

    ![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. Haga clic en **Enable Replication**. Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .

Después de habilitar la replicación, se instalará el servicio de movilidad si configura la instalación de inserción. Una vez que se instale el servicio de movilidad por inserción en una máquina virtual, se iniciará un trabajo de protección y se producirá un error. Debe reiniciar manualmente cada máquina después del error. Después, se vuelve a iniciar el trabajo de protección y se produce la replicación inicial.



### <a name="view-and-manage-vm-properties"></a>Visualización y administración de las propiedades de la máquina virtual

Se recomienda que compruebe las propiedades de máquina virtual y realice cualquier cambio que necesite.

1. Haga clic en **Elementos replicados** > y seleccione la máquina. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.
2. En **Propiedades** puede ver la información de replicación y conmutación por error de la máquina virtual.
3. En **Proceso y red** > **Propiedades de proceso**, puede especificar el nombre y el tamaño de destino de la máquina virtual de Azure. Modifique el nombre para que cumpla con los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) si es necesario.
4. Modifique la configuración de la red de destino, la subred y la dirección IP que se asignarán a la máquina virtual de Azure:

   - Puede establecer la dirección IP de destino.

    - Si no proporciona una dirección, la máquina conmutada por error usará DHCP.
    - Si establece una dirección que no esté disponible en el momento de la conmutación por error, esta no funcionará.
    - Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red que se vaya a usar en la prueba.

   - El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino:

     - Si el número de adaptadores de red en la máquina de origen es igual o menor que el número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
     - Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, se utilizará el tamaño máximo de destino.
     - Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador.     
   - Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.
   - Si la máquina virtual tiene varios adaptadores de red, el primero de ellos que se muestre en la lista se convertirá en el *predeterminado* en la máquina virtual de Azure.
5. En **Discos**, se ven el sistema operativo de la máquina virtual y los discos de datos que se van a replicar.

## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba


Una vez terminada la configuración, ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada. Vea un vídeo introductorio rápido antes de empezar
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. Para conmutar por error una sola máquina, en **Configuración** > **Elementos replicados**, haga clic en el icono Máquina virtual > **+Probar conmutación por error**.

    ![Probar conmutación por error](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan > **Probar conmutación por error**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).  

1. En **Probar conmutación por error**, seleccione la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

1. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo **Probar conmutación por error** en nombre de almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.

1. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure en Azure Portal > **Virtual Machines**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.

1. Si se [preparó para las conexiones después de la conmutación por error](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), debe ser capaz de conectarse a la máquina virtual de Azure.

1. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba. Así se eliminarán las máquinas virtuales que se crearon durante la conmutación por error de prueba.

[Más información](site-recovery-test-failover-to-azure.md) sobre las conmutaciones por error de prueba.


## <a name="vmware-account-permissions"></a>Permisos de cuenta de VMware

Site Recovery necesita acceso a VMware para que el servidor de procesos detecte automáticamente máquinas virtuales, así como para la conmutación por error y por recuperación de máquinas virtuales.

- **Migración**: si solo desea migrar máquinas virtuales de VMware a Azure, sin conmutarlas nunca por recuperación, puede usar una cuenta de VMware con un rol de solo lectura. Este rol puede ejecutar la conmutación por error, pero no puede apagar las máquinas de origen protegidas. Esto no es necesario para la migración.
- **Replicación/recuperación**: si desea implementar la replicación completa (replicación, conmutación por error, conmutación por recuperación), la cuenta debe poder ejecutar operaciones como la creación y eliminación de discos, el encendido de máquinas virtuales, etc.
- **Detección automática**: se requiere al menos una cuenta de solo lectura.


**Task** | **Cuenta/rol necesarios** | **Permisos** | **Detalles**
--- | --- | --- | ---
**El servidor de procesos detecta automáticamente máquinas virtuales de VMware** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).
**Conmutación por error** | Necesita al menos un usuario de solo lectura. | Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).<br/><br/> Es útil para la migración, pero no para la replicación completa, la conmutación por error o la conmutación por recuperación.
**Conmutación por error y conmutación por recuperación** | Se recomienda crear un rol (Azure_Site_Recovery) con los permisos necesarios y, después, asignarlo a un grupo o usuario de VMware. | Objeto de centro de datos –> Propagar a objeto secundario, rol = Azure_Site_Recovery<br/><br/> Almacén de datos -> Asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/> Red -> Asignación de red<br/><br/> Recursos-> Asignar máquina virtual al grupo de recursos, migrar apagado de máquina virtual, migrar máquinas virtuales encendidas<br/><br/> Tareas -> Crear tarea, actualizar tarea<br/><br/> Máquina virtual -> Configuración<br/><br/> Máquina virtual -> Interactuar -> Responder a pregunta, conexión de dispositivos, configurar soporte de CD, Configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/> Máquina virtual -> Inventario -> Crear, registrar, anular registro<br/><br/> Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/> Máquina virtual -> Instantáneas -> Quitar instantáneas | El usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.<br/><br/> Para restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).


## <a name="next-steps"></a>Pasos siguientes

Después de poner en funcionamiento la replicación, cuando se produce una interrupción, se conmuta por error a Azure y se crean máquinas virtuales de Azure a partir de los datos replicados. Entonces, puede acceder a cargas de trabajo y aplicaciones en Azure, hasta que conmute por recuperación a la ubicación principal cuando vuelva a funcionar de forma normal.

- [Aprenda más](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.
- Si migra máquinas en lugar de replicar y conmutar por recuperación, [lea más](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- [Lea sobre la conmutación por recuperación](site-recovery-failback-azure-to-vmware.md), para realizarla y replicar máquinas virtuales de Azure de nuevo en el sitio local principal.

## <a name="third-party-software-notices-and-information"></a>Avisos e información de software de terceros
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

