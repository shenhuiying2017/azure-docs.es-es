---
title: Referencia de host.json para Azure Functions
description: Documentación de referencia para el archivo host.json de Azure Functions.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: tdykstra
ms.openlocfilehash: d1dec6f2da4f6fcbeb38585fc6a1cfcd9d622c4a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764594"
---
# <a name="hostjson-reference-for-azure-functions"></a>Referencia de host.json para Azure Functions

El archivo de metadatos *host.json* contiene las opciones de configuración global que afectan a todas las funciones de dicha aplicación de función. Este artículo incluye una lista de las opciones de configuración disponibles. El esquema JSON está en http://json.schemastore.org/host.

Hay otras opciones de configuración global en el archivo en la [configuración de la aplicación](functions-app-settings.md) y en el archivo [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Archivo host.json de ejemplo

El siguiente archivo *host.json* de ejemplo tiene especificadas todas las opciones posibles.

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Las siguientes secciones de este artículo explican cada propiedad de nivel superior. Todas son opcionales, a menos que se indique lo contrario.

## <a name="aggregator"></a>aggregator

Especifica cuántas llamadas a funciones se agregan cuando se [calculan las métricas para Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Propiedad |Valor predeterminado  | DESCRIPCIÓN |
|---------|---------|---------| 
|batchSize|1000|Número máximo de solicitudes para agregar.| 
|flushTimeout|00:00:30|Período máximo de tiempo para agregar.| 

Las llamadas a funciones se agregan cuando se alcanza el primero de los dos límites.

## <a name="applicationinsights"></a>applicationInsights

Controla la [característica de muestreo de Application Insights](functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------| 
|isEnabled|true|Habilita o deshabilita el muestreo.| 
|maxTelemetryItemsPerSecond|5|Umbral donde comienza el muestreo.| 

## <a name="durabletask"></a>durableTask

Configuración de [Durable Functions](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey"
  }
}
```

Los nombres de la central de tareas deben empezar por una letra y estar formados únicamente por letras y números. Si no se especifica, el nombre predeterminado de la central de tareas de la aplicación de función es **DurableFunctionsHub**. Para más información, consulte el artículo sobre las [centrales de tareas](durable-functions-task-hubs.md).

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------|
|HubName|DurableFunctionsHub|Se pueden usar nombres de [central de tareas](durable-functions-task-hubs.md) alternativos para aislar varias aplicaciones de Durable Functions unas de otras, incluso si usan el mismo back-end de almacenamiento.|
|ControlQueueBatchSize|32|El número de mensajes que se van a extraer a la vez de la cola de control.|
|PartitionCount |4|El recuento de particiones para la cola de control. Puede ser un entero positivo comprendido entre 1 y 16.|
|ControlQueueVisibilityTimeout |5 minutos|El tiempo de espera de visibilidad de los mensajes de la cola de control quitados de la cola.|
|WorkItemQueueVisibilityTimeout |5 minutos|El tiempo de espera de visibilidad de los mensajes de la cola de elementos de trabajo quitados de la cola.|
|MaxConcurrentActivityFunctions |10 veces el número de procesadores en la máquina actual.|El número máximo de funciones de actividad que se pueden procesar simultáneamente en una única instancia de host.|
|MaxConcurrentOrchestratorFunctions |10 veces el número de procesadores en la máquina actual.|El número máximo de funciones de actividad que se pueden procesar simultáneamente en una única instancia de host.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|El nombre de la configuración de aplicación que tiene la cadena de conexión de Azure Storage que se usa para administrar los recursos subyacentes de Azure Storage.|
|TraceInputsAndOutputs |false|Un valor que indica si se realizará el seguimiento de las entradas y salidas de las llamadas de función. El comportamiento predeterminado al realizar el seguimiento de eventos de ejecución de funciones es incluir el número de bytes en las entradas y salidas serializadas de las llamadas de función. Este comportamiento proporciona información mínima sobre el aspecto de las entradas y salidas, sin sobredimensionar los registros o exponer por accidente información confidencial a los registros. Al establecer esta propiedad en true, el registro de funciones predeterminado registra todo el contenido de las entradas y salidas de función.|
|EventGridTopicEndpoint ||La dirección URL de un punto de conexión de tema personalizado de Azure Event Grid. Cuando se establece esta propiedad, se publican eventos de notificación del ciclo de vida de orquestación en este punto de conexión.|
|EventGridKeySettingName ||El nombre de la configuración de aplicación que contiene la clave usada para autenticarse con el tema personalizado de Azure Event Grid en `EventGridTopicEndpoint`.

Muchas de estas propiedades son para optimizar el rendimiento. Para más información, consulte [Rendimiento y escalado](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Opciones de configuración para los [desencadenadores y enlaces del Centro de eventos](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Lista de las funciones que el host de trabajo ejecutará. Una matriz vacía significa ejecutar todas las funciones. Su uso está previsto solo cuando se [ejecuta localmente](functions-run-local.md). En las aplicaciones de función, use la propiedad *function.json*`disabled` propiedad en lugar de esta propiedad en *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la duración del tiempo de espera para todas las funciones. En los planes de consumo, el intervalo válido es de 1 segundo a 10 minutos, y el valor predeterminado es 5 minutos. En los planes de App Service, no hay ningún límite y el valor predeterminado es null, lo que indica que no hay tiempo de espera.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuración del [monitor de estado de host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------| 
|Enabled|true|Si está habilitada la característica. | 
|healthCheckInterval|10 segundos|El intervalo de tiempo entre las comprobaciones periódicas de mantenimiento en segundo plano. | 
|healthCheckWindow|2 minutes|Una ventana de tiempo deslizante usada en combinación con el valor `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de veces que puede producirse un error en la comprobación de mantenimiento antes de que se inicie un reciclaje del host.| 
|counterThreshold|0.80|El umbral en el que un contador de rendimiento se considerará incorrecto.| 

## <a name="http"></a>http

Opciones de configuración para los [desencadenadores y enlaces HTTP](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Identificador único de un host de trabajo. Puede ser un GUID en minúsculas sin guiones. Requerido cuando se realiza la ejecución localmente. Cuando se ejecuta en Azure Functions, se genera automáticamente un identificador si `id` se omite.

Si comparte una cuenta de almacenamiento entre varias aplicaciones de función, asegúrese de que cada aplicación de función tiene otro `id`. Puede omitir la propiedad `id` o establecer manualmente el `id` de cada aplicación de función en un valor diferente. El desencadenador de temporizador utiliza un bloqueo de almacenamiento para asegurarse de que solo hay una instancia de temporizador cuando una aplicación de función se escala a varias instancias. Si dos aplicaciones de función comparten el mismo `id` y cada una usa un desencadenador de temporizador, solo se ejecutará un temporizador.


```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Controla el filtrado de los registros escritos por un [objeto ILogger](functions-monitoring.md#write-logs-in-c-functions) o [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------| 
|categoryFilter|N/D|Especifica el filtrado por categoría| 
|defaultLevel|Información|Para las categorías no especificadas en la matriz `categoryLevels`, envía registros en este nivel y superiores a Application Insights.| 
|categoryLevels|N/D|Matriz de categorías que especifica el nivel de registro mínimo para enviar a Application Insights para cada categoría. La categoría especificada aquí controla todas las categorías que comienzan por el mismo valor, y los valores más largos tienen prioridad. En el archivo *host.json* del ejemplo anterior, todas las categorías que comienzan por "Host.Aggregator" se registran en el nivel `Information`. Todas las demás categorías que comienzan por "Host", como "Host.Executor", se registran en el nivel `Error`.| 

## <a name="queues"></a>queues

Opciones de configuración para los [desencadenadores y enlaces de la cola de Storage](functions-bindings-storage-queue.md).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Opción de configuración para los [desencadenadores y enlaces de Service Bus](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Opciones de configuración para el comportamiento de bloqueo Singleton. Para más información, consulte [problema de compatibilidad de GitHub con singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------| 
|lockPeriod|00:00:15|Período durante el cual se producen los bloqueos de nivel de función. Los bloqueos se renuevan automáticamente.| 
|listenerLockPeriod|00:01:00|Período durante el cual se producen los bloqueos de agente de escucha.| 
|listenerLockRecoveryPollingInterval|00:01:00|Intervalo de tiempo que se utiliza para la recuperación de bloqueos del agente de escucha si no se pudo adquirir un bloqueo de agente de escucha durante el inicio.| 
|lockAcquisitionTimeout|00:01:00|Cantidad máxima de tiempo que el entorno en tiempo de ejecución intentará adquirir un bloqueo.| 
|lockAcquisitionPollingInterval|N/D|Intervalo entre intentos de adquisición de bloqueo.| 

## <a name="tracing"></a>tracing

Opciones de configuración para los registros que se crean mediante un objeto `TraceWriter`. Consulte [Registro de C#](functions-reference-csharp.md#logging) y [Registro de Node.js](functions-reference-node.md#writing-trace-output-to-the-console). 

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propiedad  |Valor predeterminado | DESCRIPCIÓN |
|---------|---------|---------| 
|consoleLevel|info|Nivel de seguimiento para el registro de la consola. Las opciones son: `off`, `error`, `warning`, `info` y `verbose`.|
|fileLoggingMode|debugOnly|Nivel de seguimiento para el registro de archivos. Las opciones son `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Conjunto de [directorios de código compartido](functions-reference-csharp.md#watched-directories) en los que se deben supervisar los cambios.  Garantiza que cuando se cambie el código en estos directorios, las funciones recibirán los cambios.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Vea cómo actualizar el archivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte las opciones de configuración global en las variables de entorno](functions-app-settings.md)
