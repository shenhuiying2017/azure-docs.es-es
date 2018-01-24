---
title: Enlaces de SendGrid de Azure Functions
description: Referencia de enlaces de SendGrid de Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/29/2017
ms.author: tdykstra
ms.openlocfilehash: aa64bbc764fe96d7c15d62660114222458e5acf4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-functions-sendgrid-bindings"></a>Enlaces de SendGrid de Azure Functions

En este artículo se explica cómo enviar correos electrónicos mediante los enlaces de [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) en Azure Functions. Azure Functions admite un enlace de salida para SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#c-example)
* [Script de C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que usa un desencadenador de cola de Service Bus y un enlace de salida de SendGrid.

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    message = new SendGridMessage();
    message.AddTo(email.To);
    message.AddContent("text/html", email.Body);
    message.SetFrom(new EmailAddress(email.From));
    message.SetSubject(email.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```

Puede omitir la propiedad `ApiKey` del atributo si tiene la clave de API en una configuración de aplicación denominada "AzureWebJobsSendGridApiKey".

### <a name="c-script-example"></a>Ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de salida de SendGrid en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace.

Estos son los datos de enlace del archivo *function.json*:

```json 
{
    "bindings": [
        {
            "name": "message",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

En la sección de [configuración](#configuration) se explican estas propiedades.

Este es el código de script de C#:

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

### <a name="javascript-example"></a>Ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de salida de SendGrid en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace.

Estos son los datos de enlace del archivo *function.json*:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

En la sección de [configuración](#configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Atributos

Para las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid).

Para información sobre las propiedades de atributo que puede configurar, consulte [Configuración](#configuration). A continuación, se muestra un ejemplo del atributo `SendGrid` en una signatura de método:

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Ejemplo de C#](#c-example).

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `SendGrid`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type**|| Requerida: se debe establecer en `sendGrid`.|
|**dirección**|| Requerida: se debe establecer en `out`.|
|**name**|| Requerida: nombre de variable que se usa en el código de la función para la solicitud o el cuerpo de la solicitud. Este valor es ```$return``` cuando hay solo un valor de devuelto. |
|**apiKey**|**ApiKey**| El nombre de una configuración de aplicación que contiene la clave de API. Si no está establecido, el nombre predeterminado de la configuración de aplicación es "AzureWebJobsSendGridApiKey".|
|**to**|**To**| Dirección de correo electrónico del destinatario. |
|**from**|**From**| Dirección de correo electrónico del remitente. |
|**subject**|**Asunto**| Asunto del correo electrónico. |
|**text**|**Texto**| Contenido del correo electrónico. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)