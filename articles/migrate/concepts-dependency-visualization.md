---
title: "Visualización de dependencia en Azure Migrate | Microsoft Docs"
description: "En este artículo se proporciona una introducción a los cálculos de evaluación del servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: bcbb2ace6686e4052149a5dde1ed837a16c36bad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
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

Azure Migrate está disponible sin costo adicional. Para utilizar las características de visualización de dependencias de Azure Migrate, es necesario Service Map. Al crear un proyecto de Azure Migrate, Azure Migrate creará automáticamente una nueva área de trabajo de Log Analytics en su nombre.

> [!NOTE]
> La característica de visualización de dependencias usa Service Map mediante un área de trabajo de Log Analytics. Desde el 28 de febrero de 2018, con el anuncio de la disponibilidad general de Azure Migrate, la característica está ahora disponible sin costo adicional. Deberá crear un nuevo proyecto para poder usar el área de trabajo de uso gratuito. Las áreas de trabajo existentes antes de la disponibilidad general siguen siendo facturables, por lo que se recomienda que cambie a un nuevo proyecto.

1. El uso de cualquier solución distinta a Service Map en esta área de trabajo de Log Analytics conlleva los cargos normales de Log Analytics. 
2. Para admitir escenarios de migración sin costo adicional, la solución de Service Map no conllevará costos durante los primeros 180 días desde la creación del proyecto de Azure Migrate, tras lo cual se aplicarán las tarifas normales.
3. Solo el área de trabajo creada como parte de la creación del proyecto se podrá usar de manera gratuita.

Al registrar agentes en el área de trabajo, utilice el identificador y la clave proporcionados por el proyecto en la página de pasos de instalación del agente. No se puede usar un área de trabajo existente y asociarla al proyecto de Azure Migrate.

Al eliminar el proyecto de Azure Migrate, el área de trabajo no se elimina con él. Tras la eliminación del proyecto, el uso de Service Map dejará de ser gratuito y cada nodo se facturará conforme al nivel de pago del área de trabajo de Log Analytics.

[Aquí](https://azure.microsoft.com/pricing/details/azure-migrate/) puede encontrar más información sobre los precios de Azure Migrate. 

## <a name="how-do-i-manage-the-workspace"></a>¿Cómo administro el área de trabajo?

Puede usar el área de trabajo de Log Analytics fuera de Azure Migrate. No se elimina si se elimina el proyecto de migración en el que se creó. Si ya no necesita el área de trabajo, [elimínelo](../log-analytics/log-analytics-manage-access.md) manualmente.

No elimine el área de trabajo creado por Azure Migrate, a menos que elimine el proyecto de migración. Si lo hace, las dependencias no funcionarán como se esperaba.

## <a name="next-steps"></a>Pasos siguientes

[Agrupación de máquinas con dependencias de máquina](how-to-create-group-machine-dependencies.md)