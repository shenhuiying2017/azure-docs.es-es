---
title: "Visualización de dependencia en Azure Migrate | Microsoft Docs"
description: "En este artículo se proporciona una introducción a los cálculos de evaluación del servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 10bc249aa70852dce71c96e46319925cececdd8b
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="dependency-visualization"></a>Visualización de dependencia

El servicio [Azure Migrate](migrate-overview.md) evalúa los grupos en las máquinas locales para su migración a Azure. Para agrupar máquinas, puede utilizar la visualización de dependencias. En este artículo se proporciona información sobre esta característica.


## <a name="overview"></a>Información general

La visualización de dependencias en Azure Migrate le permite crear grupos para evaluar la migración con mayor confianza. Mediante la visualización de dependencias se pueden visualizar las dependencias de red de máquinas específicas o a través de un grupo de máquinas. Esto es útil para garantizar que no se pierda (u olviden las máquinas) ninguna funcionalidad en el proceso de migración, cuando las aplicaciones y cargas de trabajo se ejecutan en varias máquinas.  

## <a name="how-does-it-work"></a>¿Cómo funciona?

Azure Migrate utiliza la solución [Service Map](../operations-management-suite/operations-management-suite-service-map.md) en [Log Analytics](../log-analytics/log-analytics-overview.md) para la visualización de dependencias.
- Al crear un proyecto de Azure Migration, se crea un área de trabajo de Log Analytics de OMS en su suscripción.
- El nombre del área de trabajo es el nombre especificado para el proyecto de migración, prefijado con **migrate-** y, opcionalmente, sufijado con un número. 
- Navegue hasta el área de trabajo de Log Analytics desde la sección **Essentials** de la página **Introducción** del proyecto.
- El área de trabajo creada se etiqueta con la clave **MigrateProject** y con el valor **nombre de proyecto**. Se pueden utilizar para buscar en Azure Portal.  

    ![Área de trabajo de Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Para usar la visualización de dependencias, debe descargar e instalar agentes en cada máquina local que vaya a analizar.  

## <a name="do-i-need-to-pay-for-it"></a>¿Tengo qué pagar por ello?

Sí. El área de trabajo de Log Analytics se crea de forma predeterminada, pero no se utiliza a menos que utilice la visualización de dependencias en Azure Migrate. Si usa la visualización de dependencias, o el área de trabajo fuera de Azure Migrate, se le cobrará dicho uso.  [Aprenda más](https://azure.microsoft.com/pricing/details/insight-analytics/) sobre el precio de la solución Service Map. 

## <a name="how-do-i-manage-the-workspace"></a>¿Cómo administro el área de trabajo?

Puede usar el área de trabajo de Log Analytics fuera de Azure Migrate. No se elimina si se elimina el proyecto de migración en el que se creó. Si ya no necesita el área de trabajo, [elimínelo](../log-analytics/log-analytics-manage-access.md) manualmente.

No elimine el área de trabajo creado por Azure Migrate, a menos que elimine el proyecto de migración. Si lo hace, las dependencias no funcionarán como se esperaba.

## <a name="next-steps"></a>Pasos siguientes

[Agrupación de máquinas con dependencias de máquina](how-to-create-group-machine-dependencies.md)