---
title: "Configuración de la recuperación ante desastres de las máquinas virtuales de Hyper-V locales en nubes de VMM en Azure con Azure Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en las nubes de VMM de System Center en Azure, con el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5f364c6f8a88ad53c12909f0e9f10afcce5ef8af
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configuración de la recuperación ante desastres de las máquinas virtuales de Hyper-V locales en nubes de VMM en Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este tutorial se muestra cómo configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure. El tutorial se aplica a las máquinas virtuales de Hyper-V administradas por System Center Virtual Machine Manager (VMM). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Seleccionar el origen y destino de la replicación.
> * Configurar el entorno de replicación de origen, incluidos los componentes de Site Recovery local y el entorno de replicación de destino.
> * Configurar la asignación de red para realizar asignaciones entre las redes de VM de VMM y las redes virtuales de Azure.
> * Creación de una directiva de replicación
> * Habilitación de la replicación para una máquina virtual

Este es el tercer tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores:

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. [Preparación de un entorno de Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

Antes de empezar, es útil [revisar la arquitectura](concepts-hyper-v-to-azure-architecture.md) de este escenario de recuperación ante desastres.



## <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación

1. En **Todos los servicios** > **Almacenes de Recovery Services**, haga clic en el nombre del almacén que se usa en estos tutoriales, **ContosoVMVault**.
2. En **Introducción**, haga clic en **Site Recovery**. Luego, haga clic en **Preparar infraestructura**.
3. En **Objetivo de protección** > **¿Dónde se encuentran sus máquinas?**, seleccione **Local**.
4. En **¿Dónde quiere replicar las máquinas?**, Seleccione **En Azure**.
5. En **¿Las máquinas están virtualizadas?**, seleccione **Sí, con Hyper-V**.
6. En **Are you using System Center VMM** (¿Usa System Center VMM?), seleccione **Sí**. A continuación, haga clic en **Aceptar**.

    ![Objetivo de replicación](./media/tutorial-hyper-v-vmm-to-azure/replication-goal.png)



## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Cuando configure el entorno de origen, instale el proveedor de Azure Site Recovery y el agente de Azure Recovery Services, y registre los servidores locales en el almacén. 

1. En **Preparar infraestructura**, haga clic en **Origen**.
2. En **Preparar origen**, haga clic en **+ VMM** para agregar un servidor VMM. En **Agregar servidor**, compruebe que aparezca **System Center VMM server** (Servidor VMM de System Center) en **Tipo de servidor**.
3. Descargue el programa de instalación del proveedor de Microsoft Azure Site Recovery.
4. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute el programa de instalación del proveedor. La clave será válida durante cinco días a partir del momento en que se genera.
5. Descargue el agente de Recovery Services.

    ![Descargar](./media/tutorial-hyper-v-vmm-to-azure/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Instalación del proveedor en el servidor VMM

1. En el Asistente para la instalación del proveedor de Azure Site Recovery > **Microsoft Update**, decida usar Microsoft Update para comprobar las actualizaciones del proveedor.
2. En **Instalación**, acepte la ubicación predeterminada de instalación para el proveedor y haga clic en **Instalar**. 
3. Después de la instalación, en el Asistente de registro de Microsoft Azure Site Recovery > **Configuración del almacén**, haga clic en **Examinar** y, en **Archivo de clave**, seleccione el archivo de clave del almacén que descargó.
4. Especifique la suscripción de Azure Site Recovery y el nombre del almacén (**ContosoVMVault**). Especifique un nombre descriptivo para identificar el servidor VMM en el almacén.
5. En **Configuración de proxy**, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
6. Acepte la ubicación predeterminada del certificado que se usa para cifrar los datos. Los datos cifrados se descifrarán cuando se realice la conmutación por error.
7. En **Sincronizar metadatos en la nube**, seleccione **Sincronizar los metadatos de la nube con el portal Site Recovery**. Esta acción solo se debe ejecutar una vez en cada servidor. Luego, haga clic en **Registrar**.
8. Una vez que el servidor se haya registrado en el almacén, haga clic en **Finalizar**.

Después de que finalice el registro, los metadatos del servidor se recuperan mediante Azure Site Recovery y el servidor VMM se muestra en **Site Recovery Infrastructure** (Infraestructura de Site Recovery).

### <a name="install-the-recovery-services-agent"></a>Instalación del agente de Recovery Services

Instale el agente en cada host de Hyper-V que contiene las máquinas virtuales que desea replicar.

1. En el Asistente para la instalación de Agente de Microsoft Azure Recovery Services > **Comprobación de requisitos previos**, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.
2. En **Configuración de la instalación**, acepte la ubicación de instalación y la ubicación de la memoria caché. La unidad de caché necesita al menos 5 GB de almacenamiento. Se recomienda una unidad con 600 GB o más de espacio disponible. Luego haga clic en **Instalar**.
3. En **Instalación**, cuando finalice la instalación, haga clic en **Cerrar** para finalizar el asistente.

    ![Instalación del agente](./media/tutorial-hyper-v-vmm-to-azure/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

1. Haga clic en **Preparar infraestructura** > **Destino**.
2. Seleccione la suscripción y el grupo de recursos (**ContosoRG**)donde se crearán las máquinas virtuales de Azure después de la conmutación por error.
3. Seleccione el modelo de implementación de **Resource Manager**.

Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.


## <a name="configure-network-mapping"></a>Configurar asignación de red

1. En **Infraestructura de Site Recovery** > **Asignaciones de red** > **Asignación de red**, haga clic en el icono de **+Asignación de red**.
2. En **Agregar asignación de red**, seleccione el servidor VMM de origen. Seleccione **Azure** como destino.
3. Compruebe la suscripción y el modelo de implementación después de la conmutación por error.
4. En **Red de origen**, seleccione la red de máquinas virtuales de origen local.
5. En **Red de destino**, seleccione la red de Azure en la que se ubicarán las máquinas virtuales de Azure replicadas cuando estas se creen después de la conmutación por error. A continuación, haga clic en **Aceptar**.

    ![Asignación de red](./media/tutorial-hyper-v-vmm-to-azure/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurar una directiva de replicación

1. Haga clic en **Preparar infraestructura** > **Configuración de la replicación** > **+Crear y asociar**.
2. En **Crear y asociar directiva**, especifique un nombre de directiva, **ContosoReplicationPolicy**.
3. Deje los valores predeterminados y haga clic en **Aceptar**.
    - **Frecuencia de copia** indica que los datos diferenciales (después de la replicación inicial) se replicarán cada cinco minutos.
    - **Retención de punto de recuperación** indica que los períodos de retención para cada punto de recuperación serán de dos horas.
    - **Frecuencia de instantánea coherente con la aplicación** indica que cada hora se crearán puntos de recuperación que contengan instantáneas coherentes con la aplicación.
    - **Hora de inicio de la replicación inicial** indica que la replicación inicial comenzará de inmediato.
    - **Cifrar datos almacenados en Azure**: la configuración predeterminada **Off** (Desactivado) indica que los datos en reposo de Azure no están cifrados.
4. Una vez que se cree la directiva, haga clic en **Aceptar**. Cuando se crea una nueva directiva se asocia automáticamente con la nube de VMM.

## <a name="enable-replication"></a>Habilitar replicación

1. En **Replicate application** (Replicar aplicación), haga clic en **Origen**. 
2. En **Origen**, seleccione la nube de VMM. A continuación, haga clic en **Aceptar**.
3. En **Destino**, compruebe que Azure aparece como el destino, la suscripción del almacén y seleccione el modelo de **Resource Manager**.
4. Seleccione la cuenta de almacenamiento **contosovmsacct1910171607** y la red de Azure **ContosoASRnet**.
5. En **Máquinas virtuales** > **Seleccionar**, seleccione la máquina virtual que desea replicar. A continuación, haga clic en **Aceptar**.

 Puede hacer un seguimiento del progreso de la acción **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. Una vez concluido el trabajo **Finalizar protección**, la replicación inicial finaliza y la máquina virtual está preparada para la conmutación por error.


## <a name="next-steps"></a>pasos siguientes
[Exploración de la recuperación ante desastres](tutorial-dr-drill-azure.md)
