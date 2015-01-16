<properties urlDisplayName="SendGrid Email Service" pageTitle="Utilización del servicio de correo electrónico SendGrid (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Los ejemplos de código están escritos en C# y utilizan la API .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="erikre" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/24/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />





# Envío de correos electrónicos con SendGrid y Azure

Última actualización: 24 de octubre de 2014

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en C\# y utilizan la API .NET. Entre los escenarios descritos, se incluyen la **creación de correos electrónicos**, el **envío de correos electrónicos**, la **incorporación de datos adjuntos** y el **uso de filtros**. Para obtener más información acerca de SendGrid y el envío de correos electrónicos, consulte la sección [Pasos siguientes][].

<h2><a name="toc"></a>Tabla de contenido</h2>

[¿Qué es el servicio de correo electrónico SendGrid?][]   
[Creación de una cuenta de SendGrid][]   
[Referencia de la biblioteca de clases .NET de SendGrid][]   
[Creación de un correo electrónico][]   
[Envío de un correo electrónico][]   
[Adjuntar un archivo][]   
[Uso de filtros para habilitar pies de página, seguimiento y análisis][]   
[Uso de servicios adicionales de SendGrid][]   
[Pasos siguientes][]

<h2><a name="whatis"></a>¿Qué es el servicio de correo electrónico SendGrid?</h2>

SendGrid es un [servicio de correo electrónico basado en la nube] que ofrece una
[entrega de correo electrónico transaccional y confiable], escalabilidad y análisis en tiempo real junto a API flexibles
que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes
se incluyen:

-   Envío automático de recibos a clientes.
-   Administración de listas de distribución para realizar envíos mensuales a clientes
    de folletos electrónicos y ofertas especiales.
-   Recopilación de métricas en tiempo real para correo electrónico bloqueado, por ejemplo, y
    la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias.
-   Reenvío de las consultas de los clientes.

Para obtener más información, consulte [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Creación de una cuenta de SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Referencia de la biblioteca de clases .NET de SendGrid</h2>

El [paquete NuGet de SendGrid] (https://www.nuget.org/packages/Sendgrid) es la forma más fácil de obtener la API de SendGrid y configurar su aplicación con todas las dependencias. NuGet es una extensión de Visual Studio incluida en Microsoft Visual Studio 2012 que facilita la instalación y la actualización de las bibliotecas y las herramientas. 

<div class="dev-callout">
<b>Nota:</b>
<p>Para
instalar NuGet, si está ejecutando una versión de Visual Studio anterior a Visual Studio 2012, visite <a href="http://www.nuget.org">http://www.nuget.org</a> y haga clic en el botón <b>Install
NuGet</b> (Instalar NuGet).</p>
</div>

Realice los pasos siguientes para instalar el paquete NuGet de SendGrid en su aplicación:

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **References** y, a continuación, haga clic en
    **Administración de paquetes de NuGet**.

2.  En el panel izquierdo del cuadro de diálogo **Administrar paquetes de NuGet**, haga clic en **En línea**.

3.  Busque **SendGrid** y seleccione el elemento **SendGrid** en la
    lista de resultados.

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este
    cuadro de diálogo.

La biblioteca de clases .NET de SendGrid se llama **SendGridMail**. Contiene
los espacios de nombres siguientes:

-   **SendGridMail** para la creación y el trabajo con elementos de correo electrónico.
-   **SendGridMail.Transport** para enviar correo electrónico mediante el protocolo
    **SMTP** o el protocolo HTTP 1.1 con **Web/REST**.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C\# en el que desee obtener acceso al servicio de correo electrónico SendGrid mediante programación: **System.Net** y **System.Net.Mail** son los espacios de nombres de .NET Framework que están incluidos porque contienen los tipos que utilizará normalmente con las API de SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Direccionamiento del un correo electrónico</h2>

Utilice el método **SendGrid.GetInstance** estático para crear un mensaje de correo electrónico del tipo **SendGrid**. Una vez creado el mensaje, puede utilizar las propiedades y los métodos de **SendGrid** para establecer valores como el remitente, el destinatario, el asunto y el cuerpo del correo electrónico.

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

Para obtener más información acerca de todas las propiedades y métodos admitidos por el tipo **SendGrid**, consulte [sendgrid-csharp][] en GitHub.

<h2><a name="sendemail"></a>Direccionamiento del un correo electrónico</h2>

Después de crear un mensaje de correo electrónico, puede enviarlo con la API Web que proporciona SendGrid. Como alternativa puede [utilizar la biblioteca integrada en .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

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

The following examples show how to send a message using the Web API.

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

<h2><a name="addattachment"></a>Direccionamiento del un archivo adjunto</h2>

Es posible agregar datos adjuntos a un mensaje mediante la llamada al método **AddAttachment** y la especificación del nombre y la ruta del archivo que desea adjuntar. Puede incluir múltiples datos adjuntos mediante la llamada a este método, que debe utilizar una vez por cada archivo que desee adjuntar. El siguiente ejemplo demuestra la incorporación de datos adjuntos a un mensaje:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
También puede agregar datos adjuntos desde la **Transmisión** de datos. Se puede hacer utilizando el mismo método que antes, **AddAttachment**, pero pasando la transmisión de datos y el nombre de archivo que desea mostrar como en el mensaje.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

<h2><a name="usefilters"></a>Direccionamiento del filtros para habilitar pies de página, seguimiento y análisis</h2>

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de filtros. Todos ellos son parámetros que se pueden agregar a un mensaje de correo electrónico para habilitar funciones específicas, como el seguimiento por clics, Google Analytics, el seguimiento de suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Configuración de filtros][].

Es posible aplicar filtros a los mensajes de correo electrónico de **SendGrid** con métodos implementados como parte de una clase de **SendGrid**.

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
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>Direccionamiento del servicios adicionales de SendGrid</h2>

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte la [documentación de la API de SendGrid][].

<h2><a name="nextsteps"></a>Pasos siguientes</h2>

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

* Repositorio de bibliotecas C\# de SendGrid: [sendgrid-csharp][]
*   Documentación sobre la API de SendGrid: <https://sendgrid.com/docs>
*   Oferta especial de SendGrid para clientes de Azure: [https://sendgrid.com](https://sendgrid.com)

  [Pasos siguientes]: #nextsteps
  [¿Qué es el servicio de correo electrónico SendGrid?]: #whatis
  [Creación de una cuenta de SendGrid]: #createaccount
  [Referencia de la biblioteca de clases .NET de SendGrid]: #reference
  [Direccionamiento del un correo electrónico]: #createemail
  [Direccionamiento del un correo electrónico]: #sendemail
  [Direccionamiento del un archivo adjunto]: #addattachment
  [Direccionamiento del filtros para habilitar pies de página, seguimiento y Twitter]: #usefilters
  [Direccionamiento del servicios adicionales de SendGrid]: #useservices
  
  
  [oferta especial]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
