---
title: "Uso del servicio de correo electrónico SendGrid (.NET) | Microsoft Docs"
description: "Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Los ejemplos de código están escritos en C# y utilizan la API .NET."
services: app-service\web
documentationcenter: .net
author: thinkingserious
manager: dwrede
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/14/2016
ms.author: team-pi@sendgrid.com
translationtype: Human Translation
ms.sourcegitcommit: dc1dac6e590088b45aa7afb87cf2d41027506635
ms.openlocfilehash: 5ad1d7f026836e90d04d493bd1c0454b83f936ef


---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Envío de correos electrónicos con SendGrid y Azure
## <a name="overview"></a>Información general
Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en C\#
 y utilizan la API de .NET. Entre los escenarios descritos se incluyen **creación de correos electrónicos**, **envío de correos electrónicos**, **incorporación de datos adjuntos** y **uso de filtros**. Para obtener más información sobre SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes][Pasos siguientes].

## <a name="what-is-the-sendgrid-email-service"></a>¿Qué es el servicio de correo electrónico SendGrid?
SendGrid es un [servicio de correo electrónico basado en la nube] que ofrece un sistema confiable de [entrega de correo electrónico transaccional], escalabilidad y análisis en tiempo real junto, con API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

* Envío automático de recibos a clientes.
* Administración de las listas de distribución para el envío mensual de mensajes electrónicos promocionales y ofertas especiales a clientes.
* Recopilación de diversas métricas en tiempo real, como las direcciones de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
* Generación de informes para ayudar a identificar tendencias.
* Reenvío de las consultas de los clientes.
* Procesamiento de mensajes de correo electrónico entrantes.

Para obtener más información, consulte [https://sendgrid.com](https://sendgrid.com) o nuestra [biblioteca de C#][sendgrid-csharp]

## <a name="create-a-sendgrid-account"></a>Creación de una cuenta de SendGrid
[!INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referencia de la biblioteca de clases .NET de SendGrid
El [paquete NuGet de SendGrid](https://www.nuget.org/packages/Sendgrid) es la forma más fácil de obtener la API de SendGrid y configurar la aplicación con todas las dependencias. NuGet es una extensión de Visual Studio incluida en Microsoft Visual Studio 2015 que facilita la instalación y la actualización de las bibliotecas y las herramientas. 

> [!NOTE]
> Para instalar NuGet si ejecuta una versión de Visual Studio anterior a Visual Studio 2015, visite [http://www.nuget.org](http://www.nuget.org)y haga clic en el botón **Instalar NuGet** .
> 
> 

Realice los pasos siguientes para instalar el paquete NuGet de SendGrid en su aplicación:

1. Se creó un proyecto nuevo.
   
   ![Crear un nuevo proyecto][create-new-project]
2. Seleccione una plantilla.
   
   ![Seleccione una plantilla:][select-a-template]
3. En el **Explorador de soluciones**, haga clic con el botón derecho en **Referencias** y, después, en **Administrar paquetes NuGet**.
4. Busque **SendGrid** y seleccione el elemento **SendGrid** en la lista de resultados.
   
   ![paquete NuGet de SendGrid][SendGrid-NuGet-package]
5. Seleccione la **versión 6.3.4** del paquete de NuGet en la lista desplegable de versiones para poder trabajar con el objeto de modelo y las API que se muestran en este artículo.
6. Haga clic en **Instalar** para completar la instalación y cierre este cuadro de diálogo.

La biblioteca de clases .NET de SendGrid se llama **SendGridMail**. Contiene los siguientes espacios de nombres:

* **SendGridMail** para crear y trabajar con elementos de correo electrónico.
* **SendGridMail.Transport** para enviar correo electrónico mediante el protocolo **SMTP** o el protocolo HTTP 1.1 con **Web/REST**.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de cualquier archivo C\# en el que desee obtener acceso al servicio de correo electrónico SendGrid mediante programación.
**System.Net** y **System.Net.Mail** son espacios de nombres de .NET Framework que se incluyen porque contienen los tipos que usará normalmente con las API de SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Creación de un correo electrónico
Use el objeto **SendGridMessage** para crear un mensaje de correo. Una vez creado el objeto de mensaje, establecer propiedades y métodos, incluidos el remitente, el destinatario, el asunto y el cuerpo del correo electrónico.

El siguiente ejemplo muestra la forma de crear un objeto de correo electrónico completo:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Para obtener más información sobre las propiedades y los métodos que admite el tipo **SendGrid**, consulte [sendgrid-csharp][sendgrid-csharp] en GitHub.

## <a name="how-to-send-an-email"></a>Envío de un correo electrónico
Después de crear un mensaje de correo electrónico, puede enviarlo con la API web que proporciona SendGrid. También puede [usar la biblioteca integrada de .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

El envío de correos electrónicos requiere que proporcione las credenciales de su cuenta de SendGrid (nombre de usuario y contraseña) o la clave de API de SendGrid. La clave de API es el método preferido. Si desea obtener detalles sobre cómo configurar claves de API, visite nuestra [documentación](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

Puede almacenar estas credenciales en el Portal de Azure haciendo clic en CONFIGURAR y agregando pares clave-valor en "configuración de la aplicación".

 ![Configuración de la aplicación de Azure][azure_app_settings]

 A continuación, puede tener acceso a ellas de la siguiente manera: 

    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Mediante credenciales:

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Mediante la clave de API:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


En los siguientes ejemplos se muestra la forma de enviar un mensaje con la API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Incorporación de un archivo adjunto
Es posible agregar datos adjuntos a un mensaje llamando al método **AddAttachment** y especificar el nombre y la ruta de acceso del archivo que desea adjuntar.
Puede incluir múltiples datos adjuntos mediante la llamada a este método, que debe utilizar una vez por cada archivo que desee adjuntar. El siguiente ejemplo demuestra la incorporación de datos adjuntos a un mensaje:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

También puede agregar datos adjuntos desde la **Transmisión**de los datos. Puede hacerlo llamando al mismo método que antes, **AddAttachment**, pero pasando la Transmisión de los datos y el nombre de archivo que quiere que se muestre como en el mensaje. En este caso necesitará agregar la biblioteca System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Procedimiento: uso de aplicaciones para habilitar pies de página, seguimiento y análisis
SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de aplicaciones. Todos ellos son parámetros que se pueden agregar a un mensaje de correo electrónico para habilitar funciones específicas, como el seguimiento por clics, Google Analytics, el seguimiento de suscripciones, etc. Si quiere obtener una lista completa de las aplicaciones, consulte [Configuración de aplicaciones][Configuración de aplicaciones].

Es posible incluir aplicaciones en los mensajes de correo de **SendGrid** con métodos implementados como parte de una clase de **SendGrid**.

Los siguientes ejemplos demuestran el uso de los filtros de pie de página y seguimiento por clics:

### <a name="footer"></a>Pie de página
    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Seguimiento por clics
    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Uso de servicios adicionales de SendGrid
SendGrid ofrece API basadas en web y enlaces web que puede usar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte la [Documentación sobre la API de SendGrid][Documentación sobre la API de SendGrid].

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

* Repositorio de bibliotecas C\# de SendGrid: [sendgrid-csharp][sendgrid-csharp]
* Documentación sobre la API de SendGrid: <https://sendgrid.com/docs>
* Oferta especial de SendGrid para clientes de Azure: [https://sendgrid.com](https://sendgrid.com)

[Pasos siguientes]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[Uso de filtros para habilitar pies de página, seguimiento y análisis]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[oferta especial]: https://www.sendgrid.com/windowsazure.html

[crear-nuevo-proyecto]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
[seleccionar-una-plantilla]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
[paquete-NuGet-de-SendGrid]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
[configuración_de_aplicación_de_azure]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP frente a API web]: https://sendgrid.com/docs/Integrate/index.html
[Configuración de aplicaciones]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[Documentación sobre la API de SendGrid]: https://sendgrid.com/docs

[servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
[entrega de correo electrónico transaccional]: https://sendgrid.com/transactional-email




<!--HONumber=Nov16_HO5-->


