---
title: "Configurar la recuperación ante desastres para máquinas virtuales de Hyper-V locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 96e5027adfb443aba18895213e8d83894e3f060a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este tutorial se muestra cómo configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure. El tutorial es aplicable tanto a las máquinas virtuales de Hyper-V que se administran en nubes de System Center Virtual Machine Manager (VMM) como a las que no. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar Azure y los requisitos previos locales
> * Crear un almacén de Recovery Services para Site Recovery 
> * Configurar los entornos de replicación de origen y destino 
> * Configurar la asignación de red (si Hyper-V se administra mediante System Center VMM)
> * Creación de una directiva de replicación
> * Habilitación de la replicación para una máquina virtual


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- Asegúrese de entender la [arquitectura y los componentes del escenario](concepts-hyper-v-to-azure-architecture.md).
- Revise los [requisitos de compatibilidad](site-recovery-support-matrix-to-azure.md) de todos los componentes.
- Asegúrese de que las máquinas virtuales que quiere replicar cumplen los [requisitos de máquina virtual de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Para determinar la planificación de capacidad:
    - Use la [herramienta Capacity Planner de Hyper-V](http://aka.ms/asr-capacity-planner-excel) para averiguar la frecuencia de renovación.
    - Use [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) para analizar su entorno de origen, calcular el ancho de banda y determinar los requisitos de destino.
- Prepare Azure. Necesita una suscripción de Azure, una red virtual de Azure y una cuenta de almacenamiento.
- Prepare los hosts de Hyper-V locales y los servidores VMM, si procede.





### <a name="set-up-an-azure-account"></a>Configuración de una cuenta de Azure

Obtenga una [cuenta de Microsoft Azure](http://azure.microsoft.com/).

- Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Obtenga información sobre los [precios de Site Recovery](site-recovery-faq.md#pricing) y conozca los [detalles de los precios](https://azure.microsoft.com/pricing/details/site-recovery/).
- Averigüe qué [regiones se admiten](https://azure.microsoft.com/pricing/details/site-recovery/) en Site Recovery.

### <a name="verify-azure-account-permissions"></a>Comprobar los permisos de cuenta de Azure

Asegúrese de que su cuenta de Azure tiene los permisos necesarios para replicar máquinas virtuales.

- Revise los [permisos](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) que necesita para replicar máquinas en Azure.
- Compruebe y modifique los permisos de [acceso basado en roles](../active-directory/role-based-access-control-configure.md). 


### <a name="set-up-an-azure-network"></a>Configurar una red de Azure

Configure una [red de Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- Las VM de Azure se colocarán en esta red cuando se creen después de la conmutación por error.
- La red debe estar en la misma región que el almacén de Recovery Services.


### <a name="set-up-an-azure-storage-account"></a>Configurar una cuenta de almacenamiento de Azure

Configure una [cuenta de almacenamiento de Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replica máquinas virtuales locales en Azure Storage. Las máquinas virtuales de Azure se crean en el almacenamiento después de producirse la conmutación por error.
- La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services.
- La cuenta de almacenamiento puede ser estándar o [premium](../storage/common/storage-premium-storage.md).
- Si configura una cuenta Premium, necesitará una cuenta estándar adicional para los datos de registro.

### <a name="prepare-hyper-v-hosts"></a>Preparar los hosts de Hyper-V

1. Compruebe que los hosts de Hyper-V cumplen los [requisitos de compatibilidad](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Asegúrese de que los hosts pueden tener acceso a estas direcciones URL:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Todas las reglas de firewall basadas en direcciones IP deben permitir la comunicación con Azure.
    - Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
    - Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).

### <a name="prepare-vmm-servers"></a>Preparar los servidores VMM

Si VMM administra los hosts de Hyper-V, debe preparar el servidor VMM local. 

- Compruebe que el servidor VMM cumple los [requisitos de compatibilidad](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Asegúrese de que el servidor VMM tiene al menos una nube, con uno o más grupos host. El host de Hyper-V en el que se ejecutan las máquinas virtuales debe encontrarse en la nube.
- El servidor VMM necesita acceso a Internet, con acceso a las direcciones URL siguientes:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Todas las reglas de firewall basadas en direcciones IP deben permitir la comunicación con Azure.
    - Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
    - Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).
- Prepare el servidor VMM para la asignación de red.


#### <a name="prepare-vmm-for-network-mapping"></a>Preparar VMM para la asignación de red

Si usa VMM, la [asignación de red](site-recovery-network-mapping.md) asigna entre redes de máquinas virtuales de VMM locales y redes virtuales de Azure. La asignación garantiza que las máquinas virtuales de Azure se conecten a la red adecuada cuando se crean después de la conmutación por error.

Para preparar VMM para la asignación de red, siga estos pasos:

1. Asegúrese de que tiene una [red lógica de VMM](https://docs.microsoft.com/system-center/vmm/network-logical) que esté asociada a la nube en la que se encuentran los hosts de Hyper-V.
2. Asegúrese de que tiene una [red de máquinas virtuales](https://docs.microsoft.com/system-center/vmm/network-virtual) vinculada a la red lógica.
3. Conecte las máquinas virtuales a la red de máquinas virtuales.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Selección de un objetivo de protección

Seleccione qué quiere replicar y en dónde hacerlo.

1. En el almacén, haga clic en **Site Recovery** > **Preparar infraestructura** > **Objetivo de protección**.
2. En **Objetivo de protección**, seleccione **En Azure**> **Sí, con Hyper-V**. 
    - Si los hosts de Hyper-V no se administran mediante VMM, seleccione **No** para confirmar que no usa VMM.
    - Si los hosts se administran en nubes VMM, seleccione **Sí**.

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Cuando configure el entorno de origen, instale el proveedor de Azure Site Recovery y el agente de Azure Recovery Services, y registre los servidores locales en el almacén. 

1. En **Preparar infraestructura**, haga clic en **Origen**. 
    - Si no usa VMM, haga clic en **+Sitio de Hyper-V** y especifique un nombre de sitio. Después, haga clic en **+Hyper-V Server** y agregue un host o un clúster al sitio.
    - Si usa VMM, en **Preparar origen**, haga clic en **+ VMM** para agregar un servidor VMM. En **Agregar servidor**, compruebe que aparezca **System Center VMM server** (Servidor VMM de System Center) en **Tipo de servidor**.
2. Descargue los componentes del proveedor y del agente, en función de su entorno.
    - Solo para Hyper-V, descargue el archivo de instalación del proveedor. Ejecute el archivo en cada host de Hyper-V para instalar el proveedor y el agente.
        
        ![Proveedor sin VMM](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Para Hyper-V con VMM, descargue el proveedor y el agente por separado. Ejecute el archivo de instalación del proveedor en el servidor VMM. Ejecute el archivo de instalación del agente en cada host de Hyper-V.
    
        ![Proveedor y agente con VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute el programa de instalación del proveedor. La clave será válida durante cinco días a partir del momento en que se genera.

### <a name="install-components"></a>Instalar componentes

Instale los componentes como se resume en la tabla. 

**Componente** | **Detalles** | **Solo Hyper-V** | **Hyper-V con VMM**
--- | --- | --- | ---
**Proveedor de Azure Site Recovery** | Coordina la replicación en Azure | Instálelo en cada host de Hyper-V | Instálelo en el servidor VMM
**Agente de Recovery Services** | Controla la replicación de datos | Instálelo en cada host de Hyper-V | Instálelo en el host de Hyper-V


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Instalar el proveedor en Hyper-V sin VMM

Instale el proveedor en cada host de Hyper-V que haya agregado al sitio Hyper-V. Si va a realizar la instalación en un clúster de Hyper-V, ejecute el programa de instalación en cada nodo de clúster. Instalar y registrar los nodos de cada clúster de Hyper-V garantiza que las máquinas virtuales permanezcan protegidas incluso si se migran entre nodos.

1. En **Microsoft Update** puede optar por recibir actualizaciones para que las actualizaciones del proveedor se realicen según las directivas de Microsoft Update.
2. En **Instalación** acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**.
4. En **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave del almacén que ha descargado. Especifique la suscripción de Azure Site Recovery, el nombre del almacén y el sitio de Hyper-V al que pertenece el servidor Hyper-V.
5. En **Configuración de proxy**, especifique cómo se conecta el proveedor que se ejecuta en el servidor VMM o el host de Hyper-V a Site Recovery a través de Internet.
    * Para que se conecte directamente, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
    * Para usar un proxy, seleccione **Conectar con Azure Site Recovery con un servidor proxy**. Especifique la dirección del proxy, el puerto y las credenciales, si es necesario.
6. Una vez que el servidor se haya registrado en el almacén, haga clic en **Finalizar**.

Los metadatos del servidor de Hyper-V se recuperan mediante Azure Site Recovery y el servidor se muestra en **Site Recovery Infrastructure** > **Hyper-V Hosts** (Infraestructura de Site Recovery > Hosts de Hyper-V).


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Instalar el agente de Recovery Services en el host de Hyper-V sin VMM

Si usa VMM en la implementación, ejecute el programa de instalación del agente de Recovery Services en cada host de Hyper-V.

1. En el Asistente para instalación > **Comprobación de requisitos previos**, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.

    ![Requisitos previos del agente de Recovery Services](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. En **Configuración de la instalación**, acepte o modifique la ubicación de instalación y la ubicación de la memoria caché. La unidad de caché necesita al menos 5 GB de almacenamiento. Se recomienda una unidad con 600 GB o más de espacio disponible. Luego haga clic en **Instalar**.
4. En **Instalación**, cuando finalice la instalación, haga clic en **Cerrar** para finalizar el asistente.

##### <a name="set-up-internet-access-via-a-proxy"></a>Configurar el acceso a Internet a través de un proxy

El agente de Recovery Services del host de Hyper-V necesita acceso a Internet para conectarse a Azure para la replicación de máquinas virtuales. Si va a acceder a Internet a través de un proxy, configúrelo como sigue:

1. Abra el complemento Microsoft Azure Backup de MMC en el host de Hyper-V. De manera predeterminada, hay disponible un acceso directo para Microsoft Azure Backup en el escritorio o en la siguiente ruta de acceso: C:\Archivos de programa\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.
3. En la pestaña **Configuración de proxy**, especifique la información del proxy.

    ![Registro del agente MARS](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Compruebe que el agente puede llegar a las [direcciones URL necesarias](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Instalar el proveedor en el servidor VMM (Hyper-V con VMM)

1. En **Microsoft Update** puede optar por recibir actualizaciones para que las actualizaciones del proveedor se realicen según las directivas de Microsoft Update.
2. En **Instalación** acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**. 
3. Ahora, registre el servidor VMM en el almacén. En **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave del almacén que ha descargado. Especifique la suscripción de Azure Site Recovery y el nombre del almacén.

    ![Registro de servidor](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. En **Configuración de proxy**, especifique cómo se conecta el proveedor que se ejecuta en el servidor VMM o el host de Hyper-V a Site Recovery a través de Internet.

    * Para que se conecte directamente, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
    * Para usar un proxy, seleccione **Conectar con Azure Site Recovery con un servidor proxy**. Especifique la dirección del proxy, el puerto y las credenciales, si es necesario.
    - Se crea automáticamente una cuenta de ejecución de VMM (DRAProxyAccount) que usa las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. Se puede modificar la configuración de la cuenta de ejecución en la consola VMM > **Configuración** > **Seguridad** > **Cuentas de ejecución**. Reinicie el servicio VMM para actualizar los cambios.
5. En **Cifrado de datos**, especifique si se cifran todos los datos enviados a Azure. Si selecciona esta opción, Site Recovery emite un certificado. Necesitará este certificado para descifrar los datos más adelante.
6. En **Servidor VMM**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM. En **Synchronize cloud metadata with the vault** (Sincronizar metadatos en la nube con el almacén), seleccione si quiere sincronizar los metadatos de todas las nubes del servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.

Después de que finalice el registro, los metadatos del servidor se recuperan mediante Azure Site Recovery y el servidor VMM se muestra en **Site Recovery Infrastructure** (Infraestructura de Site Recovery).






## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino. 

1. Haga clic en **Preparar infraestructura** > **Destino**.
2. Seleccione la suscripción y el grupo de recursos donde se crearán las máquinas virtuales de Azure después de la conmutación por error. Elija el modelo de implementación que quiere usar en Azure para las máquinas virtuales.

3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

## <a name="configure-network-mapping-with-vmm"></a>Configurar asignación de red (con VMM)

Si usa VMM, configure la asignación de red.

1. En **Infraestructura de Site Recovery** > **Asignaciones de red** > **Asignación de red**, haga clic en el icono de **+Asignación de red**.
2. En **Agregar asignación de red**, seleccione el servidor VMM de origen. Seleccione **Azure** como destino.
3. Compruebe la suscripción y el modelo de implementación después de la conmutación por error.
4. En **Red de origen**, seleccione la red de máquinas virtuales de origen local.
5. En **Red de destino**, seleccione la red de Azure en la que se ubicarán las máquinas virtuales de Azure replicadas cuando estas se creen después de la conmutación por error. y, a continuación, haga clic en **Aceptar**.

    ![Asignación de red](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

1. Haga clic en **Preparar infraestructura** > **Configuración de la replicación** > **+Crear y asociar**.
2. En **Crear y asociar directiva**, especifique un nombre de directiva.
3. En **Frecuencia de copia**, especifique la frecuencia con la que desea replicar diferencias de datos después de la replicación inicial (cada 30 segundos, 5 o 15 minutos).

    > [!NOTE]
    >  Cuando la replicación se realiza en Premium Storage, no se admite una frecuencia de 30 segundos. La limitación viene determinada por el número de instantáneas por blob (100) que admite Premium Storage. [Más información](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. En **Retención de punto de recuperación**, especifique la duración del período de retención (en horas) para cada punto de recuperación. Los equipos protegidos se pueden recuperar en cualquier punto dentro de un período.
5. En **Frecuencia de instantánea coherente con la aplicación**especifique la frecuencia (entre 1 y 12 horas) con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Hyper-V usa dos tipos de instantáneas:
    - **Instantánea estándar**: proporciona una instantánea incremental de toda la máquina virtual.
    - **Instantánea coherente con la aplicación**: toma una instantánea en un momento dado de los datos de la aplicación dentro de la máquina virtual. El Servicio de instantáneas de volumen (VSS) garantiza que las aplicaciones se encuentren en un estado coherente cuando se toma la instantánea. La habilitación de las instantáneas coherentes con la aplicación afecta al rendimiento de la aplicación en las máquinas virtuales de origen. Establezca un valor que sea inferior al número de puntos de recuperación adicionales que configure.
6. En **Hora de inicio de la replicación inicial**, indique cuándo debe comenzar la replicación inicial. La replicación se realiza a través del ancho de banda de Internet, por lo que tal vez le interese programarla fuera de las horas punta.
7. En **Cifrar datos almacenados en Azure**especifique si desea cifrar los datos en reposo en Azure Storage. A continuación, haga clic en **Aceptar**.

    ![Directiva de replicación](./media/tutorial-hyper-v-to-azure/replication-policy.png)


Cuando se crea una directiva, se asocia automáticamente con la nube de VMM o el sitio de Hyper-V.

## <a name="enable-replication"></a>Habilitar replicación


1. Haga clic en **Replicar la aplicación** > **Origen**. 
2. En **Origen**, seleccione el sitio de Hyper-V, el servidor VMM o la nube. y, a continuación, haga clic en **Aceptar**.
3. En **Destino**, compruebe el destino (debe ser Azure), la suscripción de almacén y el modelo que quiere usar en Azure después de la conmutación por error.
4. Seleccione la cuenta de almacenamiento para los datos replicados y la red de Azure en la que se ubicarán las máquinas virtuales de Azure después de la conmutación por error.
5. En **Máquinas virtuales** > **Seleccionar**, seleccione las máquinas virtuales que quiere replicar. y, a continuación, haga clic en **Aceptar**.

 Puede hacer un seguimiento del progreso de la acción **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. Una vez concluido el trabajo **Finalizar protección**, la replicación inicial finaliza y la máquina virtual está preparada para la conmutación por error.

## <a name="next-steps"></a>Pasos siguientes
[Obtención de detalles de recuperación ante desastres](tutorial-dr-drill-azure.md)
