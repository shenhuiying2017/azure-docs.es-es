---
title: Enlaces de Centro de eventos de Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar los enlaces del Centro de eventos de Azure en Funciones de Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/02/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: bfe0f796c8a15d655f1c5686a0e1e422fee6fbc1


---
# <a name="azure-functions-event-hub-bindings"></a>Enlaces de Centro de eventos de funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y codificar enlaces de [Centro de eventos de Azure](../event-hubs/event-hubs-overview.md) para Funciones de Azure. Azure Functions admite enlaces de desencadenador y salida para Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Si no está familiarizado con Event Hubs de Azure, consulte la [información general de Event Hubs de Azure](../event-hubs/event-hubs-overview.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Desencadenador de Centro de eventos
Use el desencadenador de Centro de eventos para responder a un evento enviado a una secuencia de eventos del centro de eventos. Debe tener acceso de lectura al centro de eventos para configurar el desencadenador.

El desencadenador de un centro de eventos para una función usa el siguiente objeto JSON en la matriz `bindings` de function.json:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the Event Hub>",
    "consumerGroup": "Consumer group to use - see below", 
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup` es una propiedad opcional que se utiliza para establecer el [grupo de consumidores](../event-hubs/event-hubs-overview.md#consumer-groups) utilizado para suscribirse a eventos en el concentrador. Si se pasa por alto, se utilizará el grupo de consumidores `$Default`.  
`connection` debe ser el nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres del centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del *espacio de nombres*, no del propio centro de eventos. Esta cadena de conexión debe tener al menos permisos de lectura para activar el desencadenador.

Se pueden proporcionar [opciones de configuración adicionales](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en un archivo host.json para ajustar aún más los desencadenadores del Centro de eventos.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso del desencadenador
Cuando se activa una función de desencadenador del Centro de eventos, el mensaje que lo activa se pasa a la función como una cadena.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Ejemplo de desencadenador
Suponga que tiene el siguiente desencadenador de Centro de eventos en la matriz `bindings` de function.json:

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

## <a name="event-hub-output-binding"></a>Enlace de salida del centro de eventos
Use el enlace de salida del centro de eventos para escribir eventos en una secuencia de eventos del centro de eventos. Debe tener permiso de envío a un centro de eventos para escribir eventos en él. 

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

`connection` debe ser el nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres del centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del *espacio de nombres*, no del propio centro de eventos. Esta cadena de conexión debe tener permisos de envío para enviar el mensaje a la secuencia de eventos.

<a name="outputsample"></a>

## <a name="output-sample"></a>Ejemplo de salida
Suponga que tiene el siguiente enlace de salida de centro de eventos en la matriz `bindings` de function.json:

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
    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


