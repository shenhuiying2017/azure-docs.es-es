---
title: Ver los registros de actividad de Azure con Log Analytics | Microsoft Docs
description: "Puede usar la solución Registros de actividad de Azure para analizar y buscar en el registro de actividad de Azure de todas las suscripciones de Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 1ad56a54f094f3c314596b3a7c9fecd09647d065
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="view-azure-activity-logs"></a>Ver los registros de actividad de Azure

![Símbolo de registros de actividad de Azure](./media/log-analytics-activity/activity-log-analytics.png)

La solución Activity Log Analytics ayuda a analizar y buscar en el [registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) de todas las suscripciones de Azure. El registro de actividad de Azure es un registro que proporciona información sobre las operaciones realizadas en los recursos de las suscripciones. Antes el registro de actividad se conocía como *Registros de auditoría* o *Registros operativos*, ya que notifica eventos de las suscripciones.

Con el registro de actividad se pueden determinar los interrogantes *qué*, *quién* y *cuándo* de las operaciones de escritura (PUT, POST, DELETE) realizadas para los recursos de la suscripción. También permite entender el estado de las operaciones y otras propiedades relevantes. El registro de actividad no incluye las operaciones de lectura (GET) ni las operaciones para los recursos que usan el modelo de implementación clásica.

Cuando se conectan los registros de actividad de Azure a Log Analytics, es posible:

- Analizar los registros de actividad con vistas predefinidas
- Analizar y buscar en los registros de actividad de varias suscripciones de Azure
- Conservar registros de actividad durante más de 90 días<sup>1</sup>
- Correlacionar registros de actividad con otros datos de plataforma y aplicación de Azure
- Ver las actividades operativas agregadas por estado
- Ver las tendencias de las actividades que se producen en cada uno de los servicios de Azure
- Informar sobre los cambios de autorización en todos los recursos de Azure
- Identificar problemas de corte de suministro o estado del servicio que afecten a los recursos
- Usar la búsqueda de registros para correlacionar las actividades del usuario, las operaciones de escalado automático, los cambios de autorización y el estado del servicio con otros registros o métricas del entorno

<sup>1</sup>De forma predeterminada, Log Analytics conserva los registros de actividad de Azure durante 90 días, incluso si está en el nivel Gratis. O bien, si tiene una configuración de retención de área de trabajo de menos de 90 días. Si el área de trabajo tiene una retención de más de 90 días, los registros de actividad se conservan durante el período de retención del área de trabajo.

Log Analytics recopila registros de actividad de forma gratuita y los almacena durante 90 días también de forma gratuita. Si almacena registros durante más de 90 días, incurrirá en gastos de retención de datos para los datos almacenados durante más de ese periodo.

Si está en el nivel de precios Gratis, los registros de actividad no se aplican al consumo de datos diario.

## <a name="connected-sources"></a>Orígenes conectados

A diferencia de la mayoría de las demás soluciones Log Analytics, los agentes no recopilan datos de los registros de actividad. Todos los datos usados por la solución proceden directamente de Azure.

| Origen conectado | Compatible | Descripción |
| --- | --- | --- |
| [Agentes de Windows](log-analytics-windows-agents.md) | No | La solución no recopila información de los agentes de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | No | La solución no recopila información de los agentes de Linux. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | No | La solución no recopila información de los agentes de un grupo de administración SCOM conectado. |
| [Cuenta de Azure Storage](log-analytics-azure-storage.md) | No | La solución no recopila información de Azure Storage. |

## <a name="prerequisites"></a>Requisitos previos

- Para acceder a la información del registro de actividad de Azure, debe tener una suscripción de Azure.

## <a name="configuration"></a>Configuración

Realice los pasos siguientes para configurar la solución Log Analytics para las áreas de trabajo.

1. Habilite la solución Activity Log Analytics desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) o mediante el proceso descrito en [Add Log Analytics solutions from the Solutions Gallery (Agregar soluciones Log Analytics desde la Galería de soluciones)](log-analytics-add-solutions.md).
2. Configure los registros de actividad para ir al área de trabajo de Log Analytics.
    1. En Azure Portal, seleccione el área de trabajo y haga clic en **Registro de actividad de Azure**.
    2. Haga clic en el nombre de cada suscripción.  
        ![agregar suscripción](./media/log-analytics-activity/add-subscription.png)
    3. En la hoja *SubscriptionName*, haga clic en **Conectar**.  
        ![conectar suscripción](./media/log-analytics-activity/subscription-connect.png)

Si agrega la solución mediante el portal de OMS, verá el icono siguiente. Inicie sesión en Azure Portal para conectar una suscripción de Azure al área de trabajo.  
![realizar una evaluación](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>Uso de la solución

Al agregar la solución Log Analytics al área de trabajo, el icono **Registros de actividad de Azure** se agrega al panel Información general. Este icono muestra un recuento del número de registros de actividad de Azure de las suscripciones de Azure a las que tiene acceso la solución.

![Icono Registros de actividad de Azure](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Ver los registros de actividad de Azure

Haga clic en el icono **Registros de actividad de Azure** para abrir el panel **Registros de actividad de Azure**. El panel incluye las hojas de la tabla siguiente. Cada hoja muestra hasta diez elementos que coinciden con los criterios de esa hoja para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que devuelva todos los registros si hace clic en **Ver todo** en la parte inferior de la hoja o si hace clic en el encabezado de esta.

Los datos del registro de actividad solo aparecen *después* de haber configurado los registros de actividad para ir a la solución, así que antes de eso no se pueden ver.

| Hoja | Descripción |
| --- | --- |
| Entradas del registro de actividad de Azure | Muestra un gráfico de barras de los principales totales de registro de entrada de registro de actividad del intervalo de fechas seleccionado y una lista de los 10 principales llamadores de actividad. Haga clic en el gráfico de barras para ejecutar una búsqueda de registros para <code>Type=AzureActivity</code>. Haga clic en un elemento de llamador para ejecutar una búsqueda de registros que devuelva todas las entradas de registro de actividad de ese elemento. |
| Registros de actividad por estado | Muestra un gráfico de anillos del estado de registro de actividad de Azure para el intervalo de fechas seleccionado. También muestra una lista de los diez principales registros de estado. Haga clic en el gráfico para ejecutar una búsqueda de registros para <code>Type=AzureActivity &#124; measure count() by ActivityStatus</code>. Haga clic en un elemento de estado para ejecutar una búsqueda de registros que devuelva todas las entradas de registro de actividad de ese registro de estado. |
| Registros de actividad por recurso | Muestra el número total de recursos con registros de actividad y enumera los diez principales recursos con recuentos de registro para cada recurso. Haga clic en la superficie total para ejecutar una búsqueda de registros para <code>Type=AzureActivity &#124; measure count() by Resource</code>, que muestra todos los recursos de Azure disponibles para la solución. Haga clic en un recurso para ejecutar una búsqueda de registros que devuelva todos los registros de actividad de ese recurso. |
| Registros de actividad por proveedor de recursos | Muestra el número total de proveedores de recursos que producen registros de actividad y enumera los diez principales. Haga clic en la superficie total para ejecutar una búsqueda de registros para <code>Type=AzureActivity &#124; measure count() by ResourceProvider</code>, que muestra todos los proveedores de recursos de Azure. Haga clic en un proveedor de recursos para ejecutar una búsqueda de registros que devuelva todos los registros de actividad para el proveedor. |

![Panel Registros de actividad de Azure](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>Pasos siguientes

- Cree una [alerta](log-analytics-alerts-creating.md) cuando se produzca una actividad determinada.
- Use [Búsqueda de registros](log-analytics-log-searches.md) para ver información detallada de los registros de actividad.
