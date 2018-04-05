---
title: Creación y personalización de los planes de recuperación para la conmutación por error y recuperación en Azure Site Recovery | Microsoft Docs
description: Aprenda a crear y personalizar planes de recuperación en Azure Site Recovery. En este artículo se describe cómo conmutar por error y recuperar máquinas virtuales y servidores físicos.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-customize-recovery-plans"></a>Creación y personalización de los planes de recuperación

En este artículo se describe cómo crear y personalizar un plan de recuperación en [Azure Site Recovery](site-recovery-overview.md). Antes de comenzar, [obtenga más información](recovery-plan-overview.md) sobre los planes de recuperación.

## <a name="create-a-recovery-plan"></a>Creación de un plan de recuperación

1. En el almacén de Recovery Services, seleccione **Planes de recuperación (Site Recovery)** > **+Plan de recuperación**.
2. En **Crear plan de recuperación**, especifique un nombre para el plan.
3. Elija un origen y un destino en función de las máquinas del plan y seleccione **Resource Manager** para el modelo de implementación. La ubicación de origen debe tener máquinas habilitadas para conmutación por error y recuperación. 

   **Conmutación por error** | **Origen** | **Destino** 
   --- | --- | ---
   Azure a Azure | Región de Azure |Región de Azure
   VMware en Azure | Servidor de configuración | Azure
   Máquinas físicas a Azure | Servidor de configuración | Azure   
   Hyper-V administrado por VMM en Azure  | Nombre para mostrar de VMM | Azure
   Hyper-V sin VMM en Azure | Nombre del sitio Hyper-V | Azure
   VMM a VMM |Nombre descriptivo de VMM | Nombre para mostrar de VMM 

   > [!NOTE]
   > Un plan de recuperación puede contener máquinas que tengan el mismo origen y destino. Las máquinas virtuales de Hyper-V y VMware administradas por VMM no pueden estar en el mismo plan. Las máquinas virtuales de VMware y los servidores físicos pueden estar en el mismo plan, donde el origen es un servidor de configuración.

2. En **Seleccionar máquinas virtuales**, seleccione las máquinas (o el grupo de replicación) que desea agregar al plan. A continuación, haga clic en **Aceptar**.
    - Las máquinas se agregan al grupo predeterminado (Grupo 1) del plan. Después de la conmutación por error, todas las máquinas de este grupo se inician al mismo tiempo.
    - Solo puede seleccionar las máquinas que se encuentran en las ubicaciones de origen y destino que especificó. 
1. Haga clic en **Aceptar** para crear el plan.

## <a name="add-a-group-to-a-plan"></a>Incorporación de un grupo a un plan

Se crean grupos adicionales y se agregan máquinas a los distintos grupos para que pueda especificar un comportamiento distinto grupo a grupo. Por ejemplo, puede especificar cuándo las máquinas de un grupo se deben iniciar después de la conmutación por error, o bien especificar las acciones personalizadas por grupo.

1. En **Planes de recuperación**, haga clic con el botón derecho en el plan > **Personalizar**. De manera predeterminada, después de crear un plan, todas las máquinas que agregó al plan se ubican de manera predeterminada en el Grupo 1.
2. Haga clic en **+Grupo**. De manera predeterminada, un grupo nuevo se enumera según el orden en que se agrega. Puede tener hasta siete grupos.
3. Seleccione la máquina que desea mover al grupo nuevo, haga clic en **Cambiar grupo** y luego seleccione el grupo nuevo. También puede hacer clic con el botón derecho en el nombre del grupo > **Elemento protegido**, y agregar máquinas en el grupo. Una máquina o un grupo de replicación solo puede pertenecer a un grupo en un plan de recuperación.


## <a name="add-a-script-or-manual-action"></a>Incorporación de un script o una acción manual

Puede personalizar un plan de recuperación si agrega un script o una acción manual. Observe lo siguiente:

- Si va a replicar a Azure, puede integrar runbooks de automatización de Azure en el plan de recuperación. [Más información](site-recovery-runbook-automation.md).
- Si replica máquinas virtuales de Hyper-V administradas por System Center VMM, puede crear un script en el servidor VMM local e incluirlo en el plan de recuperación.
- Cuando agrega un script, este agrega un nuevo conjunto de acciones al grupo. Por ejemplo, se crea un conjunto de pasos previos del grupo 1 con el nombre *Grupo 1: pasos previos*. Dentro de este conjunto se enumeran todos los pasos previos. Solo puede agregar un script en el sitio principal si hay un servidor VMM implementado.
- Si agrega una acción manual, cuando se ejecute el plan de recuperación, se detendrá en el punto en que se insertó la acción manual. En un cuadro de diálogo, se le pide que especifique que se completó la acción manual.
- Para crear un script en el servidor VMM, siga las instrucciones que aparecen en [este artículo](hyper-v-vmm-recovery-script.md).
- Los scripts se pueden aplicar durante la conmutación por error en el sitio secundario y durante la conmutación por recuperación del sitio secundario al sitio principal. La compatibilidad depende del escenario de replicación:
    
    **Escenario** | **Conmutación por error** | **Conmutación por recuperación**
    --- | --- | --- 
    Azure a Azure  | Runbook | Runbook
    VMware en Azure | Runbook | N/D 
    Hyper-V con VMM en Azure | Runbook | Script
    Sitio de Hyper-V a Azure | Runbook | N/D
    VMM en VMM secundario | Script | Script

1. En el plan de recuperación, haga clic en el paso al que se debe agregar la acción y especifique cuándo se debe producir la acción. Si desea que la acción se produzca antes de que se inicien las máquinas del grupo después de la conmutación por error, seleccione **Add pre-action** (Agregar acción previa).
    b. Si desea que la acción se produzca antes de que se inicien las máquinas del grupo después de la conmutación por error, seleccione **Add post action** (Agregar acción posterior). Para mover la posición de la acción, seleccione los botones **Move Up** (Subir) o **Move Down** (Bajar).
2. En **Insert action** (Insertar acción), seleccione **Script** o **Manual action** (Acción manual).
3. Si quiere agregar una acción manual, haga lo siguiente. Escriba un nombre para la acción e instrucciones para la misma. La persona que ejecuta la conmutación por error verá estas instrucciones.
    b. Especifique si desea agregar la acción manual para todos los tipos de conmutación por error: prueba, conmutación por error, conmutación por error planeada (si corresponde). A continuación, haga clic en **Aceptar**.
4. Si quiere agregar un script, haga lo siguiente. Si va a agregar un script de VMM, seleccione **Conmutación por error en script de VMM** y en **Ruta del script** escriba la ruta de acceso relativa al recurso compartido. Por ejemplo, si el recurso compartido está en \\<VMMServerName>\MSSCVMMLibrary\RPScripts, especifique la ruta de acceso: \RPScripts\RPScript.PS1.
    b. Si va a agregar un runbook de automatización de Azure, especifique la **cuenta de Azure Automation** en que se encuentra el runbook y seleccione el valor de **Script de runbook de Azure** adecuado.
5. Ejecute una conmutación por error de prueba del plan de recuperación para garantizar que el script funciona según lo esperado.

## <a name="watch-a-video"></a>Ver un vídeo

Vea un vídeo que muestra cómo crear un plan de recuperación.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Pasos siguientes

Aprenda más sobre la [ejecución de conmutaciones por error](site-recovery-failover.md).  

    
