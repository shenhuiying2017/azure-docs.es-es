---
title: Enlaces de Azure Service Bus en Azure Functions
description: "Descubra cómo utilizar desencadenadores y enlaces de Azure Service Bus en Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
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
ms.author: tdykstra
ms.openlocfilehash: 2df003d47291570b31e1091f34994e4023000981
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Enlaces de Azure Service Bus en Azure Functions

En este artículo se explica cómo trabajar con enlaces de Azure Service Bus en Azure Functions. Azure Functions admite enlaces de desencadenador y salida para colas y temas de Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Desencadenador

Utilice el desencadenador de Service Bus para responder a mensajes de una cola o tema de Service Bus. 

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que registra un mensaje de cola de Service Bus.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de Service Bus en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función registra un mensaje de cola de Service Bus.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de C#:

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---f-example"></a>Desencadenador: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de Service Bus en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. La función registra un mensaje de cola de Service Bus. 

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código del script de F#:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de Service Bus en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función registra un mensaje de cola de Service Bus. 

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código del script de JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

## <a name="trigger---attributes"></a>Desencadenador: atributos

Para las [bibliotecas de clases de C#](functions-dotnet-class-library.md), utilice los siguientes atributos para configurar un desencadenador de Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus)

  El constructor del atributo toma el nombre de la cola o del tema y la suscripción. También puede especificar los derechos de acceso de la conexión. Si no los especifica, el valor predeterminado es `Manage`. Para obtener instrucciones sobre cómo elegir la configuración de derechos de acceso, consulte la sección [Desencadenador: configuración](#trigger---configuration). A continuación, tiene un ejemplo que muestra el atributo que se usa con un parámetro de cadena:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Puede establecer la propiedad `Connection` para especificar la cuenta de Service Bus que se usará, tal como se muestra en el ejemplo siguiente:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Para obtener un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs), definido en el paquete NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus)

  Proporciona otra manera de especificar la cuenta de Service Bus que se debe usar. El constructor toma el nombre de una configuración de aplicación que contiene una cadena de conexión de Service Bus. El atributo se puede aplicar en el nivel de clase, método o parámetro. En el ejemplo siguiente se muestran el nivel de clase y de método:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

La cuenta de Service Bus que se debe usar se determina en el orden siguiente:

* La propiedad `Connection` del atributo `ServiceBusTrigger`.
* El atributo `ServiceBusAccount` aplicado al mismo parámetro que el atributo `ServiceBusTrigger`.
* El atributo `ServiceBusAccount` aplicado a la función.
* El atributo `ServiceBusAccount` aplicado a la clase.
* La configuración de aplicación "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `ServiceBusTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "serviceBusTrigger". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Debe establecerse en "in". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa el mensaje de cola o tema en el código de la función. Se establece en "$return" para hacer referencia al valor devuelto de la función. | 
|**queueName**|**QueueName**|Nombre de la cola que se debe supervisar.  Se establece únicamente si se supervisa una cola, no un tema.
|**topicName**|**TopicName**|Nombre del tema que se debe supervisar. Se establece únicamente si se supervisa un tema, no una cola.|
|**subscriptionName**|**SubscriptionName**|Nombre de la suscripción que se debe supervisar. Se establece únicamente si se supervisa un tema, no una cola.|
|**conexión**|**Connection**|Nombre de una configuración de aplicación que contiene la cadena de conexión de Service Bus que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre. Por ejemplo, si establece `connection` en "MyServiceBus", el entorno de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyServiceBus". Si deja el valor de `connection` vacío, el entorno de ejecución de Functions usa la cadena de conexión de Service Bus predeterminada en la configuración de aplicación que se denomina "AzureWebJobsServiceBus".<br><br>Para obtener la cadena de conexión, siga los pasos mostrados en [Obtención de las credenciales de administración](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). La cadena de conexión debe ser para un espacio de nombres de Service Bus y no estar limitada a una cola o un tema concretos. |
|**accessRights**|**Access**|Derechos de acceso para la cadena de conexión. Los valores disponibles son `manage` y `listen`. El valor predeterminado es `manage`, lo que indica que `connection` tiene el permiso **Administrar**. Si usa una cadena de conexión que no tiene el permiso **Administrar**, establezca `accessRights` en "listen". De lo contrario, el runtime de Functions puede intentar realizar operaciones que requieran derechos de administración y no conseguirlo.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Desencadenador: uso

En C# y el script de C#, acceda al mensaje de cola o tema mediante un parámetro de método, como `string paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede utilizar cualquiera de los siguientes tipos en lugar de `string`:

* `byte[]`: útil para datos binarios.
* Un tipo personalizado: si el mensaje contiene el archivo JSON, Azure Functions intenta deserializar los datos JSON.
* `BrokeredMessage`: proporciona el mensaje deserializado con el método [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx).

En JavaScript, puede obtener acceso al mensaje de cola o tema mediante el uso de `context.bindings.<name>`. `<name>` es el valor especificado en la propiedad `name` de *function.json*. El mensaje de Service Bus se pasa a la función como una cadena o como un objeto JSON.

## <a name="trigger---poison-messages"></a>Desencadenador: mensajes dudosos

El control de mensajes dudosos no se puede controlar ni configurar en Azure Functions. Service Bus controla este tipo de mensajes.

## <a name="trigger---peeklock-behavior"></a>Desencadenador: comportamiento de PeekLock

El entorno de ejecución de Functions recibe un mensaje en [modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Llama a `Complete` en el mensaje si la función finaliza correctamente, o llama a `Abandon` si se produce un error en la función. Si la ejecución de la función dura más que el tiempo de espera de `PeekLock`, el bloqueo se renovará automáticamente.

## <a name="trigger---hostjson-properties"></a>Desencadenador: propiedades de host.json

El archivo [host.json](functions-host-json.md#servicebus) contiene opciones de configuración que controlan el comportamiento de desencadenador de Service Bus.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Salida

Use el enlace de salida de Azure Service Bus para enviar mensajes de cola o tema.

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que envía un mensaje de cola de Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de salida de Service Bus en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función usa un desencadenador de temporizador para enviar un mensaje de cola cada 15 segundos.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de C# que crea un único mensaje:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Este es el código de script de C# que crea varios mensajes:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Salida: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de salida de Service Bus en un archivo *function.json* y una [función de script de F#](functions-reference-fsharp.md) que usa el enlace. La función usa un desencadenador de temporizador para enviar un mensaje de cola cada 15 segundos.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de F# que crea un único mensaje:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de salida de Service Bus en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función usa un desencadenador de temporizador para enviar un mensaje de cola cada 15 segundos.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de JavaScript que crea un único mensaje:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Este es el código de script de JavaScript que crea varios mensajes:

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

## <a name="output---attributes"></a>Salida: atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

El constructor del atributo toma el nombre de la cola o del tema y la suscripción. También puede especificar los derechos de acceso de la conexión. En la sección [Salida: configuración](#output---configuration) se explica cómo elegir la configuración de derechos de acceso. Este es un ejemplo que muestra el atributo aplicado al valor devuelto de la función:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de Service Bus que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

Puede usar el atributo `ServiceBusAccount` para especificar la cuenta de Service Bus en el nivel de clase, método o parámetro.  Para obtener más información, consulte [Desencadenador: atributos](#trigger---attributes).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `ServiceBus`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "serviceBus". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Debe establecerse en "out". Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa la cola o el tema en el código de la función. Se establece en "$return" para hacer referencia al valor devuelto de la función. | 
|**queueName**|**QueueName**|Nombre de la cola.  Se establece únicamente si se envían mensajes de cola, no de tema.
|**topicName**|**TopicName**|Nombre del tema que se debe supervisar. Se establece únicamente si se envían mensajes de tema, no de cola.|
|**subscriptionName**|**SubscriptionName**|Nombre de la suscripción que se debe supervisar. Se establece únicamente si se envían mensajes de tema, no de cola.|
|**conexión**|**Connection**|Nombre de una configuración de aplicación que contiene la cadena de conexión de Service Bus que se usará para este enlace. Si el nombre de la configuración de aplicación comienza con "AzureWebJobs", puede especificar solo el resto del nombre. Por ejemplo, si establece `connection` en "MyServiceBus", el entorno de ejecución de Functions busca una configuración de aplicación denominada "AzureWebJobsMyServiceBus". Si deja el valor de `connection` vacío, el entorno de ejecución de Functions usa la cadena de conexión de Service Bus predeterminada en la configuración de aplicación que se denomina "AzureWebJobsServiceBus".<br><br>Para obtener la cadena de conexión, siga los pasos mostrados en [Obtención de las credenciales de administración](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). La cadena de conexión debe ser para un espacio de nombres de Service Bus y no estar limitada a una cola o un tema concretos.|
|**accessRights**|**Access** |Derechos de acceso para la cadena de conexión. Los valores disponibles son "manage" y "listen". El valor predeterminado es "manage", lo que indica que la conexión tiene los permisos **Administrar**. Si usa una cadena de conexión que no tiene los permisos **Administrar**, establezca `accessRights` en "listen". De lo contrario, el runtime de Functions puede intentar realizar operaciones que requieran derechos de administración y no conseguirlo.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

En C# y el script de C#, acceda a la cola o al tema mediante un parámetro de método, como `out string paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede utilizar cualquiera de los siguientes tipos de parámetros:

* `out T paramName` - `T` puede ser cualquier tipo que se pueda serializar con JSON. Si el valor del parámetro es null cuando finaliza la función, Functions crea el mensaje con un objeto null.
* `out string`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `out byte[]`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `out BrokeredMessage`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.

Para crear varios mensajes en una función de C# o script de C#, puede utilizar `ICollector<T>` o `IAsyncCollector<T>`. Se crea un mensaje al llamar al método `Add` .

En JavaScript, puede obtener acceso a la cola o al tema mediante el uso de `context.bindings.<name>`. `<name>` es el valor especificado en la propiedad `name` de *function.json*. Puede asignar una cadena, una matriz de bytes o un objeto de JavaScript (deserializado en JSON) a `context.binding.<name>`.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
