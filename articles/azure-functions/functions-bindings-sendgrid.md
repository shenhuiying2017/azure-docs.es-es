---
title: Enlaces de SendGrid de Azure Functions | Microsoft Docs
description: Referencia de enlaces de SendGrid de Azure Functions
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/16/2017
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 0cd7e7c55e77863c142800cdc11d6ea144c38293
ms.lasthandoff: 03/18/2017


---
# <a name="azure-functions-sendgrid-bindings"></a>Enlaces de SendGrid de Azure Functions

En este artículo se explica cómo configurar y trabajar con enlaces de SendGrid en Azure Functions. Con SendGrid, puede usar Azure Functions para enviar correo electrónico personalizado mediante programación.

Este artículo sirve como información de referencia para los desarrolladores de Azure Functions. Si está familiarizado con Funciones de Azure, comience con los siguientes recursos:

[Cree la primera Azure Function](functions-create-first-azure-function.md). 
Referencias para desarrolladores de [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) o [nodo](functions-reference-node.md).

## <a name="functionjson-for-sendgrid-bindings"></a>function.json para enlaces de SendGrid

Azure Functions proporciona un enlace de salida para SendGrid. El enlace de salida de SendGrid le permite crear y enviar correo electrónico mediante programación. 

El enlace de SendGrid admite las siguientes propiedades:

- `name` (requerido): el nombre de variable que se usa en el código de función para la solicitud o el cuerpo de la solicitud. Este valor es ```$return``` cuando hay solo un valor de devuelto. 
- `type` (requerido): se debe establecer en "sendGrid".
- `direction` (requerido): se debe establecer en "out".
- `apiKey` (requerido): se debe establecer en el nombre de la clave de API almacenada en la configuración de aplicación de Function App.
- `to`: dirección de correo electrónico del destinatario.
- `from`: dirección de correo electrónico del remitente.
- `subject` : el asunto del correo electrónico.
- `text` : el contenido del correo electrónico.

Ejemplo de **function.json**:

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

> [!NOTE]
> Azure Functions almacena la información de conexión y las claves de API como configuración de la aplicación de forma que no se protegen en el repositorio de control de código fuente. Esta acción protege la información confidencial.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>Ejemplo de C# del enlace de salida de SendGrid

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
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

## <a name="node-example-of-the-sendgrid-output-binding"></a>Ejemplo de nodo del enlace de salida de SendGrid

```javascript
module.exports = function (context, input) {    
    var message = {
        to: "recipient@contoso.com",
        from: "sender@contoso.com",        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>Pasos siguientes
Para información sobre otros enlaces y desencadenadores de Azure Functions, consulte: 
- [Referencia para desarrolladores de desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

- [Procedimientos recomendados de Azure Functions](functions-best-practices.md): se enumeran algunos procedimientos recomendados para crear Azure Functions.

- [Guía para desarrolladores de Azure Functions](functions-reference.md): contiene las referencias del programador para codificar las funciones y definir los desencadenadores y los enlaces.
