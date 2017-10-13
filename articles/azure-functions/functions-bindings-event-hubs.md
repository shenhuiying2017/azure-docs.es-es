---
title: Enlaces de Event Hubs de Azure Functions | Microsoft Docs
description: "Descubra cómo usar los enlaces de Azure Event Hubs de Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/20/2017
ms.author: wesmc
ms.openlocfilehash: 85eb6985ef3579b1b2313db3ce5f91c3471da72f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Enlaces de Event Hubs de Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y usar enlaces de [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) para Azure Functions.
Azure Functions admite enlaces de desencadenador y salida para Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Si no está familiarizado con Azure Event Hubs, consulte la [introducción a Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Desencadenador de centro de eventos
Use el desencadenador de Event Hubs para responder a un evento enviado a una secuencia de eventos de centro de eventos. Debe tener acceso de lectura al centro de eventos para configurar el desencadenador.

El desencadenador de funciones de Event Hubs usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup` es una propiedad opcional que se utiliza para establecer el [grupo de consumidores](../event-hubs/event-hubs-features.md#event-consumers) utilizado para suscribirse a eventos en el concentrador. Si se pasa por alto, se utilizará el grupo de consumidores `$Default`.  
`connection` debe ser el nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres del centro de eventos.
Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del *espacio de nombres*, no del propio centro de eventos. Esta cadena de conexión debe tener al menos permisos de lectura para activar el desencadenador.

Se pueden proporcionar [opciones de configuración adicionales](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en un archivo host.json para ajustar aún más los desencadenadores de Event Hubs.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
Cuando se activa una función de desencadenador de Event Hubs, el mensaje que la activa se pasa a la función como una cadena.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente desencadenador de Event Hubs en la matriz `bindings` de function.json:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Consulte el ejemplo de lenguaje específico que registra el cuerpo del mensaje del desencadenador del centro de eventos.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Ejemplo de desencadenador en C# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

También puede recibir el evento como un objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata), que proporciona acceso a los metadatos de evento.

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Para recibir eventos en un lote, cambie la firma del método a `string[]` o `EventData[]`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Ejemplo de desencadenador en F# #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Ejemplo de desencadenador en Node.js

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Enlace de salida de Event Hubs
Use el enlace de salida de Event Hubs para escribir eventos en una secuencia de eventos de centro de eventos. Debe tener permiso de envío a un centro de eventos para escribir eventos en él.

El enlace de salida usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection` debe ser el nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres del centro de eventos.
Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del *espacio de nombres*, no del propio centro de eventos. Esta cadena de conexión debe tener permisos de envío para enviar el mensaje a la secuencia de eventos.

## <a name="output-usage"></a>Uso de salidas
En esta sección se muestra cómo usar el enlace de salida de Event Hub en el código de función.

Puede generar mensajes para el centro de eventos configurado con los siguientes tipos de parámetro:

* `out string`
* `ICollector<string>`( para generar varios mensajes)
* `IAsyncCollector<string>` (versión asincrónica de `ICollector<T>`)

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Suponga que tiene el siguiente enlace de salida de Event Hubs en la matriz `bindings` de function.json:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Consulte el ejemplo de lenguaje específico que permite escribir un evento en la secuencia de eventos.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Ejemplo de salida en C# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

O bien, cree varios mensajes:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Ejemplo de salida en F# #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Ejemplo de salida de Node.js

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

O, para enviar varios mensajes,

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
