---
title: Enlaces de Queue Storage en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure en funciones de Azure."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f39f674bf576a2661a0e03710b9005b0515b3aa5
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="azure-functions-queue-storage-bindings"></a>Enlaces de Queue Storage en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se describe cómo configurar enlaces de Azure Queue Storage y codificarlos en Azure Functions. Azure Functions admite enlaces de desencadenador y salida para colas de Azure. Para las características que estén disponibles en todos los enlaces, consulte [conceptos sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Desencadenador de Queue Storage
El desencadenador de Azure Queue Storage permite supervisar si hay mensajes nuevos en una cola de almacenamiento y reaccionar ante ellos. 

Defina un desencadenador de cola mediante la pestaña **Integrar** del portal de Functions. El portal crea la siguiente definición en la sección de **enlaces** de *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* La propiedad `connection` debe contener el nombre de una configuración de aplicación que contenga una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación al seleccionar una cuenta de almacenamiento.

Se pueden proporcionar opciones de configuración adicionales en un archivo [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) para ajustar aún más los desencadenadores de Queue Storage. Por ejemplo, puede cambiar el intervalo de sondeo de cola de host.json.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Uso de un desencadenador de cola
En las funciones de Node.js, acceda a los datos de cola mediante `context.bindings.<name>`.


En las funciones de .NET, acceda a la carga útil de la cola mediante un parámetro de método como `CloudQueueMessage paramName`. Aquí, `paramName` es el valor que especificó en la [configuración del desencadenador](#trigger). El mensaje de la cola se puede deserializar en cualquiera de los siguientes tipos:

* Objeto POCO. Se debe usar si la carga útil de la cola es un objeto JSON. El entorno en tiempo de ejecución de Functions deserializa la carga útil en el objeto POCO. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadatos de desencadenador de cola
El desencadenador de cola proporciona varias propiedades de metadatos. Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Los valores tienen la misma semántica que [`CloudQueueMessage`].

* **QueueTrigger**: carga de cola (si hay una cadena válida)
* **DequeueCount**: escriba `int`. Es el número de veces que se ha quitado de la cola este mensaje.
* **ExpirationTime**: escriba `DateTimeOffset?`. Es la hora de expiración del mensaje.
* **Id**: escriba `string`. Es el identificador del mensaje de cola.
* **InsertionTime**: escriba `DateTimeOffset?`. Es la hora en la que el mensaje se agregó a la cola.
* **NextVisibleTime**: tipo `DateTimeOffset?`. Es la siguiente hora a la que será visible el mensaje.
* **PopReceipt**: escriba `string`. Es la confirmación de extracción del mensaje.

Consulte cómo usar los metadatos de la cola en la sección [Ejemplo de desencadenador](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente elemento function.json que define un desencadenador de cola:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Consulte el ejemplo de lenguaje específico que permite recuperar y registrar los metadatos de la cola.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Ejemplo de desencadenador en C# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Ejemplo de desencadenador en Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Control de mensajes dudosos en la cola
Si se produce un error en una función del desencadenador de cola, Azure Functions volverá a intentar esa función hasta cinco veces para un determinado mensaje en la cola, incluido el primer intento. Si se produce un error en los cinco intentos, Functions agregará un mensaje a la cola de almacenamiento llamada *&lt;nombreDeColaOriginal>-poison*. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual. 

Para controlar manualmente los mensajes dudosos, compruebe el elemento `dequeueCount` del mensaje de la cola (consulte [Metadatos de desencadenador de cola](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Enlace de salida de Queue Storage
El enlace de salida de Azure Queue Storage le permite escribir mensajes en una cola. 

Defina un enlace de salida de cola mediante la pestaña **Integrar** del portal de Functions. El portal crea la siguiente definición en la sección de **enlaces** de *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* La propiedad `connection` debe contener el nombre de una configuración de aplicación que contenga una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación al seleccionar una cuenta de almacenamiento.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Uso de un enlace de salida de cola
En las funciones de Node.js, accede a la cola de salida mediante `context.bindings.<name>`.

En las funciones de .NET, puede enviar a la salida cualquiera de los siguientes tipos. Si hay algún parámetro de tipo `T`, `T` debe ser uno de los tipos de salida admitidos, como `string` o un elemento POCO.

* `out T` (serializado como JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

También puede usar el tipo de valor devuelto del método como enlace de salida.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Ejemplo de salida de cola
El siguiente elemento *function.json* define un desencadenador HTTP con un enlace de salida de cola:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Consulte el ejemplo específico del lenguaje que envía a la salida un mensaje de cola con la carga útil HTTP entrante.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Ejemplo de salida de cola en C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Para enviar varios mensajes, use un elemento `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Ejemplo de salida de cola en Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

O, para enviar varios mensajes,

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes

Para ver un ejemplo de una función que usa desencadenadores y enlaces de Queue Storage, consulte [Creación de una instancia de Azure Functions conectada a un servicio de Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage

