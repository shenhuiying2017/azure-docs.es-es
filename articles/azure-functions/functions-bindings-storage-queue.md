---
title: Enlaces de colas de Storage en Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar desencadenadores y enlaces de almacenamiento de Azure en funciones de Azure."
services: functions
documentationcenter: na
author: christopheranderson
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
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


---
# <a name="azure-functions-storage-queue-bindings"></a>Enlaces de colas de Storage en Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de colas de Azure Storage en Azure Functions. Azure Functions admite enlaces de desencadenador y salida para colas de Azure Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Desencadenador de cola de Azure Storage
El desencadenador de cola de Azure Storage permite supervisar si hay mensajes nuevos en una cola de almacenamiento y reaccionar ante ellos. 

El desencadenador de cola de Azure Storage para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, puede definir esta configuración de aplicación en la pestaña **Integrar** al crear una cuenta de almacenamiento o seleccionar una existente. Para crear manualmente esta configuración de la aplicación, consulte [Manage App Service settings](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings) (Administración de la configuración de App Service).

Se pueden proporcionar [opciones de configuración adicionales](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en un archivo host.json para ajustar aún más los desencadenadores de colas de almacenamiento.  

### <a name="handling-poison-queue-messages"></a>Control de mensajes dudosos en la cola
Si se produce un error en una función del desencadenador de cola, Azure Functions volverá a intentar esa función hasta cinco veces para un determinado mensaje en la cola, incluido el primer intento. Si se produce un error en los cinco intentos, Functions agregará un mensaje a una cola de Storage denominada *&lt;nombreDeColaOriginal>-poison*. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual. 

Para administrar manualmente los mensajes dudosos, puede conocer el número de veces que un mensaje se recogió para su procesamiento mediante la comprobación de `dequeueCount` (consulte [Metadatos de desencadenador de cola](#meta)).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
En las funciones de C#, puede enlazar con el mensaje de entrada mediante un parámetro con nombre en la firma de la función, como `<T> <name>`.
Donde `T` es el tipo de datos en el que desea deserializar los datos, y `paramName` es el nombre que especificó en el [enlace de desencadenador](#trigger). En las funciones de Node.js, puede acceder a los datos de entrada del blob mediante `context.bindings.<name>`.

El mensaje de la cola se puede deserializar en cualquiera de los siguientes tipos:

* [Objeto](https://msdn.microsoft.com/library/system.object.aspx): usado para mensajes serializados de JSON. Cuando se declara un tipo de entrada personalizado, el sistema en tiempo de ejecución intenta deserializar el objeto JSON. 
* string
* Byte array
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx) (solo C#)

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Metadatos de desencadenador de cola
Puede obtener metadatos de la cola en la función mediante estos nombres de variable:

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (otra manera de recuperar el texto del mensaje en cola como una cadena)

Consulte cómo usar los metadatos de la cola en la sección [Ejemplo de desencadenador](#triggersample)

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente function.json que define un desencadenador de cola de Storage:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
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
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
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

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Enlace de salida de la cola de Storage
El enlace de salida de cola de Azure Storage le permite escribir mensajes en una cola de Azure Storage en su función. 

La salida de cola de Azure Storage para una función utiliza los siguientes objetos JSON en la matriz `bindings` de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection`: debe contener el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. En Azure Portal, el editor estándar de la pestaña **Integrar** permite modificar esta configuración de aplicación cuando crea una cuenta de Azure Storage o selecciona una ya existente. Para crear manualmente esta configuración de la aplicación, consulte [Manage App Service settings](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings) (Administración de la configuración de App Service).

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de salidas
En las funciones de C#, puede escribir un mensaje de entrada mediante el parámetro `out` con nombre en la firma de la función, como `out <T> <name>`. En este caso, `T` es el tipo de datos en el que desea serializar el mensaje, y `paramName` es el nombre que especificó en el [enlace de salida](#output). En las funciones de Node.js, puede acceder a la salida mediante `context.bindings.<name>`.

Puede generar un mensaje de cola mediante cualquiera de los tipos de datos del código:

* Cualquier [objeto](https://msdn.microsoft.com/library/system.object.aspx): `out MyCustomType paramName`  
Se usa para la serialización de JSON.  Cuando se declara un tipo de salida personalizado, el sistema en tiempo de ejecución intenta serializar el objeto JSON. Si el parámetro de salida es nulo cuando sale la función, el sistema en tiempo de ejecución crea un mensaje de cola como objeto nulo.
* Cadena: `out string paramName`  
Se utiliza para los mensajes de prueba. El sistema en tiempo de ejecución solo genera un mensaje si el parámetro de cadena no es nulo cuando la función sale.
* Matriz de bytes: `out byte[]` 

Se admiten estos tipos de salida adicionales en una función de C#:

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx): `out CloudQueueMessage` 
* `ICollector<T>`o `IAsyncCollector<T>`, donde `T` es uno de los tipos admitidos.

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Suponga que tiene el siguiente objeto function.json que define un [desencadenador de cola de Storage](functions-bindings-storage-queue.md), una entrada y una salida de blob de Storage:

Ejemplo de *function.json* con un enlace de salida de la cola de almacenamiento que usa un desencadenador manual y escribe la entrada en un mensaje de cola:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Consulte el ejemplo de lenguaje específico que permite escribir un mensaje de la cola de salida para cada mensaje de la cola de entrada.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Ejemplo de salida en C# #

```cs
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

O, para enviar varios mensajes,

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Ejemplo de salida en Node.js

```javascript
module.exports = function(context) {
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
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

Para ver un ejemplo de una función que usa desencadenadores y enlaces de la cola de Storage, consulte el artículo sobre la [creación de una instancia de Azure Functions conectada a un servicio de Azure](functions-create-an-azure-connected-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


