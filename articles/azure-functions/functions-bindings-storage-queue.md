---
title: Enlaces de Azure Queue Storage para Azure Functions
description: Obtenga información acerca de cómo usar enlaces de salida y desencadenador de Azure Queue Storage en Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: tdykstra
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1ca2b821678b48f65fe6ec6e58fa65cd8e4304f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303421"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Enlaces de Azure Queue Storage para Azure Functions

En este artículo se explica cómo trabajar con enlaces de Azure Queue Storage en Azure Functions. Azure Functions admite enlaces de salida y desencadenador para colas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Paquetes

Los enlaces de almacenamiento de Queue se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs). El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src) de GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-package-versions](../../includes/functions-package-versions.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

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

En el ejemplo siguiente se muestra un enlace de desencadenador de cola de un archivo *function.json* y código de [script de C# (.csx)](functions-reference-csharp.md) que usa el enlace. La función sondea la cola `myqueue-items` y escribe un registro cada vez que se procesa un elemento de cola.

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

En el ejemplo siguiente se muestra un enlace de desencadenador de cola de un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función sondea la cola `myqueue-items` y escribe un registro cada vez que se procesa un elemento de cola.

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

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

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

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

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
 
En C# y el script de C#, acceda a los datos de mensaje mediante un parámetro de método, como `string paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede enlazar a cualquiera de los siguientes tipos:

* Objeto: el entorno de tiempo de ejecución de Functions deserializa una carga JSON en una instancia de una clase arbitraria definida en el código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

En JavaScript, use `context.bindings.<name>` para tener acceso a la carga del elemento de la cola. Si la carga es JSON, se deserializa en un objeto.

## <a name="trigger---message-metadata"></a>Desencadenador: metadatos del mensaje

El desencadenador de cola proporciona varias [propiedades de metadatos](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Estas son propiedades de la clase [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Propiedad|Escriba|DESCRIPCIÓN|
|--------|----|-----------|
|`QueueTrigger`|`string`|Carga de cola (si hay una cadena válida). Si la cola envía la carga como una cadena, `QueueTrigger` tiene el mismo valor que la variable denominada por la propiedad `name` en *function.json*.|
|`DequeueCount`|`int`|Es el número de veces que se ha quitado de la cola este mensaje.|
|`ExpirationTime`|`DateTimeOffset`|Es la hora de expiración del mensaje.|
|`Id`|`string`|Es el identificador del mensaje de cola.|
|`InsertionTime`|`DateTimeOffset`|Es la hora en la que el mensaje se agregó a la cola.|
|`NextVisibleTime`|`DateTimeOffset`|Es la siguiente hora a la que será visible el mensaje.|
|`PopReceipt`|`string`|Es la confirmación de extracción del mensaje.|

## <a name="trigger---poison-messages"></a>Desencadenador: mensajes dudosos

Si se produce un error en una función del desencadenador de cola, Azure Functions volverá a intentar esa función hasta cinco veces para un determinado mensaje en la cola, incluido el primer intento. Si se produce un error en los cinco intentos, Functions agregará un mensaje a la cola denominada *&lt;nombreDeColaOriginal>-poison*. Puede escribir una función para procesar los mensajes desde la cola de mensajes dudosos registrándolos o enviando una notificación indicando que se necesita atención manual.

Para controlar manualmente los mensajes dudosos, compruebe el elemento [dequeueCount](#trigger---message-metadata) del mensaje de la cola.

## <a name="trigger---polling-algorithm"></a>Desencadenador: algoritmo de sondeo

El desencadenador de cola implementa un algoritmo de interrupción exponencial aleatorio para reducir el efecto del sondeo de cola inactiva en los costos de transacción de almacenamiento.  Cuando se encuentra un mensaje, el entorno en tiempo de ejecución espera dos segundos y, a continuación, comprueba si hay otro mensaje; cuando no se encuentra ningún mensaje, espera unos cuatro segundos antes de intentarlo de nuevo. Después de varios intentos fallidos para obtener un mensaje de la cola, el tiempo de espera sigue aumentando hasta que alcanza el tiempo de espera máximo, predeterminado en un minuto. El tiempo de espera máximo se configura mediante la propiedad `maxPollingInterval` en el [archivo host.json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Desencadenador: simultaneidad

Cuando hay varios mensajes en cola en espera, el desencadenador de cola recupera un lote de mensajes e invoca instancias de función de manera simultánea para procesarlas. De manera predeterminada, el tamaño de lote es 16. Cuando el número que se está procesando llega a 8, el entorno en tiempo de ejecución obtiene otro lote y empieza a procesar esos mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función en una máquina virtual es 24. Este límite se aplica por separado a cada función desencadenada por la cola en cada máquina virtual. Si la aplicación de función se escala horizontalmente a varias máquinas virtuales, cada máquina virtual esperará los desencadenadores e intentará ejecutar las funciones. Por ejemplo, si una aplicación de función se escala horizontalmente a 3 máquinas virtuales, el número máximo predeterminado de instancias simultáneas de una función desencadenada por la cola es 72.

El tamaño de lote y el umbral para obtener un lote nuevo se configuran en el [archivo host.json](functions-host-json.md#queues). Si quiere minimizar la ejecución en paralelo para las funciones desencadenadas por la cola en una aplicación de función, puede establecer el tamaño de lote en 1. Este valor solo elimina la simultaneidad siempre y cuando la aplicación de función se ejecute en una única máquina virtual (VM). 

El desencadenador de la cola automáticamente impide que una función procese un mensaje de cola varias veces; las funciones no tienen que escribirse para que sean idempotentes.

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

En el ejemplo siguiente se muestra un enlace de desencadenador HTTP de un archivo *function.json* y el código de [script de C# (.csx)](functions-reference-csharp.md) que usa el enlace. La función crea un elemento de cola con una carga del objeto **CustomQueueMessage** para cada una de las solicitudes HTTP recibidas.

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
    ICollector<CustomQueueMessage> myQueueItems, 
    TraceWriter log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador HTTP de un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función crea un elemento de cola para cada una de las solicitudes HTTP recibidas.

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
 
En las [bibliotecas de clase C#](functions-dotnet-class-library.md), use [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

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

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

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

* Un objeto serializable como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

En C# y script de C#, escriba varios mensajes de cola mediante uno de los siguientes tipos: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

En las funciones de JavaScript, use `context.bindings.<name>` para tener acceso al mensaje de cola de salida. Puede usar una cadena o un objeto JSON serializable para la carga del elemento de cola.


## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace |  Referencia |
|---|---|
| Cola | [Códigos de error de cola](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, tabla, cola | [Códigos de error de almacenamiento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabla, cola |  [Solución de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Ir a un inicio rápido que use un desencadenador de Queue Storage](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Ir a un tutorial que usa un enlace de salida de Queue Storage](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
