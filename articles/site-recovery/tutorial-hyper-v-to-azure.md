---
title: "Configuración de la recuperación ante desastres para máquinas virtuales de Hyper-V locales (sin VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales (sin VMM) en Azure con el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31`/2017
ms.author: raynew
ms.openlocfilehash: 633c14bd25bc8a1419196b2c76ca94c26db68991
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este tutorial se muestra cómo configurar la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure. El tutorial se aplica a las máquinas virtuales de Hyper-V no administradas por System Center Virtual Machine Manager (VMM). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Seleccionar el origen y destino de la replicación.
> * Configurar el entorno de replicación de origen, incluidos los componentes de Site Recovery local y el entorno de replicación de destino.
> * Crear una directiva de replicación.
> * Habilitar la replicación para una máquina virtual.

Este es el tercer tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores:

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. [Preparación de un entorno de Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

Antes de empezar, es útil [revisar la arquitectura](concepts-hyper-v-to-azure-architecture.md) de este escenario de recuperación ante desastres.

## <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación


1. En **Todos los servicios** > **Almacenes de Recovery Services**, haga clic en el nombre del almacén preparado en el tutorial anterior, **ContosoVMVault**.
2. En **Introducción**, haga clic en **Site Recovery**. Luego, haga clic en **Preparar infraestructura**.
3. En **Objetivo de protección** > **¿Dónde están ubicadas las máquinas?**, seleccione **Local**.
4. En **¿A dónde quiere replicar las máquinas?**, seleccione **En Azure**.
5. En **¿Las máquinas están virtualizadas?**, seleccione **Sí, con Hyper-V**.
6. En **Are you using System Center VMM** (¿Usa System Center VMM?), seleccione **No**. A continuación, haga clic en **Aceptar**.

    ![Objetivo de replicación](./media/tutorial-hyper-v-to-azure/replication-goal.png)

## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

Para configurar el entorno de origen, agregue los hosts de Hyper-V a un sitio de Hyper-V, descargue e instale el proveedor de Azure Site Recovery y el agente de Azure Recovery Services y registre el sitio de Hyper-V en el almacén. 

1. En **Preparar infraestructura**, haga clic en **Origen**.
2. Haga clic en **+ Sitio Hyper-V** y especifique el nombre del sitio creado en el tutorial anterior, **ContosoHyperVSite**.
3. Haga clic en **+ Hyper-V Server**.
4. Descargue el archivo de instalación del proveedor.
5. Descargue la clave de registro del almacén. Se le solicitará cuando ejecute el programa de instalación del proveedor. La clave será válida durante cinco días a partir del momento en que se genera.

    ![Descarga del proveedor](./media/tutorial-hyper-v-to-azure/download.png)
    

### <a name="install-the-provider"></a>Instalación del proveedor

Ejecute el archivo de instalación del proveedor (AzureSiteRecoveryProvider.exe) en cada host de Hyper-V que agregó al sitio **ContosoHyperVSite**. El archivo instala el proveedor de Azure Site Recovery y el agente de Recovery Services en cada host de Hyper-V.

1. En el Asistente para la instalación del proveedor de Azure Site Recovery > **Microsoft Update**, decida usar Microsoft Update para comprobar las actualizaciones del proveedor.
2. En **Instalación**, acepte la ubicación predeterminada de instalación para el proveedor y el agente y haga clic en **Instalar**.
3. Después de la instalación, en el Asistente de registro de Microsoft Azure Site Recovery > **Configuración del almacén**, haga clic en **Examinar** y, en **Archivo de clave**, seleccione el archivo de clave del almacén que descargó. 
4. Especifique la suscripción de Azure Site Recovery, el nombre del almacén (**ContosoVMVault**) y el sitio de Hyper-V (**ContosoHyperVSite**) al que pertenece el servidor Hyper-V.
5. En **Configuración de proxy**, seleccione **Conectar directamente con Azure Site Recovery sin un servidor proxy**.
6. En **Registro**, una vez que el servidor se haya registrado en el almacén, haga clic en **Finalizar**.

Los metadatos del servidor de Hyper-V se recuperan mediante Azure Site Recovery y el servidor se muestra en **Site Recovery Infrastructure** > **Hyper-V Hosts** (Infraestructura de Site Recovery > Hosts de Hyper-V). Esta operación puede tardar hasta treinta minutos.


## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino. 

1. Haga clic en **Preparar infraestructura** > **Destino**.
2. Seleccione la suscripción y el grupo de recursos **ContosoRG**, donde se crearán las máquinas virtuales de Azure después de la conmutación por error.
3. Seleccione el modelo de implementación de **Resource Manager**.

Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.


## <a name="set-up-a-replication-policy"></a>Configurar una directiva de replicación

1. Haga clic en **Preparar infraestructura** > **Configuración de la replicación** > **+Crear y asociar**.
2. En **Crear y asociar directiva**, especifique un nombre de directiva, **ContosoReplicationPolicy**.
3. Deje los valores predeterminados y haga clic en **Aceptar**.
    - **Frecuencia de copia** indica que los datos diferenciales (después de la replicación inicial) se replicarán cada cinco minutos.
    - **Retención de punto de recuperación** indica que los períodos de retención para cada punto de recuperación serán de dos horas.
    - **Frecuencia de instantánea coherente con la aplicación** indica que cada hora se crearán puntos de recuperación que contengan instantáneas coherentes con la aplicación.
    - **Hora de inicio de la replicación inicial** indica que la replicación inicial comenzará de inmediato.
4. Una vez que se cree la directiva, haga clic en **Aceptar**. Cuando se crea una directiva, se asocia automáticamente con el sitio de Hyper-V especificado (**ContosoHyperVSite**).

    ![Directiva de replicación](./media/tutorial-hyper-v-to-azure/replication-policy.png)


## <a name="enable-replication"></a>Habilitar replicación


1. En **Replicar la aplicación**, haga clic en **Origen**. 
2. En **Origen**, seleccione el sitio **ContosoHyperVSite**. A continuación, haga clic en **Aceptar**.
3. En **Destino**, verifique Azure como el destino, la suscripción del almacén y el modelo de implementación de **Resource Manager**.
4. Seleccione la cuenta de almacenamiento **contosovmsacct1910171607** creada en el tutorial anterior para los datos replicados y la red **ContosoASRnet** en que se ubicarán las máquinas virtuales de Azure después de la conmutación por error.
5. En **Máquinas virtuales** > **Seleccionar**, seleccione la máquina virtual que desea replicar. A continuación, haga clic en **Aceptar**.

 Puede hacer un seguimiento del progreso de la acción **Habilitar protección** en **Trabajos** > **Trabajos de Site Recovery**. Una vez concluido el trabajo **Finalizar protección**, la replicación inicial finaliza y la máquina virtual está preparada para la conmutación por error.

## <a name="next-steps"></a>pasos siguientes
[Exploración de la recuperación ante desastres](tutorial-dr-drill-azure.md)
