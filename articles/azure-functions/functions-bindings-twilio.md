---
title: Enlace de Twilio de Azure Functions
description: "Entender cómo usar enlaces de Twilio con Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 52a45f1b67e3194739fe97daad56de2d3515dee3
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="twilio-binding-for-azure-functions"></a>Enlace de Twilio para Azure Functions

En este artículo se explica cómo enviar mensajes de texto mediante los enlaces de [Twilio](https://www.twilio.com/) en Azure Functions. Azure Functions admite los enlaces de salida para Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#c-example)
* [Script de C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que envía un mensaje de texto cuando se desencadena mediante un mensaje de cola.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

En este ejemplo se usa el atributo `TwilioSms` con el valor devuelto del método. Una alternativa es usar el atributo con un parámetro `out SMSMessage` o un parámetro `ICollector<SMSMessage>` o `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>Ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de salida de Twilio en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función usa un parámetro `out` para enviar un mensaje de texto.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de script de C#:

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

No puede usar parámetros de salida en el código asincrónico. Este es un ejemplo del código de script de C# asincrónico:

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

### <a name="javascript-example"></a>Ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de salida de Twilio en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace.

Estos son los datos de enlace del archivo *function.json*:

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

Este es el código de JavaScript:

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

## <a name="attributes"></a>Atributos

Para las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio).

Para información sobre las propiedades de atributo que puede configurar, consulte [Configuración](#configuration). A continuación, se muestra un ejemplo del atributo `TwilioSms` en una signatura de método:

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(
    AccountSidSetting = "TwilioAccountSid", 
    AuthTokenSetting = "TwilioAuthToken", 
    From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    ...
}
 ```

Para obtener un ejemplo completo, consulte [Ejemplo de C#](#c-example).

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `TwilioSms`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type**|| Se debe establecer en `twilioSms`.|
|**dirección**|| Se debe establecer en `out`.|
|**name**|| Nombre de variable usado en el código de función para el mensaje de texto SMS de Twilio. |
|**accountSid**|**AccountSid**| Este valor debe establecerse en el nombre de una configuración de aplicación que contiene el SID de la cuenta de Twilio.|
|**authToken**|**AuthToken**| Este valor debe establecerse en el nombre de una configuración de aplicación que contiene el token de autenticación de Twilio.|
|**to**|**To**| Este valor se establece en el número de teléfono al que se envía el mensaje de texto SMS.|
|**from**|**From**| Este valor se establece en el número de teléfono desde el que se envía el mensaje de texto SMS.|
|**body**|**Cuerpo**| Este valor se puede usar para codificar el mensaje de texto SMS si no necesita establecerlo dinámicamente en el código de la función. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)


