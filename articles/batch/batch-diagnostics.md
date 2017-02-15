---
title: "Registro de diagnóstico de Azure Batch | Microsoft Docs"
description: "Registre y analice los eventos de registro de diagnóstico de los recursos de la cuenta de Azure Batch como tareas y grupos."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 01/20/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dfcf1e1d54a0c04cacffb50eca4afd39c6f6a1b1
ms.openlocfilehash: f1aa23d2865ec8401a59b2370c8157d2458c60f7


---
# <a name="azure-batch-diagnostic-logging"></a>Registro de diagnóstico de Azure Batch
Como ocurre con muchos de los servicios de Azure, el servicio Batch emite eventos de registro para determinados recursos mientras dure el recurso. Puede habilitar los registros de diagnóstico de Azure Batch para registrar los eventos de los recursos, como los grupos y las tareas y, después, utilizar los registros para la evaluación y supervisión de diagnóstico. En los registros de diagnóstico de Batch se incluyen eventos como la creación de grupos, la eliminación de grupos, el inicio de tareas y la finalización de tareas, entre otros.

> [!NOTE]
> En este artículo se explica el registro de eventos para los propios recursos de la cuenta de Batch, no lo datos de salida de tareas y trabajos. Para más información sobre el almacenamiento de los datos de salida de trabajos y tareas, consulte [Almacenamiento de la salida de trabajos y tareas de Azure Batch](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* [Una cuenta de Azure Batch](batch-account-create-portal.md)
* [Una cuenta de Azure Storage](../storage/storage-create-storage-account.md#create-a-storage-account)
  
  Para conservar los registros de diagnóstico de Batch, es preciso crear la cuenta de Azure Storage en la que Azure va a almacenar los registros. Especifique esta cuenta de Storage cuando [habilite el registro de diagnóstico](#enable-diagnostic-logging) para su cuenta de Batch. La cuenta de Storage que especifique al habilitar la recopilación de registros no es la misma que la cuenta de almacenamiento vinculada a la que se hace referencia en los artículos sobre [paquetes de aplicación](batch-application-packages.md) y [persistencia de salida de tarea](batch-task-output.md).
  
  > [!WARNING]
  > Se le **cobra** por los datos almacenados en su cuenta de Azure Storage. Aquí se incluyen los registros de diagnóstico que se describen en este artículo. Téngalo en cuenta al diseñar su [directiva de retención de registro](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Activación del registro de diagnóstico
El registro de diagnóstico no está habilitado de manera predeterminada en la cuenta de Batch. Por consiguiente, es preciso habilitarlo explícitamente en cada cuenta de Batch que se desea supervisar:

[Cómo habilitar la recopilación de registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Se recomienda leer toda el artículo [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para conocer no solo cómo habilitar el registro, sino también las categorías de registro que admiten los diversos servicios de Azure. Por ejemplo, actualmente Azure Batch admite una categoría de registro: **registros de servicio**.

## <a name="service-logs"></a>Registros de servicios
Los registros de servicio de Azure Batch contienen los eventos emitidos por Azure Batch mientras dure un recurso de Batch como un grupo o una tarea. Cada evento que emite Batch se almacena en la cuenta de Storage especificada en formato JSON. Por ejemplo, este es el cuerpo de un **evento de creación de grupos** de ejemplo:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Cada cuerpo del evento reside en un archivo .json de la cuenta de Azure Storage especificada. Si desea acceder directamente a los registros, puede revisar el [esquema de registros de diagnóstico en la cuenta de almacenamiento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventos del registro del servicio
Actualmente, el servicio Batch emite los siguientes eventos del registro del servicio. Es posible que la lista no sea exhaustiva, ya que se pueden haber agregado eventos adicionales desde la última vez que se actualizó este artículo.

| **Eventos del registro del servicio** |
| --- |
| [Creación de grupos][pool_create] |
| [Inicio de eliminación de grupo][pool_delete_start] |
| [Finalización de eliminaciones de grupo][pool_delete_complete] |
| [Inicio de cambio de tamaño de grupo][pool_resize_start] |
| [Finalización de cambio de tamaño de grupo][pool_resize_complete] |
| [Inicio de tarea][task_start] |
| [Tarea completada][task_complete] |
| [Error en la tarea][task_fail] |

## <a name="next-steps"></a>Pasos siguientes
Además de almacenar los eventos de registro de diagnóstico en una cuenta de Azure Storage, los eventos del registro del servicio Batch también se pueden transmitir a un [Centro de eventos de Azure](../event-hubs/event-hubs-what-is-event-hubs.md) y enviarlos a [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Transmita registros de diagnóstico de Azure a centros de eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Transmita eventos de diagnóstico de Batch al servicio de entrada de datos altamente escalable, Event Hubs. Event Hubs puede ingerir millones de eventos por segundo, que posteriormente se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real.
* [Análisis de los registros de diagnóstico de Azure mediante Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)
  
  Envíe los registros de diagnóstico a Log Analytics, donde puede analizarlos en el portal de Operations Management Suite (OMS), o bien exportarlos para su análisis en Power BI o Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx



<!--HONumber=Dec16_HO2-->


