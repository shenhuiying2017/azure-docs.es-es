---
title: Enlaces de SendGrid de Azure Functions | Microsoft Docs
description: Referencia de enlaces de SendGrid de Azure Functions
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.openlocfilehash: 4cdafbe05e29d8b483c6b0e1daf41a36583d7b5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-sendgrid-bindings"></a>Enlaces de SendGrid de Azure Functions

En este artículo se explica cómo configurar y trabajar con enlaces de SendGrid en Azure Functions. Con SendGrid, puede usar Azure Functions para enviar correo electrónico personalizado mediante programación.

Este artículo sirve como información de referencia para los desarrolladores de Azure Functions. Si está familiarizado con Funciones de Azure, comience con los siguientes recursos:

[Cree la primera Azure Function](functions-create-first-azure-function.md). 
Referencias para desarrolladores de [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) o [nodo](functions-reference-node.md).

## <a name="functionjson-for-sendgrid-bindings"></a>function.json para enlaces de SendGrid

Azure Functions proporciona un enlace de salida para SendGrid. El enlace de salida de SendGrid le permite crear y enviar correo electrónico mediante programación. 

El enlace de SendGrid admite las siguientes propiedades:

|Propiedad  |Descripción  |
|---------|---------|
|**name**| Requerida: nombre de variable que se usa en el código de la función para la solicitud o el cuerpo de la solicitud. Este valor es ```$return``` cuando hay solo un valor de devuelto. |
|**type**| Requerida: se debe establecer en `sendGrid`.|
|**dirección**| Requerida: se debe establecer en `out`.|
|**apiKey**| Requerida: se debe establecer en el nombre de la clave de API almacenada en la configuración de aplicación de Function App. |
|**to**| Dirección de correo electrónico del destinatario. |
|**from**| Dirección de correo electrónico del remitente. |
|**subject**| Asunto del correo electrónico. |
|**text**| Contenido del correo electrónico. |

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

## <a name="next-steps"></a>Pasos siguientes
Para información sobre otros enlaces y desencadenadores de Azure Functions, consulte: 
- [Referencia para desarrolladores de desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

- [Procedimientos recomendados de Azure Functions](functions-best-practices.md): se enumeran algunos procedimientos recomendados para crear Azure Functions.

- [Guía para desarrolladores de Azure Functions](functions-reference.md): contiene las referencias del programador para codificar las funciones y definir los desencadenadores y los enlaces.
