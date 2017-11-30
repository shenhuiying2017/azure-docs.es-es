---
title: Uso de SendGrid en Azure Functions | Microsoft Docs
description: "Muestra cómo usar SendGrid en Azure Functions"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Uso de SendGrid en Azure Functions

## <a name="sendgrid-overview"></a>Información general sobre SendGrid

Azure Functions admite enlaces de salida de SendGrid para permitir a sus funciones enviar mensajes de correo electrónico con unas cuantas líneas de código y una cuenta de SendGrid.

Para usar la API de SendGrid en una función de Azure, debe tener una [cuenta de SendGrid](http://SendGrid.com). Además, debe tener una clave de API de SendGrid. Inicie sesión en la cuenta de SendGrid y haga clic en **Settings** (Configuración) y luego en **API Key** (Clave de API) para generar una clave de API. Tenga a mano la clave, porque debe usarla en un próximo paso.

Ahora está listo para crear una Function App de Azure.

## <a name="create-an-azure-function-app"></a>Creación de una Function App de Azure 

Las Function App de Azure son contenedores para una o varias funciones de Azure. Las funciones de Azure son justamente eso: una función. Cada función de Azure está asociada a un desencadenador, que es un evento que hace que la función se ejecute.
Cada función puede contener cualquier número de enlaces de entrada o salida. Los enlaces son servicios que puede usar en una función. SendGrid es un enlace de salida que se puede usar para enviar correo electrónico. 

1. Inicie sesión en Azure Portal y [cree una Function App de Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) o abra una Function App existente. 
2. Cree una función de Azure. Para simplificarla, elija un desencadenador manual y C#. 

 ![Creación de una función de Azure](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Configuración de SendGrid para usarlo en una Function App de Azure

Debe almacenar la clave de API de SendGrid como una configuración de aplicación para utilizarla en una función. El campo ApiKey no es la clave de API de SendGrid real, sino un valor de configuración de la aplicación que se establece y que representa la clave de API real. La clave se almacena de esta forma por seguridad, ya que es independiente de cualquier código o archivo que pueda comprobarse en el control de código fuente.

- Cree una clave **AppSettings** en la **configuración de la aplicación** de la Function App.

 ![Creación de una función de Azure](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>Configuración de enlaces de salida de SendGrid

SendGrid está disponible como un enlace de salida de una función de Azure. Para crear un enlace de salida de SendGrid:

1. Vaya a la pestaña **Integrar** de la función en Azure Portal.
2. Haga clic en **Nueva salida** para crear un enlace de salida de SendGrid.
3. Rellene las propiedades **Clave de API** y **Nombre de parámetro de mensaje**. Si lo desea, puede especificar las demás propiedades ahora o, en su lugar, también puede codificarlas. Esta configuración se puede utilizar como valores predeterminados.

 ![Configuración de enlaces de salida de SendGrid](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

Al agregar un enlace a una función se crea un archivo denominado **function.json** en la carpeta de la función. Este archivo contiene la misma información que se ve en la pestaña **Integrar** de la función de Azure, pero en formato JSON. Al especificar los campos **ApiKey**, **mensaje** y **de**, se crean las entradas siguientes en el archivo **function.json**: 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

Si lo prefiere, puede modificar este archivo por sí mismo directamente.

Ahora que ha creado y configurado la Function App y la función, puede escribir el código para enviar un correo electrónico.

## <a name="write-code-that-creates-and-sends-email"></a>Escritura del código que crea y envía correos electrónicos

La API de SendGrid contiene todos los comandos que necesita para crear y enviar un correo electrónico.  

- Reemplace el código de la función por el código siguiente:

```cs
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
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

Observe que la primera línea contiene la directiva ```#r``` que hace referencia al ensamblado de SendGrid. Después, puede usar una instrucción ```using``` para acceder con más facilidad a los objetos de ese espacio de nombres. En el código, cree instancias de los objetos ```Mail```, ```Personalization``` y ```Content``` a través de la API de SendGrid que redacta el correo electrónico. Cuando devuelve el mensaje, SendGrid lo entrega. 

La firma de la función contiene también un parámetro de salida adicional del tipo ```Mail``` denominado ```message```. Tanto los enlaces de entrada como los de salida se expresan como parámetros de la función en el código. 

2. Pruebe el código; para ello, haga clic en **Probar** y escriba un mensaje en el campo **Cuerpo de la solicitud** y luego haga clic en el botón **Ejecutar**.

 ![Prueba del código](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. Compruebe el correo electrónico para verificar que SendGrid envió el correo electrónico. Debe remitirse a la dirección del código del paso 1 y contener el mensaje escrito en el campo **Cuerpo de la solicitud**.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se ha explicado cómo usar el servicio SendGrid para crear y enviar correo electrónico. Para más información sobre el uso de Azure Functions en sus aplicaciones, consulte los temas siguientes: 

- [Procedimientos recomendados de Azure Functions](functions-best-practices.md): se enumeran algunos procedimientos recomendados para crear Azure Functions.

- [Guía para desarrolladores de Azure Functions](functions-reference.md): contiene las referencias del programador para codificar las funciones y definir los desencadenadores y los enlaces.

- [Prueba de Azure Functions](functions-test-a-function.md): se describen las diversas herramientas y técnicas para probar sus funciones.