---
title: Enlace de Twilio de Azure Functions | Microsoft Docs
description: "Entender cómo usar enlaces de Twilio con Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e8c5e8f2dfedae26486e1c8afbe0cec3f3228e86
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Envío de mensajes SMS desde Azure Functions mediante el enlace de salida de Twilio
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y usar enlaces de Twilio con Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions admite enlaces de salida de Twilio para permitir a sus funciones enviar mensajes de texto SMS con unas cuantas líneas de código y una cuenta de [Twilio](https://www.twilio.com/). 

## <a name="functionjson-for-the-twilio-output-binding"></a>function.json para el enlace de salida de Twilio
El archivo function.json ofrece las siguientes propiedades:

|Propiedad  |Descripción  |
|---------|---------|
|**name**| Nombre de variable usado en el código de función para el mensaje de texto SMS de Twilio. |
|**type**| Se debe establecer en `twilioSms`.|
|**accountSid**| Este valor debe establecerse en el nombre de una configuración de aplicación que contenga el SID de la cuenta de Twilio.|
|**authToken**| Este valor debe establecerse en el nombre de una configuración de aplicación que contenga el token de autenticación de Twilio.|
|**to**| Este valor se establece en el número de teléfono al que se envía el mensaje de texto SMS.|
|**from**| Este valor se establece en el número de teléfono desde el que se envía el mensaje de texto SMS.|
|**dirección**| Se debe establecer en `out`.|
|**body**| Este valor se puede usar para codificar el mensaje de texto SMS si no necesita establecerlo dinámicamente en el código de la función. |

Function.json de ejemplo:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Ejemplo de desencadenador de cola de C# con enlace de salida de Twilio
#### <a name="synchronous"></a>Sincrónico
Este código de ejemplo sincrónico de un desencadenador de cola de Azure Storage usa un parámetro de salida para enviar un mensaje de texto a un cliente que ha realizado un pedido.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>Asincrónico
Este código de ejemplo asincrónico de un desencadenador de cola de Azure Storage envía un mensaje de texto a un cliente que ha realizado un pedido.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Ejemplo de desencadenador de cola de Node.js con enlace de salida de Twilio
En este ejemplo de Node.js se envía un mensaje de texto a un cliente que ha realizado un pedido.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


