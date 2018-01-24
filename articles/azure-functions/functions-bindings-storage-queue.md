---
title: Enlaces de Azure Queue Storage para Azure Functions
description: "Obtenga información acerca de cómo usar enlaces de salida y desencadenador de Azure Queue Storage en Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 2ca511bf0c145878cc80bdbae694f581fd487820
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Enlaces de Azure Queue Storage para Azure Functions

En este artículo se explica cómo trabajar con enlaces de Azure Queue Storage en Azure Functions. Azure Functions admite enlaces de salida y desencadenador para colas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Desencadenador

Use el desencadenador de cola para iniciar una función cuando se reciba un nuevo elemento en una cola. El mensaje de la cola se proporciona a modo de entrada para la función.

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que sondea la cola `myqueue-items` y escribe un registro cada vez que se procesa un elemento de cola.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa el enlace. La función sondea la cola `myqueue-items` y escribe un registro cada vez que se procesa un elemento de cola.

Este es el archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de script de C#:

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

En la sección acerca del [uso](#trigger---usage) se explica `myQueueItem`, que recibe el nombre de la propiedad `name` de function.json.  En la [sección de metadatos del mensaje](#trigger---message-metadata) se explican el resto de variables que se muestran.

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de blob en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función sondea la cola `myqueue-items` y escribe un registro cada vez que se procesa un elemento de cola.

Este es el archivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

En la sección de [configuración](#trigger---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

En la sección acerca del [uso](#trigger---usage) se explica `myQueueItem`, que recibe el nombre de la propiedad `name` de function.json.  En la [sección de metadatos del mensaje](#trigger---message-metadata) se explican el resto de variables que se muestran.

## <a name="trigger---attributes"></a>Desencadenador: atributos
 
Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use los siguientes atributos para configurar un desencadenador de cola:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  El constructor del atributo toma el nombre de la cola que debe supervisar, tal como se muestra en el ejemplo siguiente:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Para un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Proporciona otra manera de especificar la cuenta de almacenamiento que se debe usar. El constructor toma el nombre de una configuración de aplicación que contiene una cadena de conexión de almacenamiento. El atributo se puede aplicar en el nivel de clase, método o parámetro. En el ejemplo siguiente se muestran el nivel de clase y de método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

La cuenta de almacenamiento que se debe usar se determina en el orden siguiente:

* La propiedad `Connection` del atributo `QueueTrigger`.
* El atributo `StorageAccount` aplicado al mismo parámetro que el atributo `QueueTrigger`.
* El atributo `StorageAccount` aplicado a la función.
* El atributo `StorageAccount` aplicado a la clase.
* La configuración de aplicación "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `QueueTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D| Se debe establecer en `queueTrigger`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección**| N/D | Solo en el archivo *function.json*. Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D |Nombre de la variable que representa la cola en el código de la función.  | 
|**queueName** | **QueueName**| Nombre de la cola que se sondea. | 
|**conexión** | **Connection** |El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Desencadenador: uso
 
En C# y script de C#, acceda a los datos del blob mediante un parámetro de método, como `Stream paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede enlazar a cualquiera de los siguientes tipos:

* Objeto POCO: el tiempo de ejecución de Functions deserializa la carga de JSON en un objeto POCO. 
* `string`
* `byte[]`
* [CloudQueueMessage]

En JavaScript, use `context.bindings.<name>` para tener acceso a la carga del elemento de la cola. Si la carga es JSON, se deserializa en un objeto.

## <a name="trigger---message-metadata"></a>Desencadenador: metadatos del mensaje

El desencadenador de cola proporciona varias propiedades de metadatos. Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Los valores tienen la misma semántica que [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Propiedad|type|DESCRIPCIÓN|
|--------|----|-----------|
|`QueueTrigger`|`string`|Carga de cola (si hay una cadena válida). Si la cola envía la carga como una cadena, `QueueTrigger` tiene el mismo valor que la variable denominada por la propiedad `name` en *function.json*.|
|`DequeueCount`|`int`|Es el número de veces que se ha quitado de la cola este mensaje.|
|`ExpirationTime`|`DateTimeOffset?`|Es la hora de expiración del mensaje.|
|`Id`|`string`|Es el identificador del mensaje de cola.|
|`InsertionTime`|`DateTimeOffset?`|Es la hora en la que el mensaje se agregó a la cola.|
|`NextVisibleTime`|`DateTimeOffset?`|Es la siguiente hora a la que será visible el mensaje.|
|`PopReceipt`|`string`|Es la confirmación de extracción del mensaje.|

## <a name="trigger---poison-messages"></a>Desencadenador: mensajes dudosos

Si se produce un error en una función del desencadenador de cola, Azure Functions volverá a intentar esa función hasta cinco veces para un determinado mensaje en la cola, incluido el primer intento. Si se produce un error en los cinco intentos, Functions agregará un mensaje a la cola denominada *&lt;nombreDeColaOriginal>-poison*. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual.

Para controlar manualmente los mensajes dudosos, compruebe el elemento [dequeueCount](#trigger---message-metadata) del mensaje de la cola.

## <a name="trigger---hostjson-properties"></a>Desencadenador: propiedades de host.json

El archivo [host.json](functions-host-json.md#queues) contiene opciones de configuración que controlan el comportamiento de desencadenador de cola.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Salida

Use el enlace de salida de Azure Queue Storage para escribir mensajes en una cola.

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que crea un mensaje de cola para cada una de las solicitudes HTTP que se reciben.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de blobs en un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa el enlace. La función crea un elemento de cola con una carga POCO para cada una de las solicitudes HTTP recibidas.

Este es el archivo *function.json*:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de script de C# que crea un único mensaje de cola:

```cs
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

Puede enviar varios mensajes a la vez mediante el uso de un parámetro `ICollector` o `IAsyncCollector`. Este es el código de script de C# que envía varios mensajes, uno con los datos de la solicitud HTTP y otro con valores codificados de forma rígida:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de blob en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función crea un elemento de cola para cada una de las solicitudes HTTP recibidas.

Este es el archivo *function.json*:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

En la sección de [configuración](#output---configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Puede enviar varios mensajes a la vez mediante la definición de una matriz de mensajes para el enlace de salida `myQueueItem`. El código JavaScript envía dos mensajes de cola con valores codificados de forma rígida para cada una de las solicitudes HTTP recibidas.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Salida: atributos
 
Para [bibliotecas de clases de C#](functions-dotnet-class-library.md), use [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

El atributo se aplica a un parámetro `out` o al valor de retorno de la función. El constructor del atributo toma el nombre de la cola, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Para un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte [Desencadenador: atributos](#trigger---attribute).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Queue`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `queue`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Se debe establecer en `out`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa la cola en el código de la función. Se establece en `$return` para hacer referencia al valor devuelto de la función.| 
|**queueName** |**QueueName** | Nombre de la cola. | 
|**conexión** | **Connection** |El nombre de una configuración de aplicación que contiene la cadena de conexión de almacenamiento que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre aquí. Por ejemplo, si establece `connection` en "MyStorage", el entorno en tiempo de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyStorage". Si deja `connection` vacía, el entorno en tiempo de ejecución de Functions usa la cadena de conexión de almacenamiento predeterminada en la configuración de aplicación que se denomina `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas
 
En C# y script de C#, escriba un mensaje de cola único mediante un parámetro de método como `out T paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede usar el tipo de valor devuelto del método en lugar de un parámetro `out`, y `T` puede ser cualquiera de los siguientes tipos:

* Un objeto POCO serializable como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

En C# y script de C#, escriba varios mensajes de cola mediante uno de los siguientes tipos: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

En las funciones de JavaScript, use `context.bindings.<name>` para tener acceso al mensaje de cola de salida. Puede usar una cadena o un objeto JSON serializable para la carga del elemento de cola.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Ir a un inicio rápido que use un desencadenador de Queue Storage](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Ir a un tutorial que usa un enlace de salida de Queue Storage](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
