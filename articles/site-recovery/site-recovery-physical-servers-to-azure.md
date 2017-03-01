---
title: "Replicación de servidores físicos en Azure | Microsoft Docs"
description: "Se describe cómo implementar Azure Site Recovery para orquestar la replicación, la conmutación por error y la recuperación de servidores físicos de Windows o Linux locales en Azure con Azure Portal."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3396818cd177330b7123f3a346b1591a4bcb1e4e
ms.openlocfilehash: c9994e4e826bc35e1de439684ed5877db91fa069
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-physical-machines-to-azure-with-azure-site-recovery-using-the-azure-portal"></a>Replicación de máquinas físicas en Azure con Azure Site Recovery mediante Azure Portal


¡Bienvenido al servicio Azure Site Recovery!

Site Recovery es un servicio de Azure que contribuye a su estrategia de recuperación ante desastres y continuidad empresarial (BCDR). Este servicio organiza la replicación de máquinas virtuales y servidores físicos locales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

En este artículo se describe cómo replicar servidores físicos de Windows o Linux locales en Azure mediante Azure Site Recovery en Azure Portal.

Después de leer este artículo, publique los comentarios en la parte inferior de los comentarios de Disqus. Formule cualquier pregunta técnica en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="steps"></a>Pasos

Esto es lo que debe hacer:

1. Compruebe los requisitos previos y las limitaciones.
2. Configure las cuentas de red y almacenamiento de Azure.
3. Prepare la máquina local que desee implementar como servidor de configuración.
4. Cree un almacén de Recovery Services. El almacén contiene valores de configuración y organiza la replicación.
5. Especifique la configuración de origen, destino y replicación.
6. Implemente el servicio de movilidad en las máquinas virtuales que desee replicar.
7. Habilite la replicación para las máquinas virtuales.
7. Ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.

## <a name="prerequisites"></a>Requisitos previos

**Requisito de compatibilidad** | **Detalles**
--- | ---
**Las tablas de Azure** | Aprenda acerca de los [requisitos de Azure](site-recovery-prereq.md#azure-requirements)
**Servidor de configuración local** | Máquina local con Windows Server 2012 R2 o versiones posteriores. Configure este servidor durante la implementación de Site Recovery.<br/><br/> De forma predeterminada, el servidor de procesos y el servidor de destino maestro también están instalados en esta máquina virtual. Al escalar una verticalmente, podría necesitar un servidor de procesos independiente, con los mismos requisitos que el servidor de configuración.<br/><br/> Aprenda más sobre estos componentes [aquí](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**Máquinas virtuales locales** | Las máquinas virtuales que desee replicar deben ejecutar un [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs** | El servidor de configuración necesita acceso a estas direcciones URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.<br/></br> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/></br> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).<br/><br/> Permita esta dirección URL para la descarga de MySQL: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Servicio de movilidad** | Instalado en cada máquina virtual replicada.


## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery en Azure Portal
Azure tiene dos [modelos de implementación](../azure-resource-manager/resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: el de Azure Resource Manager y el clásico. Azure cuenta también con dos portales: el Portal de Azure clásico y Azure Portal.

En este artículo, se describe cómo realizar la implementación en Azure Portal, que permite llevarla a cabo de forma simplificada y ofrece nuevas características. Se puede usar el portal clásico para mantener los almacenes existentes. No se pueden crear almacenes con el portal clásico.


## <a name="set-up-azure"></a>Configuración de Azure

1. [Configure una red de Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - Las máquinas virtuales de Azure se colocarán en esta red cuando se creen después de la conmutación por error.
    - Puede configurar una red en el modo de [Resource Manager](../resource-manager-deployment-model.md) o en el clásico.

2. Configure una [cuenta de almacenamiento de Azure](../storage/storage-create-storage-account.md#create-a-storage-account) para los datos replicados.
    - La cuenta puede ser estándar o [premium](../storage/storage-premium-storage.md).
    - Puede configurar una red en el modo de Resource Manager o en el clásico.

## <a name="prepare-the-configuration-server"></a>Preparar el servidor de configuración

1. Instale Windows Server 2012 R2 o una versión posterior, en un servidor local.
2. Asegúrese de que la máquina virtual tenga acceso a las direcciones URL que aparecen en los [requisitos previos](#prerequisites).

**Prepare una cuenta para insertar el servicio de movilidad**: si desea insertar el servicio de movilidad en máquinas virtuales, necesita una cuenta que el servidor de procesos pueda usar para acceder a la máquina virtual. La cuenta solo se utiliza para la instalación de inserción. Puede usar una cuenta local o de dominio:

  - Para Windows, si no utiliza una cuenta de dominio, debe deshabilitar el control de acceso de usuario remoto en la máquina local. Para hacerlo, en el Registro, en **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, agregue la entrada DWORD **LocalAccountTokenFilterPolicy** con un valor de 1.
    - Si desea agregar la entrada del Registro de Windows desde una CLI, escriba:   ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Para Linux, la cuenta debe ser un usuario raíz en el servidor Linux de origen.

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>Selección del objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.

1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos > haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)
3. En **Objetivo de protección**, seleccione **En Azure** y, luego, **No virtualizado/Otro**.


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el servidor de configuración, regístrelo en el almacén y detecte máquinas virtuales.

1. Haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Origen**.
2. Si no tiene un servidor de configuración, haga clic en **+Servidor de configuración**.

    ![Configurar origen](./media/site-recovery-vmware-to-azure/set-source1.png)
3. En **Agregar servidor**, compruebe que aparezca **Servidor de configuración** en **Tipo de servidor**.
4. Descargue el archivo de instalación unificada de Site Recovery.
5. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute la instalación unificada. La clave será válida durante cinco días a partir del momento en que se genera.

   ![Configurar origen](./media/site-recovery-vmware-to-azure/set-source2.png)
6. En la máquina virtual del servidor de configuración, asegúrese de que el reloj del sistema esté sincronizado con un [servidor horario](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) y ejecute la instalación unificada para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro.

## <a name="run-site-recovery-unified-setup"></a>Ejecución de la instalación unificada de Site Recovery

Antes de comenzar:

- Asegúrese de que la hora de la máquina virtual coincida con la hora en la zona horaria local. Deben ser iguales. Si hay una diferencia de 15 minutos, antes o después, se podría producir un error en la instalación.
- Ejecute la instalación como administrador Local en la máquina virtual del servidor de configuración.
- Asegúrese de que TLS 1.0 está habilitada en la máquina virtual.

Después, ejecute el archivo de instalación para la instalación unificada en el servidor de configuración.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> El servidor de configuración se puede instalar a través de la línea de comandos. Para más información, consulte [Instalación del servidor de configuración con herramientas de línea de comandos](http://aka.ms/installconfigsrv).

## <a name="set-up-the-target"></a>Configuración del destino

Antes de configurar el entorno de destino, compruebe que tenga una [cuenta de almacenamiento y una red de Azure](#set-up-azure)

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique si el modelo de implementación de destino está basado en Resource Manager o si es el clásico.
3. Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

   ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)
4. Si no ha creado una cuenta de almacenamiento ni una red, haga clic en **+Cuenta de almacenamiento** o **+Red** para crear una cuenta o red de Resource Manager en línea.

## <a name="set-up-replication-settings"></a>Configuración de las opciones de replicación

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

Todas las máquinas que desee replicar deben tener instalado el servicio de movilidad. Puede instalarlo de varias maneras:

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

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**.
2. En **Origen**, seleccione Local.
3. En **Ubicación de origen**, seleccione el nombre del servidor de configuración.
4. En **Tipo de máquina**, seleccione **Máquinas físicas**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host de vSphere, o bien seleccione el host.
5. En **Servidor de procesos**, elija el servidor de procesos. Si no ha creado ningún servidor de procesos adicional, este será el servidor de configuración. y, a continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-physical-to-azure/chooseVM.png)

6. En **Destino**, seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales conmutadas por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales conmutadas por error.

7. Seleccione la cuenta de Azure Storage que desea usar para los datos de replicación. Si no desea usar una cuenta que ya haya configurado, puede crear una.

8. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** con el fin de aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. Si no desea usar una red existente, puede crear una.

    ![Habilitar replicación](./media/site-recovery-physical-to-azure/targetsettings.png)
9. En **Máquinas físicas**, haga clic en el botón "+", escriba el **nombre** y la **dirección IP** y elija el tipo de sistema operativo de la máquina que quiere replicar.
  ![Habilitación de la replicación](./media/site-recovery-physical-to-azure/machineselect.png) Deberá esperar unos minutos antes de que las máquinas se detecten y se muestren en la lista.

10. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que utilizará el servidor de procesos para instalar automáticamente Mobility Service en la máquina.
11. De manera predeterminada se replican todos los discos. Haga clic en **Todos los discos** y desactive todos los discos que no desea replicar. y, a continuación, haga clic en **Aceptar**. Puede establecer otras propiedades de las máquinas virtuales más adelante.

    ![Habilitar replicación](./media/site-recovery-physical-to-azure/configprop.png)
11. En **Configuración de la replicación** > **Establecer configuración de replicación**, compruebe que se haya seleccionado la directiva de replicación correcta. Si modifica una directiva, los cambios se aplicarán a la máquina que se replica y a las nuevas máquinas.
12. Habilite la **coherencia de múltiples VM** si desea recopilar las máquinas en un grupo de replicación y especifique un nombre para el grupo. y, a continuación, haga clic en **Aceptar**. Observe lo siguiente:

    * Todas las máquinas de un grupo de replicación se replican al mismo tiempo y comparten puntos de recuperación coherentes con los bloqueos y con la aplicación cuando conmutan por error.
    * Se recomienda que recopile las máquinas virtuales y los servidores físicos juntos para que reflejen las cargas de trabajo. Habilitar la coherencia de múltiples máquinas virtuales puede afectar al rendimiento de la carga de trabajo y solo debe utilizarse si las máquinas ejecutan la misma carga de trabajo y necesita coherencia.
    ![Habilitar replicación](./media/site-recovery-physical-to-azure/policy.png)

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

Una vez terminada la configuración, ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.


1. Para conmutar por error una sola máquina, en **Configuración** > **Elementos replicados**, haga clic en el icono Máquina virtual > **+Probar conmutación por error**.

    ![Probar conmutación por error](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan > **Probar conmutación por error**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).  

1. En **Probar conmutación por error**, seleccione la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

1. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo **Probar conmutación por error** en nombre de almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.

1. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure en Azure Portal > **Virtual Machines**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.

1. Si se [preparó para las conexiones después de la conmutación por error](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), debe ser capaz de conectarse a la máquina virtual de Azure.

1. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En **Notas** , registre y guarde las observaciones asociadas a la conmutación por error de prueba. Así se eliminarán las máquinas virtuales que se crearon durante la conmutación por error de prueba.

Para más información, consulte el documento sobre [conmutación por error de prueba de Azure](site-recovery-test-failover-to-azure.md).

## <a name="next-steps"></a>Pasos siguientes

Después de poner en funcionamiento la replicación, cuando se produce una interrupción, se conmuta por error a Azure y se crean máquinas virtuales de Azure a partir de los datos replicados. Entonces, puede acceder a cargas de trabajo y aplicaciones en Azure, hasta que conmute por recuperación a la ubicación principal cuando vuelva a funcionar de forma normal.

- [Aprenda más](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.
- Si migra máquinas en lugar de replicar y conmutar por recuperación, [lea más](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- Al replicar máquinas físicas, solo puede conmutar por error a un entorno de VMWare. [Lea acerca de la conmutación por recuperación](site-recovery-failback-azure-to-vmware.md).

## <a name="third-party-software-notices-and-information"></a>Avisos e información de software de terceros
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

