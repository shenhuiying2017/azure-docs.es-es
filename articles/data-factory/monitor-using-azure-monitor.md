---
title: "Supervisión de factorías de datos mediante Azure Monitor | Microsoft Docs"
description: "Aprenda a utilizar Azure Monitor para supervisar las canalizaciones de factorías de datos mediante la habilitación de registros de diagnóstico con la información de Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: cae3c797171c3904f100ae3cdec47a31b06d3b31
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Supervisión de factorías de datos mediante Azure Monitor | Microsoft Docs  
Las aplicaciones de nube son complejas y tienen muchas partes móviles. La supervisión proporciona datos para garantizar que la aplicación permanece en funcionamiento en un estado correcto. También ayuda a evitar posibles problemas o a solucionar los existentes. Además, puede usar datos de supervisión para obtener un conocimiento más profundo sobre su aplicación. Este conocimiento puede ayudarle a mejorar el rendimiento o mantenimiento de la aplicación, o a automatizar acciones que de lo contrario requerirían intervención manual.

Azure Monitor ofrece registros y métricas de infraestructuras a nivel básico para la mayoría de los servicios de Microsoft Azure. Para más información, consulte la [introducción a la supervisión](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Los registros de diagnóstico de Azure son registros emitidos por un recurso que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. Data Factory da como resultado registros de diagnóstico en Azure Monitor. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Supervisión y administración de canalizaciones en Data Factory, versión 1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Registros de diagnóstico

* Guardarlos en una **cuenta de almacenamiento** para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) mediante la configuración de diagnóstico.
* Transmitirlos a **Event Hubs** para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.
* Analícelos con **Log Analytics en Operations Management Suite (OMS)**

Puede usar una cuenta de almacenamiento o un espacio de nombres de centro de eventos que no esté en la misma suscripción que el recurso que emite los registros. El usuario que configura los ajustes debe tener el control de acceso basado en rol (RBAC) adecuado a ambas suscripciones.

## <a name="set-up-diagnostic-logs"></a>Configuración de registros de diagnósticos

### <a name="diagnostic-settings"></a>Configuración de diagnóstico
Los registros de diagnóstico para recursos no de proceso se configuran mediante Configuración de diagnóstico. Configuración de diagnóstico para un control de recurso:

* Dónde se envían los registros de diagnóstico (cuenta de Storage, Event Hubs o Log Analytics de OMS).
* Qué categorías de registro se envían.
* Cuánto tiempo se debe conservar cada categoría de registro en una cuenta de almacenamiento
* Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.
* Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de Event Hubs u OMS), las directivas de retención no surten ningún efecto.
* Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Habilitación de los registros de diagnóstico mediante las API de REST

Creación o actualización de una configuración de diagnóstico en la API de REST de Azure Monitor

**Solicitud**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Encabezados**
* Reemplace `{api-version}` por `2016-09-01`.
* Reemplace `{resource-id}` por el identificador del recurso para el que desea editar la configuración de diagnóstico. Para más información, consulte el artículo sobre el [uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-portal.md).
* Establezca el encabezado `Content-Type` en `application/json`.
* Establezca el encabezado de autorización en un token web de JSON que se obtiene de Azure Active Directory. Par más información, consulte [Solicitudes de autenticación](../active-directory/develop/active-directory-authentication-scenarios.md).

**Cuerpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Propiedad | type | DESCRIPCIÓN |
| --- | --- | --- |
| storageAccountId |string | El identificador de recurso de la cuenta de almacenamiento en la que le gustaría enviar los registros de diagnóstico |
| serviceBusRuleId |string | El identificador de regla de Service Bus para el espacio de nombres de Service Bus donde desea que se creen las instancias de Event Hub creadas por los registros de diagnóstico de streaming. El identificador de regla tiene el formato: "{Id. de recurso de Service Bus}/authorizationrules/{nombre de clave}".|
| workspaceId | Tipo complejo | Matriz de intervalos de agregación de métricas y sus directivas de retención. Actualmente, esta propiedad está vacía. |
|Métricas| Valores de parámetros de la ejecución de canalización que se pasan a la canalización invocada| Un objeto JSON que asigna nombres de parámetro a los valores de argumento | 
| logs| Tipo complejo| Nombre de una categoría de registro de diagnóstico para un tipo de recurso. Para obtener la lista de categorías de registro de diagnóstico para un recurso, realice primero una operación de configuración de diagnóstico GET. |
| categoría| string| Matriz de las categorías de registro y sus directivas de retención |
| timeGrain | string | La granularidad de las métricas que se capturan en formato de duración ISO 8601. Debe ser PT1M (un minuto).|
| Enabled| boolean | Especifica si la colección de esa categoría de métrica o registro está habilitada para este recurso.|
| retentionPolicy| Tipo complejo| Describe la directiva de retención para una categoría de métrica o registro. Se utiliza solamente para la opción de cuenta de almacenamiento.|
| days| int| Número de días para retener las métricas o registros. Con el valor cero, se retienen los registros indefinidamente. Se utiliza solamente para la opción de cuenta de almacenamiento. |

**Respuesta**

200 OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Permite obtener información sobre la configuración de diagnóstico en la API de REST de Azure Monitor.

**Solicitud**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Encabezados**
* Reemplace `{api-version}` por `2016-09-01`.
* Reemplace `{resource-id}` por el identificador del recurso para el que desea editar la configuración de diagnóstico. Para más información, consulte el artículo sobre el uso de grupos de recursos para administrar los recursos de Azure.
* Establezca el encabezado `Content-Type` en `application/json`.
* Establezca el encabezado de autorización en un token web de JSON que se obtiene de Azure Active Directory. Para más información, consulte Solicitudes de autenticación.

**Respuesta**

200 OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Más información aquí] (https://msdn.microsoft.com/en-us/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Esquema de registros y eventos

### <a name="activity-run-logs-attributes"></a>Atributos de registros de ejecución de actividad

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propiedad | type | DESCRIPCIÓN | Ejemplo |
| --- | --- | --- | --- |
| Nivel |string | Nivel de los registros de diagnóstico. El nivel 4 siempre es el caso de los registros de ejecución de actividad. | `4`  |
| correlationId |string | Identificador único para realizar el seguimiento de una solicitud determinada completa | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| Twitter en tiempo | string | Hora del evento de intervalo de tiempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| string| Identificador de la ejecución de la actividad | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| string| Identificador de la ejecución de canalización | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| string | Identificador de recurso asociado para el recurso de la factoría de datos | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoría| string | Categoría de los registros de diagnóstico. Establezca esta propiedad en "ActivityRuns". | `ActivityRuns` |
|level| string | Nivel de los registros de diagnóstico. Establezca esta propiedad en "Informational". | `Informational` |
|operationName| string |Nombre de la actividad con estado. Si el estado es el latido de inicio, es `MyActivity -`. Si el estado es el latido final, es `MyActivity - Succeeded` con estado final | `MyActivity - Succeeded` |
|pipelineName| string | Nombre de la canalización | `MyPipeline` |
|activityName| string | Nombre de la actividad | `MyActivity` |
|start| string | Inicio de la ejecución de la actividad en el intervalo de tiempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Finaliza la ejecución de la actividad en el intervalo de tiempo, formato UTC. Si la actividad no ha finalizado todavía (registro de diagnóstico para una actividad de inicio), se establece un valor predeterminado de `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Atributos de registros de ejecución de canalización

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propiedad | type | DESCRIPCIÓN | Ejemplo |
| --- | --- | --- | --- |
| Nivel |string | Nivel de los registros de diagnóstico. El nivel 4 es el caso de los registros de ejecución de actividad. | `4`  |
| correlationId |string | Identificador único para realizar el seguimiento de una solicitud determinada completa | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| Twitter en tiempo | string | Hora del evento de intervalo de tiempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| string| Identificador de la ejecución de canalización | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| string | Identificador de recurso asociado para el recurso de la factoría de datos | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoría| string | Categoría de los registros de diagnóstico. Establezca esta propiedad en "PipelineRuns". | `PipelineRuns` |
|level| string | Nivel de los registros de diagnóstico. Establezca esta propiedad en "Informational". | `Informational` |
|operationName| string |Nombre de la canalización con estado. "Pipeline - Succeeded" (Canalización - Correcto) con estado final cuando se ha completado la ejecución de canalización| `MyPipeline - Succeeded` |
|pipelineName| string | Nombre de la canalización | `MyPipeline` |
|start| string | Inicio de la ejecución de la actividad en el intervalo de tiempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Final de las ejecuciones de la actividad en el intervalo de tiempo, formato UTC. Si la actividad no ha finalizado todavía (registro de diagnóstico para una actividad de inicio), se establece un valor predeterminado de `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|status| string | Estado final de la ejecución de canalización (correcto o erróneo) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Atributos de registros de ejecución de desencadenador

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Propiedad | type | DESCRIPCIÓN | Ejemplo |
| --- | --- | --- | --- |
| Nivel |string | Nivel de los registros de diagnóstico. Se establece en el nivel 4 para registros de ejecución de actividad. | `4`  |
| correlationId |string | Identificador único para realizar el seguimiento de una solicitud determinada completa | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| Twitter en tiempo | string | Hora del evento de intervalo de tiempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| string| Identificador de la ejecución del desencadenador | `08587023010602533858661257311` |
|ResourceId| string | Identificador de recurso asociado para el recurso de la factoría de datos | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoría| string | Categoría de los registros de diagnóstico. Establezca esta propiedad en "PipelineRuns". | `PipelineRuns` |
|level| string | Nivel de los registros de diagnóstico. Establezca esta propiedad en "Informational". | `Informational` |
|operationName| string |Nombre del desencadenador con estado final si activa correctamente. "MyTrigger - Succeeded" (MyTrigger - Correcto) si el latido se realizó correctamente.| `MyTrigger - Succeeded` |
|triggerName| string | Nombre del desencadenador | `MyTrigger` |
|triggerType| string | Tipo del desencadenador (desencadenador manual o desencadenador de programación) | `ScheduleTrigger` |
|triggerEvent| string | Evento del desencadenador | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| string | Inicio de la activación del desencadenador en el intervalo de tiempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| string | Estado final de si el desencadenador se ha activado correctamente (correcto o erróneo) | `Succeeded`|

### <a name="metrics"></a>Métricas

Azure Monitor permite utilizar telemetría para obtener información sobre el rendimiento y el estado de las cargas de trabajo en Azure. El tipo de telemetría de datos de Azure más importante son las métricas (también denominadas contadores de rendimiento) emitidas por la mayoría de los recursos de Azure. Azure Monitor proporciona varias maneras de configurar y usar estas métricas para supervisar y solucionar problemas.

ADFV2 emite las siguientes métricas:

| **Métrica**           | **Nombre de métrica para mostrar**         | **Unidad** | **Tipo de agregación** | **Descripción**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Las métricas de ejecuciones de canalización se realizaron correctamente | Recuento    | Total                | Total de ejecuciones de canalizaciones realizadas correctamente dentro de una ventana de minutos |
| PipelineFailedRuns   | Métricas de ejecuciones de canalización erróneas    | Recuento    | Total                | Total de ejecuciones de canalizaciones erróneas dentro de una ventana de minutos    |
| ActiviySucceededRuns | Métricas de ejecución de actividad realizadas correctamente | Recuento    | Total                | Total de ejecuciones de actividad realizadas correctamente dentro de una ventana de minutos  |
| ActivityFailedRuns   | Métricas de ejecuciones de actividad erróneas    | Recuento    | Total                | Total de ejecuciones de actividad erróneas dentro de una ventana de minutos     |
| TriggerSucceededRuns | Métricas de ejecuciones de desencadenador realizadas correctamente  | Recuento    | Total                | Total de ejecuciones de desencadenador realizadas correctamente dentro de una ventana de minutos   |
| TriggerFailedRuns    | Métricas de ejecuciones de desencadenador erróneas     | Recuento    | Total                | Total de ejecuciones de desencadenador erróneas dentro de una ventana de minutos      |

Para acceder a las métricas, siga las instrucciones del artículo https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics. 

## <a name="next-steps"></a>pasos siguientes
Consulte el artículo [Supervisión y administración de canalizaciones](monitor-programmatically.md) para obtener información sobre la supervisión y administración de canalizaciones mediante programación. 