<properties 
	pageTitle="Cómo usar el servicio de correo electrónico SendGrid (.NET) - Azure" 
	description="Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Los ejemplos de código están escritos en C# y utilizan la API .NET." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="erikre"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>





# Envío de correos electrónicos con SendGrid y Azure

Última actualización: 24 de febrero de 2015

<h2><a name="overview"></a><span  class="short-header">Información general</span></h2>

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en C# y utilizan la API .NET. Entre los escenarios descritos se incluyen **creación de correos electrónicos**, **envío de correos electrónicos**, **incorporación de datos adjuntos** y **uso de filtros**. Para obtener más información sobre SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes][].

<h2><a name="whatis"></a><span  class="short-header">¿Qué es el servicio de correo electrónico SendGrid?</span></h2>

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona un sistema fiable de [entrega de correos electrónicos transaccional], escalabilidad y análisis en tiempo real junto con API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a clientes.
-   Administración de las listas de distribución para el envío mensual de mensajes electrónicos promocionales y ofertas especiales a clientes.
-   Recopilación de diversas métricas en tiempo real, como las direcciones de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias.
-   Reenvío de las consultas de los clientes.
-   Procesamiento de mensajes de correo electrónico entrantes.

Para obtener más información, consulte [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Creación de una cuenta de SendGrid</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Referencia de la biblioteca de clases .NET de SendGrid</h2>

El [paquete NuGet de SendGrid](https://www.nuget.org/packages/Sendgrid) es la forma más fácil de obtener la API de SendGrid y configurar la aplicación con todas las dependencias. NuGet es una extensión de Visual Studio incluida en Microsoft Visual Studio 2012 que facilita la instalación y la actualización de las bibliotecas y las herramientas.

> [AZURE.NOTE]Para instalar NuGet si ejecuta una versión de Visual Studio anterior a Visual Studio 2012, visite [http://www.nuget.org](http://www.nuget.org) y haga clic en el botón **Instalar NuGet**.

Realice los pasos siguientes para instalar el paquete NuGet de SendGrid en su aplicación:

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **Referencias** y luego en **Administrar paquetes de NuGet**.

2.  En el panel izquierdo del cuadro de diálogo **Administrar paquetes de NuGet**, haga clic en **En línea**.

3.  Busque **SendGrid** y seleccione el elemento **SendGrid** en la lista de resultados (la versión actual es 5.0.0).

    ![Paquete NuGet de SendGrid][SendGrid-NuGet-package]

4.  Haga clic en **Instalar** para completar la instalación y cierre este cuadro de diálogo.

La biblioteca de clases .NET de SendGrid se llama **SendGridMail**. Contiene los siguientes espacios de nombres:

-   **SendGridMail** para crear y trabajar con elementos de correo electrónico.
-   **SendGridMail.Transport** para enviar correo electrónico mediante el protocolo **SMTP** o el protocolo HTTP 1.1 con **Web/REST**.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al servicio de correo electrónico SendGrid mediante programación: **System.Net** y **System.Net.Mail** son espacios de nombres de .NET Framework que se incluyen porque contienen los tipos que usará normalmente con las API de SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Creación de un correo electrónico</h2>

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

Para obtener más información sobre las propiedades y los métodos que admite el tipo **SendGrid**, consulte [sendgrid-csharp][] en GitHub.

<h2><a name="sendemail"></a>Envío de un correo electrónico</h2>

Después de crear un mensaje de correo electrónico, puede enviarlo con la API web que proporciona SendGrid. También puede [usar la biblioteca integrada de .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

El envío de correos electrónicos requiere que proporcione las credenciales de su cuenta de SendGrid (nombre de usuario y contraseña). El siguiente código demuestra la forma de unificar sus credenciales en un objeto **NetworkCredential**:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

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

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

<h2><a name="addattachment"></a>Incorporación de un archivo adjunto</h2>

Es posible agregar datos adjuntos a un mensaje llamando al método **AddAttachment** y especificar el nombre y la ruta de acceso del archivo que desea adjuntar. Puede incluir múltiples datos adjuntos mediante la llamada a este método, que debe utilizar una vez por cada archivo que desee adjuntar. El siguiente ejemplo demuestra la incorporación de datos adjuntos a un mensaje:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
También puede agregar datos adjuntos desde la **Transmisión** de los datos. Puede hacerlo llamando al mismo método que antes, **AddAttachment**, pero pasando la Transmisión de los datos y el nombre de archivo que quiere que se muestre como en el mensaje. En este caso necesitará agregar la biblioteca System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">Uso de aplicaciones para habilitar pies de página, seguimiento y análisis</span></h2>

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de aplicaciones. Todos ellos son parámetros que se pueden agregar a un mensaje de correo electrónico para habilitar funciones específicas, como el seguimiento por clics, Google Analytics, el seguimiento de suscripciones, etc. Si quiere obtener una lista completa de las aplicaciones, consulte [Configuración de aplicaciones][].

Es posible incluir aplicaciones en los mensajes de correo de **SendGrid** con métodos implementados como parte de una clase de **SendGrid**.

Los siguientes ejemplos demuestran el uso de los filtros de pie de página y seguimiento por clics:

### Pie de página

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Seguimiento por clics

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href="http://www.example.com">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>Uso de servicios adicionales de SendGrid</h2>

SendGrid ofrece API basadas en web y enlaces web que puede usar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte la [Documentación sobre la API de SendGrid][].

<h2><a name="nextsteps"></a>Pasos siguientes</h2>

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

* Repositorio de bibliotecas C# de SendGrid: [sendgrid-csharp][]
*   Documentación sobre la API de SendGrid: <https://sendgrid.com/docs>
*   Oferta especial de SendGrid para clientes de Azure: [https://sendgrid.com](https://sendgrid.com)

  [Pasos siguientes]: #nextsteps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Configuración de aplicaciones]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentación sobre la API de SendGrid]: https://sendgrid.com/docs
  
  [servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
  [entrega de correos electrónicos transaccional]: https://sendgrid.com/transactional-email
 

<!---HONumber=August15_HO6-->