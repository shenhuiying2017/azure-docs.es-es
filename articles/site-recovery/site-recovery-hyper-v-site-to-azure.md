---
title: "Replicación de máquinas virtuales de Hyper-V en Azure | Microsoft Docs"
description: "Describe cómo coordinar la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V locales en Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/05/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0ef782a7bb7a98da2ec63c91732b3d5ddd959848
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure-using-azure-site-recovery-with-the-azure-portal"></a>Replicación de máquinas virtuales de Hyper-V (sin VMM) en Azure usando Azure Site Recovery con Azure Portal

> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure clásico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell: administrador de recursos](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

En este artículo se describe cómo replicar máquinas virtuales locales de Hyper-V en Azure mediante [Azure Site Recovery](site-recovery-overview.md) en Azure Portal. En esta implementación, las máquinas virtuales de Hyper-V no están administradas por System Center Virtual Machine Manager (VMM).

Cuando haya terminado de leer este artículo, publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Si desea migrar equipos a Azure (sin conmutación por recuperación), puede obtener más información al respecto en [este artículo](site-recovery-migrate-to-azure.md).



## <a name="deployment-steps"></a>Pasos de implementación

Siga el artículo para completar estos pasos de implementación:

1. [Obtenga más información](site-recovery-components.md#hyper-v-to-azure) sobre la arquitectura de esta implementación. Además, [conozca](site-recovery-hyper-v-azure-architecture.md) cómo funciona la replicación de Hyper-V en Site Recovery.
2. Compruebe los requisitos previos y las limitaciones.
3. Configure las cuentas de red y almacenamiento de Azure.
4. Prepare los hosts de Hyper-V.
5. Cree un almacén de Recovery Services. El almacén contiene valores de configuración y organiza la replicación.
6. Especifique la configuración de origen. Cree un sitio de Hyper-V que contenga los hosts de Hyper-V y registre el sitio en el almacén. Instale el proveedor de Azure Site Recovery y el agente de Microsoft Recovery Services en los hosts de Hyper-V.
7. Especifique la configuración de destino y replicación.
8. Habilite la replicación para las máquinas virtuales.
9. Ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.



## <a name="prerequisites"></a>Requisitos previos


**Requisito** | **Detalles** |
--- | --- |
**Las tablas de Azure** | Obtenga información sobre los [requisitos de Azure](site-recovery-prereq.md#azure-requirements)
**Servidores locales** | [Más información](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager) sobre los requisitos de los hosts de Hyper-V locales.
**Máquinas virtuales de Hyper-V locales** | Las máquinas virtuales que desee replicar deben ejecutar un [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**Direcciones URL de Azure** | Los hosts de Hyper-V necesitan tener acceso a estas direcciones URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.<br/></br> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/></br> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).



## <a name="prepare-for-deployment"></a>Preparación de la implementación

Para preparar la implementación debe:

1. [Configurar una red de Azure](#set-up-an-azure-network) en la que las máquinas virtuales de Azure se encuentran cuando se crean después de la conmutación por error.
2. [Configurar una cuenta de almacenamiento de Azure](#set-up-an-azure-storage-account) para datos replicados.
3. [Preparar los hosts de Hyper-V](#prepare-the-hyper-v-hosts) para asegurarse de que puedan acceder a las direcciones URL necesarias.

### <a name="set-up-an-azure-network"></a>Configurar una red de Azure

Configure una red de Azure. Debe hacer esto para que las máquinas virtuales de Azure creadas después de la conmutación por error se conecten a una red.

* La red debe estar en la misma región que el almacén de Recovery Services.
* Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, configure la red de Azure en [modo Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o en [modo clásico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
* Es recomendable configurar una red antes de empezar. Si no lo hace, deberá hacerlo durante la implementación de Site Recovery.
- Las cuentas de almacenamiento usadas en Site Recovery no se pueden [mover](../azure-resource-manager/resource-group-move-resources.md) dentro de la misma suscripción o entre suscripciones diferentes.


### <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

- Necesita una cuenta Estándar o Premium de Azure Storage para albergar los datos replicados en Azure. [Premium Storage](../storage/storage-premium-storage.md) suele usarse para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y latencia baja para hospedar cargas de trabajo intensivas de E/S.
- Si desea utilizar una cuenta premium para almacenar los datos replicados, también necesita una cuenta de almacenamiento estándar para almacenar los registros de replicación que capturan los cambios continuos de los datos locales.
- Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, configure una cuenta en [modo Resource Manager](../storage/storage-create-storage-account.md) o en [modo clásico](../storage/storage-create-storage-account-classic-portal.md).
- Es recomendable configurar una cuenta de almacenamiento antes de empezar. Si no lo hace, deberá hacerlo durante la implementación de Site Recovery. Las cuentas deben estar en la misma región que el almacén de Recovery Services.
- No se pueden mover cuentas de almacenamiento que usa Site Recovery en grupos de recursos dentro de la misma suscripción, o bien en distintas suscripciones.


### <a name="prepare-the-hyper-v-hosts"></a>Preparar los hosts de Hyper-V

* Asegúrese de que los hosts de Hyper-V cumplen los [requisitos previos](site-recovery-prereq.md#disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager).
- Asegúrese de que los hosts pueden acceder a las direcciones URL necesarias.


## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** > **Supervisión y administración** > **Backup y Site Recovery (OMS)**.  

    ![Almacén nuevo](./media/site-recovery-hyper-v-site-to-azure/new-vault.png)

3. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. Si tiene más de una suscripción, seleccione una de ellas.

4. [Cree un grupo de recursos nuevo](../azure-resource-manager/resource-group-template-deploy-portal.md) o seleccione uno que ya exista, y especifique una región de Azure. Las máquinas se replicarán en esta región. Para comprobar las regiones admitidas, consulte la disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

5. Si desea acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** y, después, en **Crear**.

    ![Almacén nuevo](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

El nuevo almacén aparecerá en la lista **Panel** > **Todos los recursos** y en la hoja principal de **Almacenes de Recovery Services**.



## <a name="select-the-protection-goal"></a>Selección del objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.

1. En **Almacenes de Recovery Services**, seleccione el almacén.
2. En **Introducción**, haga clic en **Site Recovery** > **Preparar infraestructura** > **Objetivo de protección**.

    ![Elegir objetivos](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)
3. En **Objetivo de protección** seleccione **En Azure** y seleccione **Sí, con Hyper-V**. Seleccione **No** para confirmar que no usa VMM. y, a continuación, haga clic en **Aceptar**.

    ![Elegir objetivos](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Configure el sitio Hyper-V, instale el Proveedor de Azure Site Recovery y el agente de Azure Recovery Services en hosts de Hyper-V y registre el sitio en el almacén.

1. En **Preparar infraestructura**, haga clic en **Origen**. Para agregar un nuevo sitio de Hyper-V como contenedor para los hosts o clústeres de Hyper-V, haga clic en **+Sitio Hyper-V**.

    ![Configurar origen](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)
2. En **Crear sitio Hyper-V**, especifique un nombre para el sitio. A continuación, haga clic en **Aceptar**. Ahora, seleccione el sitio que creó y, después, haga clic en **+Hyper-V Server** para agregar un servidor al sitio.

    ![Configurar origen](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. En **Agregar servidor** > **Tipo de servidor**, compruebe que se muestra **Hyper-V Server**.

    - Asegúrese de que el servidor de Hyper-V que se va a agregar satisface los [requisitos previos](#on-premises-prerequisites) y puede acceder a las direcciones URL especificadas.
    - Descargue el archivo de instalación del proveedor de Azure Site Recovery. Ejecute este archivo para instalar el proveedor y el agente de Recovery Services en cada host de Hyper-V.

    ![Configurar origen](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)


## <a name="install-the-provider-and-agent"></a>Instalación del proveedor y el agente

1. Ejecute el archivo de instalación del proveedor en cada host que agregue al sitio Hyper-V. Si va a realizar la instalación en un clúster de Hyper-V, ejecute el programa de instalación en cada nodo de clúster. Instalar y registrar los nodos de cada clúster de Hyper-V garantiza que las máquinas virtuales permanezcan protegidas incluso si se migran entre nodos.
2. En **Microsoft Update** puede optar por recibir actualizaciones para que las actualizaciones del proveedor se realicen según las directivas de Microsoft Update.
3. En **Instalación** acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**.
4. En **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave del almacén que ha descargado. Especifique la suscripción de Azure Site Recovery, el nombre del almacén y el sitio de Hyper-V al que pertenece el servidor Hyper-V.

    ![Registro de servidor](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5. En **Configuración del proxy**, especifique cómo se conecta el proveedor que se ejecuta en los hosts de Hyper-V a Azure Site Recovery a través de Internet.

    * Si quiere que el proveedor se conecte directamente, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
    * Si el proxy existente requiere autenticación, o si quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Conectar con Azure Site Recovery con un servidor proxy**.
    * Si utiliza un proxy:
        - Especifique la dirección, el puerto y las credenciales.
        - Asegúrese de que las direcciones URL descritas en los [requisitos previos](#prerequisites) se permiten a través del proxy.

    ![Internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6. Una vez finalizada la instalación, haga clic en **Registrar** para registrar el servidor en el almacén.

    ![Ubicación de instalación](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7. Después de que finalice el registro, los metadatos del servidor de Hyper-V se recuperan mediante Azure Site Recovery y el servidor se muestra en **Infraestructura de Site Recovery** > **Hosts de Hyper-V**.


## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Especifique la cuenta de Azure Storage para la replicación y la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

1. Haga clic en **Preparar infraestructura** > **Destino**.
2. Seleccione la suscripción y el grupo de recursos donde desee crear las máquinas virtuales de Azure después de la conmutación por error. Elija el modelo de implementación que desee usar en Azure (clásico o de Resource Manager) para las máquinas virtuales.

3. Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

    - Si no tiene una cuenta de almacenamiento, haga clic en **+Storage** para crear una cuenta basada en Resource Manager insertada. Consulte los [requisitos de almacenamiento](site-recovery-prereq.md#azure-requirements).
    - Si no tiene una red de Azure, haga clic en **+Red** para crear una red basada en Resource Manager insertada.

    ![Storage](./media/site-recovery-vmware-to-azure/enable-rep3.png)




## <a name="configure-replication-settings"></a>Configuración de opciones de replicación

1. Para crear una nueva directiva de replicación, haga clic en **Preparar infraestructura** > **Configuración de la replicación** > **+Crear y asociar**.

    ![Red](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)
2. En **Crear y asociar directiva**, especifique un nombre de directiva.
3. En **Frecuencia de copia**, especifique la frecuencia con la que desea replicar diferencias de datos después de la replicación inicial (cada 30 segundos, 5 o 15 minutos).

    > [!NOTE]
    > Cuando la replicación se realiza en Premium Storage, no se admite una frecuencia de 30 segundos. La limitación viene determinada por el número de instantáneas por blob (100) que admite Premium Storage. [Más información](../storage/storage-premium-storage.md#snapshots-and-copy-blob).

4. En **Retención de punto de recuperación**, especifique la duración en horas del período de retención para cada punto de recuperación. Las máquinas virtuales se pueden recuperar en cualquier punto dentro de un período.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (entre 1 y 12 horas) con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación.

    - Hyper-V usa dos tipos de instantáneas, una instantánea estándar que proporciona una instantánea incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual.
    - Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea.
    - S habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.

6. En **Hora de inicio de la replicación inicial**, especifique cuándo debe comenzar la replicación inicial. La replicación se produce utilizando el ancho de banda de Internet, así que puede que deba programarla fuera del horario de trabajo. y, a continuación, haga clic en **Aceptar**.

    ![Directiva de replicación](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Cuando se crea una nueva directiva, esta se asocia automáticamente con el sitio Hyper-V. Puede asociar un sitio Hyper-V y las máquinas virtuales que contiene con varias directivas de replicación en **Replicación** > nombre de directiva > **Associate Hyper-V Site** (Asociar sitio Hyper-V).

## <a name="capacity-planning"></a>Planificación de capacidad

Ahora que tiene la infraestructura básica configurada, puede planear la capacidad y averiguar si necesita recursos adicionales.

Site Recovery proporciona la utilidad Capacity Planner para ayudarle a asignar los recursos adecuados para procesos, redes y almacenamiento. Puede ejecutar el planificador en modo rápido para obtener resultados basados en un promedio de máquinas virtuales, discos y almacenamiento o en el modo detallado con cifras personalizadas en el nivel de carga de trabajo. Antes de empezar, necesita realizar lo siguiente:

* Recopilar información sobre su entorno de replicación, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
* Calcular la tasa de cambio (renovación) diaria para los datos replicados. Para ayudarle a ello, puede usar la [herramienta de planeamiento de capacidad para réplicas de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) .

1. Haga clic en **Descargar** para descargar la herramienta y, luego, ejecútela. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2. Una vez que haya terminado, seleccione **Yes** (Sí) en **Have you run the Capacity Planner**? (¿Ha ejecutado la herramienta Capacity Planner?)

   ![Planificación de capacidad](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

Más información sobre el [control del ancho de banda de red](#network-bandwidth-considerations)



## <a name="enable-replication"></a>Habilitar replicación

Antes de empezar, asegúrese de que la cuenta de usuario de Azure tiene los [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necesarios para habilitar la replicación de una nueva máquina virtual en Azure.

Habilite la replicación para máquinas virtuales como sigue:          

1. Haga clic en **Replicar la aplicación** > **Origen**. Después de configurar la replicación por primera vez, haga clic en **+Replicar** para habilitar la replicación de más máquinas.

    ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)
2. En **Origen**, seleccione el sitio Hyper-V. A continuación, haga clic en **Aceptar**.
3. En **Destino** seleccione la suscripción de almacén y el modelo de conmutación por error que se va a utilizar en Azure (Resource Manager o clásico) después de la conmutación por error.
4. Seleccione la cuenta de almacenamiento que desea usar. Si no tiene una cuenta que desea utilizar, puede [crear una](#set-up-an-azure-storage-account). A continuación, haga clic en **Aceptar**.
5. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.

    - Para aplicar la configuración de red a todas las máquinas habilitadas para replicación, seleccione la opción **Configurar ahora para las máquinas seleccionadas**.
    - Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina.
    - Si no tiene una red que desea utilizar, puede [crear una](#set-up-an-azure-network). Seleccione una subred si es posible. A continuación, haga clic en **Aceptar**.

   ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic en cada máquina que desea replicar y selecciónela. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. A continuación, haga clic en **Aceptar**.

    ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/enable-replication5-for-exclude-disk.png)

7. En **Propiedades** > **Configurar propiedades**, seleccione el sistema operativo para las máquinas virtuales seleccionadas y el disco del sistema operativo.
8. Compruebe que el nombre de la máquina virtual de Azure (nombre de destino) cumple los [requisitos de las máquinas virtuales de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. De forma predeterminada, se seleccionan todos los discos de la máquina virtual para la replicación, pero se pueden borrar todos aquellos que se deseen excluir.
    - A veces se excluyen discos para reducir el ancho de banda de replicación. Por ejemplo, es posible que no desee replicar discos con datos temporales o datos que se actualizan cada vez que se reinicia un equipo o una aplicación (como pagefile.sys o tempdb de Microsoft SQL Server). Para excluir el disco de la replicación, deselecciónelo.
    - Solo se pueden excluir los discos básicos. Los discos del sistema operativo no se pueden excluir.
    - Se recomienda no excluir discos dinámicos. Site Recovery no se puede identificar si un disco duro virtual de una máquina virtual invitada es básico o dinámico. Si no se excluyen todos los discos de volumen dinámico dependientes, el disco dinámico protegido se mostrará como uno fallido cuando la máquina virtual se conmute por error, y no se podrá acceder a los datos de dicho disco.
        - Una vez habilitada la replicación, no puede agregar ni quitar discos para la replicación. Si desea agregar o excluir un disco, deshabilite la protección de la máquina virtual y vuelva a habilitarla.
        - No se produce una conmutación por recuperación de los discos que se crean manualmente en Azure. Por ejemplo, si realiza la conmutación por error de tres discos y crea dos directamente en una máquina virtual de Azure, solo tres discos que se conmutaron por error se conmutarán por recuperación desde Azure hasta Hyper-V. No puede incluir los discos creados manualmente en la conmutación por recuperación o en la replicación inversa de Hyper-V a Azure.
        - Si excluye un disco necesario para que una aplicación funcione, después de la conmutación por error a Azure, debe crearlo manualmente en Azure para poder ejecutar la aplicación replicada. También puede integrar Azure Automation en un plan de recuperación para crear el disco durante la conmutación por error de la máquina.

10. Haga clic en **Aceptar** para guardar los cambios. Puede establecer propiedades adicionales más adelante.

     ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/enable-replication6-with-exclude-disk.png)

11. En **Configuración de replicación** > **Establecer configuración de replicación**, seleccione la directiva de replicación que desea aplicar para las máquinas virtuales protegidas. A continuación, haga clic en **Aceptar**. Puede modificar la directiva de replicación en **Directivas de replicación** > nombre de directiva > **Editar configuración**. Los cambios que aplique se utilizarán tanto para las máquinas que ya se estén replicando como para otras nuevas.


   ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Puede hacer un seguimiento del progreso del trabajo **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar la protección** .

### <a name="view-and-manage-vm-properties"></a>Visualización y administración de las propiedades de la máquina virtual

Es recomendable que compruebe las propiedades de la máquina de origen.

1. En **Elementos protegidos**, haga clic en **Elementos replicados** y seleccione la máquina.

    ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)
2. En **Propiedades** puede ver la información de replicación y conmutación por error de la máquina virtual.

    ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)
3. En **Proceso y red** > **Propiedades de proceso**, puede especificar el nombre y el tamaño de destino de la máquina virtual de Azure. Modifique el nombre para que cumpla con los requisitos de Azure si es necesario. También puede ver y modificar la información acerca de la red, la subred y la dirección IP de destino que se asignarán a la máquina virtual de Azure. Tenga en cuenta lo siguiente:

   * Puede establecer la dirección IP de destino. Si no proporciona una dirección, la máquina conmutada por error usará DHCP. Si establece una dirección que no está disponible en el momento de la conmutación por error, se producirá un error. Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red.
   * El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino, de la siguiente manera:

     * Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
     * Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
     * Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino es compatible con cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador.     
     * Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.
     * Si la máquina virtual tiene varios adaptadores de red, el primero de ellos que se muestre en la lista se convertirá en el *predeterminado* en la máquina virtual de Azure.

     ![Habilitar replicación](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

4. En **Discos** puede ver los discos de datos y del sistema operativo en la máquina virtual que se va a replicar.

#### <a name="managed-disks"></a>Discos administrados

En **Proceso y red** > **Propiedades de proceso**, puede establecer la opción "Usar discos administrados" en "Sí" para la máquina virtual si desea conectar discos administrados a la máquina durante la migración a Azure. Los discos administrados simplifican la administración de discos para las máquinas virtuales de Azure IaaS, ya que administra las cuentas de almacenamiento asociadas a los discos de la máquina virtual. [Más información sobre discos administrados](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview).

   - Los discos administrados se crean y conectan a la máquina virtual solo en una conmutación por error de Azure. Al habilitar la protección, los datos de las máquinas locales continuarán la replicación en las cuentas de almacenamiento.
   Los discos administrados solo se pueden crear para máquinas virtuales implementadas con el modelo de implementación de Resource Manager.

  > [!NOTE]
  > La conmutación por recuperación desde Azure al entorno local de Hyper-V no se admite actualmente en máquinas con discos administrados. Establezca "Usar discos administrados" en "Sí" únicamente si piensa migrar esta máquina a Azure.

   - Cuando se establece "Usar discos administrados" en "Sí", solo los conjuntos de disponibilidad del grupo de recursos con "Usar discos administrados" establecido en "Sí" estarán disponibles para la selección. Esto ocurre porque las máquinas virtuales con discos administrados solo pueden formar parte de conjuntos de disponibilidad con la propiedad "Usar discos administrados" establecida en "Sí". Asegúrese de crear conjuntos de disponibilidad con la propiedad "Usar discos administrados" establecida en función de su intención de usar discos administrados en la conmutación por error. Del mismo modo, cuando se establece "Usar discos administrados" en "No", solo los conjuntos de disponibilidad del grupo de recursos con la propiedad "Usar discos administrados" establecida en "No" estarán disponibles para la selección. [Más información sobre discos administrados y conjuntos de disponibilidad](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).

  > [!NOTE]
  > Si la cuenta de almacenamiento utilizada para la replicación ha sido cifrada con el Cifrado del servicio de Storage en algún momento, se producirá un error en la creación de discos administrados durante la conmutación por error. Puede establecer "Usar discos administrados" en "No" y volver a intentar la conmutación por error o deshabilitar la protección de la máquina virtual y protegerla en una cuenta de almacenamiento que no haya tenido habilitado el Cifrado del servicio de Storage en ningún momento.
  > [Más información sobre el Cifrado del servicio de Storage y los discos administrados](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="test-the-deployment"></a>Prueba de la implementación

Para probar la implementación, puede ejecutar una conmutación por error de prueba para una sola máquina virtual o un plan de recuperación que contenga una o varias máquinas virtuales.

### <a name="before-you-start"></a>Antes de comenzar

 - Si desea conectarse a máquinas virtuales de Azure mediante RDP después de la conmutación por error, obtenga más información acerca de [cómo prepararse para conectar](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Para realizar una comprobación completa, es preciso que realice una copia de Active Directory y DNS de su entorno de prueba. [Más información](site-recovery-active-directory.md#test-failover-considerations).

### <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1. Para conmutar por error una sola máquina, en **Elementos replicados**, haga clic en el icono Máquina virtual > **+Probar conmutación por error**.
2. Para conmutar por error un plan de recuperación, en **Planes de recuperación**, haga clic con el botón derecho en el plan > **Probar conmutación por error**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).
3. En **Probar conmutación por error**, seleccione la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.
4. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede realizar un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo **Probar conmutación por error** en nombre de almacén > **Trabajos** > **Trabajos de Site Recovery**.
5. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure en Azure Portal > **Virtual Machines**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.
6. Si se preparó para las conexiones después de la conmutación por error, debe ser capaz de conectarse a la máquina virtual de Azure.
7. Una vez que haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación. En **Notas** , registre y guarde las observaciones asociadas a la conmutación por error de prueba. Así se eliminarán las máquinas virtuales que se crearon durante la conmutación por error de prueba.

Para más información, consulte el artículo [Conmutación por error de prueba a Azure en Site Recovery](site-recovery-test-failover-to-azure.md).



## <a name="monitor-the-deployment"></a>Supervisión de la implementación

Supervise la configuración y el estado de la implementación de Site Recovery:

1. Haga clic en el nombre del almacén para acceder al panel **Essentials** . En este panel puede realizar un seguimiento de los trabajos de Site Recovery, el estado de la replicación, los planes de recuperación, y el estado y los eventos del servidor.  

    ![Essentials](./media/site-recovery-hyper-v-site-to-azure/essentials.png)
2. En el icono de **Estado** puede supervisar los servidores del sitio que están experimentando el problema y los eventos provocados por Site Recovery en las últimas 24 horas. Puede personalizar Essentials para mostrar los iconos y los diseños que sean más útiles para usted, incluido el estado de otros almacenes de Copia de seguridad y Site Recovery.
3. Puede administrar y supervisar la replicación en los iconos de **Elementos replicados**, **Planes de recuperación** y **Trabajos de Site Recovery**. Puede ver más detalles de los trabajos en **Trabajos** > **Trabajos de Site Recovery**.

## <a name="command-line-provider-and-agent-installation"></a>Instalación del agente y del proveedor de línea de comandos

El agente y el proveedor de Azure Site Recovery también pueden instalarse mediante la siguiente línea de comandos. Este método se puede usar para instalar el proveedor en Server Core para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\ASR.
2. Desde un símbolo del sistema con privilegios elevados, ejecute estos comandos para extraer el instalador del proveedor.

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Ejecute este comando para instalar los componentes:

            C:\ASR> setupdr.exe /i
4. Ejecute estos comandos para registrar el servidor en el almacén:

            CD C:\Program Files\Microsoft Azure Site Recovery Provider\
            C:\Program Files\Microsoft Azure Site Recovery Provider\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>

<br/>
Donde:

* **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro.  
* **/FriendlyName**: parámetro obligatorio para el nombre del servidor host Hyper-V que aparece en el portal de Azure Site Recovery.
* **/proxyAddress**: parámetro opcional que especifica la dirección del servidor proxy.
* **/proxyport** : parámetro opcional que especifica el puerto del servidor proxy.
* **/proxyUsername**: parámetro opcional que especifica el nombre de usuario de proxy (si el proxy requiere autenticación).
* **/proxyPassword**: parámetro opcional que especifica la contraseña para autenticarse con el servidor proxy (si el proxy requiere autenticación).


## <a name="network-bandwidth-considerations"></a>Consideraciones sobre el ancho de banda de red
Puede utilizar la [herramienta de planeación de capacidad de Hyper-V](site-recovery-capacity-planner.md) para calcular el ancho de banda necesario para la replicación (replicación inicial y luego la diferencial). Para controlar el uso de ancho de banda de la replicación tiene algunas opciones:

* **Limitar ancho de banda**: el tráfico de Hyper-V que se replica en Azure pasa a través de un host de Hyper-V específico. Puede limitar el ancho de banda en el servidor host.
* **Retocar el ancho de banda**: puede influir en el ancho de banda utilizado para la replicación mediante un par de claves del Registro.

### <a name="throttle-bandwidth"></a>Limitar el ancho de banda
1. Abra el complemento Copia de seguridad de Microsoft Azure en el servidor host de Hyper-V. De manera predeterminada, hay disponible un acceso directo para Copia de seguridad de Microsoft Azure en el escritorio o en la siguiente ruta de acceso: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.
3. En la pestaña **Limitación**, seleccione **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad** y defina los límites durante las horas de trabajo y fuera de las horas de trabajo. Los intervalos válidos van de 512 Kbps a 102 Mbps por segundo.

    ![Limitar el ancho de banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

También puede utilizar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para establecer la limitación. Aquí tiene un ejemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que no se requiere ninguna limitación.

### <a name="influence-network-bandwidth"></a>Control del uso de ancho de banda de red
1. En el Registro, vaya a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Para influir en el tráfico de ancho de banda en un disco de replicación, modifique el valor de **UploadThreadsPerVM**o cree la clave en caso de que no exista.
   * Para influir en el ancho de banda para el tráfico de conmutación por recuperación de Azure, modifique el valor **DownloadThreadsPerVM**.
2. El valor predeterminado es 4. En una red "sobreaprovisionada", se deben cambiar los valores predeterminados de estas claves de registro. El valor máximo es 32. Supervise el tráfico para optimizar el valor.

## <a name="next-steps"></a>Pasos siguientes

Cuando se haya completado la replicación inicial y haya probado la implementación, puede invocar las conmutaciones por error a medida que las necesite. [Obtenga más información](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error y cómo ejecutarlos.

