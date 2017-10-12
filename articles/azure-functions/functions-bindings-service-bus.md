---
title: Enlaces y desencadenadores de Service Bus en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de Azure Service Bus en Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Enlaces de Service Bus en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y trabajar con enlaces de Azure Service Bus en Azure Functions. 

Azure Functions admite enlaces de desencadenador y salida para colas y temas de Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Desencadenador de Service Bus
Utilice el desencadenador de Service Bus para responder a mensajes de una cola o tema de Service Bus. 

Los desencadenadores de cola y tema de Service Bus se definen mediante los siguientes objetos JSON en la matriz `bindings` de function.json:

* Desencadenador de *cola*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* Desencadenador de *tema*:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Tenga en cuenta lo siguiente:

* Para `connection`, [cree una configuración de aplicación en la aplicación de la función](functions-how-to-use-azure-function-app-settings.md) que contenga la cadena de conexión al espacio de nombres de Service Bus y después especifique el nombre de la configuración de la aplicación en la propiedad `connection` en el desencadenador. Obtenga la cadena de conexión siguiendo los pasos mostrados en [Obtención de las credenciales de administración](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  La cadena de conexión debe ser para un espacio de nombres de Service Bus y no estar limitada a una cola o un tema concretos.
  Si `connection` se deja vacío, el desencadenador asume que se especifica una cadena de conexión de Service Bus predeterminada en una configuración de aplicación con el nombre `AzureWebJobsServiceBus`.
* Para `accessRights`, los valores disponibles son `manage` y `listen`. El valor predeterminado es `manage`, lo que indica que `connection` tiene el permiso **Administrar**. Si usa una cadena de conexión que no tiene el permiso **Administrar**, establezca `accessRights` en `listen`. De lo contrario, el runtime de Functions puede intentar realizar operaciones que requieran derechos de administración y no conseguirlo.

## <a name="trigger-behavior"></a>Comportamiento de un desencadenador
* **Subprocesamiento único**: De forma predeterminada, el runtime de Functions procesa simultáneamente varios mensajes en cola. Para indicar al runtime que procese los mensajes de la cola o del tema de uno en uno, establezca `serviceBus.maxConcurrentCalls` en 1 en el archivo *host.json*. 
  Para más información acerca de *host.json*, consulte [Estructura de carpetas](functions-reference.md#folder-structure) y [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Gestión de mensajes dudosos**: Service Bus realiza su propio tratamiento de mensajes dudosos, que no se puede controlar ni configurar en el código ni en la configuración de Azure Functions. 
* **Comportamiento de PeekLock**: El sistema en tiempo de ejecución de Funciones recibe un mensaje en el [modo `PeekLock`](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) y llama a `Complete` en el mensaje si la función finaliza correctamente, o bien llama a `Abandon` si se produce un error en la función. 
  Si la ejecución de la función dura más que el tiempo de espera de `PeekLock`, el bloqueo se renovará automáticamente.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
En esta sección se muestra cómo utilizar el desencadenador enlace de entrada de Service Bus en el código de función. 

En C# y F#, el mensaje de desencadenador de Service Bus se puede deserializar en cualquiera de los siguientes tipos de entrada:

* `string`: útil para mensajes de cadena
* `byte[]`: útil para datos binarios
* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): útil para datos serializados mediante JSON.
  Si declara un tipo de entrada personalizado (como, `CustomType`), Azure Functions intentará deserializar los datos JSON en el tipo especificado.
* `BrokeredMessage`: proporciona el mensaje deserializado con el método [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

En Node.js, el mensaje de desencadenador de Service Bus se pasa a la función como una cadena o como un objeto JSON.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente código function.json:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Consulte el ejemplo específico del lenguaje que procesa un mensaje de cola de Service Bus.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Ejemplo de desencadenador en C# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Ejemplo de desencadenador en F# #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Ejemplo de desencadenador en Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Enlace de salida de Service Bus
La salida de cola y tema de Service Bus para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

* Salida de *cola*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* Salida de *tema*:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Tenga en cuenta lo siguiente:

* Para `connection`, [cree una configuración de aplicación en la aplicación de la función](functions-how-to-use-azure-function-app-settings.md) que contenga la cadena de conexión al espacio de nombres de Service Bus y después especifique el nombre de la configuración de la aplicación en la propiedad `connection` en el enlace de salida. Obtenga la cadena de conexión siguiendo los pasos mostrados en [Obtención de las credenciales de administración](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  La cadena de conexión debe ser para un espacio de nombres de Service Bus y no estar limitada a una cola o un tema concretos.
  Si `connection` se deja vacío, el enlace de salida asume que se especifica una cadena de conexión de Service Bus predeterminada en una configuración de aplicación con el nombre `AzureWebJobsServiceBus`.
* Para `accessRights`, los valores disponibles son `manage` y `listen`. El valor predeterminado es `manage`, lo que indica que `connection` tiene el permiso **Administrar**. Si usa una cadena de conexión que no tiene el permiso **Administrar**, establezca `accessRights` en `listen`. De lo contrario, el runtime de Functions puede intentar realizar operaciones que requieran derechos de administración y no conseguirlo.

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de salidas
En C# y F#, Azure Functions puede crear un mensaje de cola de Service Bus de cualquiera de los siguientes tipos:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): la definición de parámetro es similar a `out T paramName` (C#).
  Functions deserializa el objeto en un mensaje JSON. Si el valor de salida es null cuando finaliza la función, Functions crea el mensaje con un objeto null.
* `string`: la definición de parámetro es similar a `out string paraName` (C#). Si el valor del parámetro es distinto de null cuando finaliza la función, Functions crea un mensaje.
* `byte[]`: la definición de parámetro es similar a `out byte[] paraName` (C#). Si el valor del parámetro es distinto de null cuando finaliza la función, Functions crea un mensaje.
* `BrokeredMessage`: la definición de parámetro es similar a `out BrokeredMessage paraName` (C#). Si el valor del parámetro es distinto de null cuando finaliza la función, Functions crea un mensaje.

Para crear varios mensajes en una función de C#, puede utilizar `ICollector<T>` o `IAsyncCollector<T>`. Se crea un mensaje al llamar al método `Add` .

En Node.js, puede asignar una cadena, una matriz de bytes o un objeto de Javascript (deserializado en JSON) a `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Suponga que tiene el siguiente código function.json que define una salida de cola de Service Bus:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Consulte el ejemplo específico del lenguaje que envía un mensaje a la cola de Service Bus.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Ejemplo de salida en C# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

O bien, cree varios mensajes:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Ejemplo de salida en F# #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Ejemplo de salida en Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

O bien, cree varios mensajes:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

