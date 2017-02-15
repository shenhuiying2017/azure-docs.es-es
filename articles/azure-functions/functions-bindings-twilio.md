---
title: Enlace de Twilio de Azure Functions | Microsoft Docs
description: "Entender cómo usar enlaces de Twilio con Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
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
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 29fa4620885ec787f362966f732510fded600d53


---
# <a name="azure-functions-twilio-output-binding"></a>Enlace de salida de Twilio de Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

En este artículo se explica cómo configurar y usar enlaces de Twilio con Azure Functions. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions admite enlaces de salida de Twilio para permitir a sus funciones enviar mensajes de texto SMS con unas cuantas líneas de código y una cuenta de [Twilio](https://www.twilio.com/). 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>function.json para enlace de salida de centros de notificaciones de Azure
El archivo function.json ofrece las siguientes propiedades:

* `name`: nombre de variable usado en el código de función para el mensaje de texto SMS de Twilio.
* `type`: se debe establecer en *"twilioSms"*.
* `accountSid`: este valor debe establecerse en el nombre de una configuración de aplicación que contiene al SID de la cuenta de Twilio.
* `authToken`: este valor debe establecerse en el nombre de una configuración de aplicación que contiene el token de autenticación de Twilio.
* `to`: este valor se establece en el número de teléfono al que se envía el texto SMS.
* `from`: este valor se establece en el número de teléfono desde el que se envía el texto del SMS.
* `direction` : debe establecerse en *out*.
* `body`: este valor se puede usar para codificar el mensaje de texto SMS si no necesita establecerlo dinámicamente en el código de la función. 

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




<!--HONumber=Nov16_HO3-->


