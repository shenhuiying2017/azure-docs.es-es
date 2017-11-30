---
title: Referencia de host.json para Azure Functions
description: "Documentación de referencia para el archivo host.json de Azure Functions."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2017
ms.author: tdykstra
ms.openlocfilehash: 522d0590595b0fc0fef503599f1677658f223bd8
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
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

|Propiedad  |Valor predeterminado | Descripción |
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

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|isEnabled|false|Habilita o deshabilita el muestreo.| 
|maxTelemetryItemsPerSecond|5|Umbral donde comienza el muestreo.| 

## <a name="eventhub"></a>eventHub

Opciones de configuración para los [desencadenadores y enlaces del Centro de eventos](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Lista de las funciones que el host de trabajo ejecutará.  Una matriz vacía significa ejecutar todas las funciones.  Su uso está previsto solo cuando se [ejecuta localmente](functions-run-local.md). En las aplicaciones de función, use la propiedad *function.json*`disabled` propiedad en lugar de esta propiedad en *host.json*.

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

## <a name="http"></a>http

Opciones de configuración para los [desencadenadores y enlaces HTTP](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

Identificador único de un host de trabajo. Puede ser un GUID en minúsculas sin guiones. Requerido cuando se realiza la ejecución localmente. Cuando se ejecuta en Azure Functions, se genera automáticamente un identificador si `id` se omite.

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

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|categoryFilter|N/D|Especifica el filtrado por categoría| 
|defaultLevel|Información|Para las categorías no especificadas en la matriz `categoryLevels`, envía registros en este nivel y superiores a Application Insights.| 
|categoryLevels|N/D|Matriz de categorías que especifica el nivel de registro mínimo para enviar a Application Insights para cada categoría. La categoría especificada aquí controla todas las categorías que comienzan por el mismo valor, y los valores más largos tienen prioridad. En el archivo *host.json* del ejemplo anterior, todas las categorías que comienzan por "Host.Aggregator" se registran en el nivel `Information`. Todas las demás categorías que comienzan por "Host", como "Host.Executor", se registran en el nivel `Error`.| 

## <a name="queues"></a>queues

Opciones de configuración para los [desencadenadores y enlaces de la cola de Storage](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|maxPollingInterval|60000|Intervalo máximo, en milisegundos, entre sondeos de la cola.| 
|visibilityTimeout|0|Intervalo de tiempo entre los reintentos cuando se produce un error al procesar un mensaje.| 
|batchSize|16|Número de mensajes en cola para recuperar y procesar en paralelo. El valor máximo es 32.| 
|maxDequeueCount|5|Número de veces que se intenta procesar un mensaje antes de pasarlo a la cola de mensajes dudosos.| 
|newBatchThreshold|batchSize/2|Umbral en el que se debe capturar un nuevo lote de mensajes.| 

## <a name="servicebus"></a>serviceBus

Opción de configuración para los [desencadenadores y enlaces de Service Bus](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Opciones de configuración para el comportamiento de bloqueo Singleton. Para más información, consulte [problema de compatibilidad de GitHub con singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
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

|Propiedad  |Valor predeterminado | Descripción |
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

## <a name="durabletask"></a>durableTask

Nombre de la [central de tareas](durable-functions-task-hubs.md) para [Durable Functions](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Los nombres de la central de tareas deben empezar por una letra y estar formados únicamente por letras y números. Si no se especifica, el nombre predeterminado de la central de tareas de la aplicación de función es **DurableFunctionsHub**. Para más información, consulte el artículo sobre las [centrales de tareas](durable-functions-task-hubs.md).


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Vea cómo actualizar el archivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte las opciones de configuración global en las variables de entorno](functions-app-settings.md)
