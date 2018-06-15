---
title: Supervisión de Azure Batch | Microsoft Docs
description: Más información sobre los servicios de supervisión de Azure, las métricas, los registros de diagnóstico y otras características de supervisión para Azure Batch.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799806"
---
# <a name="monitor-batch-solutions"></a>Supervisión de soluciones de Batch

Azure y el servicio Batch proporcionan una gama de servicios, herramientas y API para supervisar las soluciones de Batch. Este artículo de información general le ayudará a elegir una estrategia de supervisión que se adapte a sus necesidades.

Para información general de los componentes y servicios de Azure disponibles para la supervisión de recursos de Azure, consulte [Supervisión de aplicaciones y recursos de Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Supervisión a nivel de suscripción

El nivel de suscripción, que incluye las cuentas de Batch, el [registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) recopila datos de eventos operativos de [varias categorías](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

Para las cuentas de Batch en concreto, el registro de actividad recopila eventos relacionados con la creación y la eliminación de cuentas, y la administración de claves.

Una manera de recuperar eventos del registro de actividad es usar Azure Portal. Haga clic en **Todos los servicios** > **Registro de actividad**. O consulte los eventos con la CLI de Azure, los cmdlets de PowerShell o la API REST de Azure Monitor. También puede exportar el registro de actividad o configurar [alertas del registro de actividad](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Supervisión a nivel de la cuenta de Batch

Supervise las cuentas de Batch con características de [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor recopila [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) y, opcionalmente, [registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para los recursos del nivel de cuenta de Batch, como grupos, trabajos y tareas. Recopile y consuma estos datos manualmente o mediante programación para supervisar las actividades de la cuenta de Batch y para diagnosticar problemas. Para detalles, consulte [Métricas, alertas y registros de Batch para la evaluación diagnóstica y la supervisión](batch-diagnostics.md).
 
> [!NOTE]
> Las métricas están disponibles de manera predeterminada en la cuenta de Batch sin configuración adicional y su historial se acumula durante 30 días. Debe habilitar el registro de diagnóstico para una cuenta de Batch; puede incurrir en costos adicionales por almacenar o procesar datos del registro de diagnóstico. 

## <a name="batch-resource-monitoring"></a>Supervisión de recursos de Batch

En las aplicaciones de Batch, utilice las API de Batch para supervisar o consultar el estado de los recursos, como los trabajos, las tareas, los nodos y los grupos. Por ejemplo: 

* [Recuento de tareas por estado](batch-get-task-counts.md)
* [Creación de consultas para enumerar los recursos de Batch con eficacia](batch-efficient-list-queries.md)
* [Creación de dependencias de tareas](batch-task-dependencies.md)
* Uso de una [tarea del administrador de trabajos](/rest/api/batchservice/job/add#jobmanagertask)
* Supervisión del [estado de las tareas](/rest/api/batchservice/task/list#taskstate)
* Supervisión del [estado de los nodos](/rest/api/batchservice/computenode/list#computenodestate)
* Supervisión del [estado de los grupos](/rest/api/batchservice/pool/get#poolstate)
* Supervisión del [uso de grupos en la cuenta](/rest/api/batchservice/pool/listusagemetrics)
* [Número de nodos de grupo por estado](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>Contadores de rendimiento de las máquinas virtuales y supervisión de la aplicación

* [Application Insights](../application-insights/app-insights-overview.md) es un servicio de Azure que puede usar para supervisar la disponibilidad, el rendimiento y el uso de los trabajos y las tareas de Batch mediante programación. Obtenga contadores de rendimiento fácilmente de los nodos de proceso (máquinas virtuales) e información personalizada para las tareas fuera de las máquinas virtuales. 

  Para un ejemplo, consulte [Monitor and debug a Batch .NET application with Application Insights](monitor-application-insights.md) (Supervisión y depuración de una aplicación .NET de Batch con Application Insights) y el [código de ejemplo](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) que lo acompaña.

  > [!NOTE]
  > El uso de Application Insights puede suponer costos adicionales. Consulte las [opciones de precios](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [BatchLabs](https://github.com/Azure/BatchLabs) es una herramienta de cliente independiente, completa y gratuita que puede ayudarle a crear, depurar y supervisar aplicaciones de Azure Batch. Descargue un [paquete de instalación](https://azure.github.io/BatchLabs/) para Mac, Linux o Windows. Opcionalmente, configure la solución de Batch para [mostrar datos de Application Insights](https://github.com/Azure/batch-insights), como los contadores de rendimiento de la máquina virtual en BatchLabs.


## <a name="next-steps"></a>Pasos siguientes

* Obtenga información acerca de las [API y herramientas de Batch](batch-apis-tools.md) disponibles para la creación de soluciones de Batch.
* Más información sobre el [registro de diagnóstico](batch-diagnostics.md) con Batch.