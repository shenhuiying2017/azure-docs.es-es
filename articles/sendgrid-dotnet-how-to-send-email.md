<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Envío de correos electrónicos con SendGrid y Azure

Última actualización: 21 de agosto de 2014

En esta guía se describe cómo realizar tareas comunes de programación con el servicio de correo electrónico
SendGrid en Azure. Los ejemplos están escritos en C#
y utilizan la API de .NET. Entre los escenarios descritos, se incluyen la **creación de
correos electrónicos**, el **envío de correos electrónicos**, la **incorporación de datos adjuntos** y el **uso de
filtros**. Para obtener más información acerca de SendGrid y el envío de correos electrónicos, consulte la sección
[Pasos siguientes][Pasos siguientes].

## <a name="toc"></a>Tabla de contenido

[¿Qué es el servicio de correo electrónico SendGrid?][¿Qué es el servicio de correo electrónico SendGrid?]   
[Creación de una cuenta de SendGrid][Creación de una cuenta de SendGrid]   
[Incorporación de referencias de la biblioteca de clases de .NET a SendGrid][Incorporación de referencias de la biblioteca de clases de .NET a SendGrid]   
[Creación de un correo electrónico][Creación de un correo electrónico]   
[Creación de un correo electrónico][1]   
[Envío de un archivo adjunto][Envío de un archivo adjunto]   
[Uso de filtros para habilitar pies de página, seguimiento y análisis][Uso de filtros para habilitar pies de página, seguimiento y análisis]   
[Uso de servicios adicionales de SendGrid][Uso de servicios adicionales de SendGrid]   
 [Pasos siguientes][Pasos siguientes]

## <a name="whatis"></a><span class="short-header">¿Qué es el servicio de correo electrónico SendGrid?</span>

SendGrid es un [servicio de correo electrónico basado en la nube][servicio de correo electrónico basado en la nube]
que proporciona un sistema fiable de [entrega de correo electrónico transaccional][entrega de correo electrónico transaccional],
escalabilidad y análisis en tiempo real, además de API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid
más comunes se incluyen:

-   Envío automático de recibos a clientes.
-   Administración de listas de distribución para enviar a los clientes
    prospectos electrónicos y ofertas mensuales especiales.
-   Recopilación de diversas métricas en tiempo real, como las direcciones
    de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias.
-   Reenvío de las consultas de los clientes.

Para obtener más información, consulte [][]<http://sendgrid.com></a> (en inglés).

## <a name="createaccount"></a><span class="short-header">Creación de una cuenta de SendGrid</span>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a><span class="short-header">Incorporación de referencias de la biblioteca de clases .NET de SendGrid</span>Incorporación de referencias de la biblioteca de clases .NET de SendGrid

El [paquete NuGet de SendGrid][paquete NuGet de SendGrid] es la forma más fácil de obtener la API
de SendGrid y configurar su aplicación con todas las dependencias. NuGet es una extensión de
Visual Studio incluida en Microsoft Visual Studio 2012 que facilita la instalación y la actualización
de las bibliotecas y las herramientas.

<div class="dev-callout">
<b>Nota:</b>
<p>Para
instalar NuGet, si est&aacute; ejecutando una versi&oacute;n de Visual Studio anterior a Visual Studio 2012, visite <a href="http://www.nuget.org">http://www.nuget.org</a> (en ingl&eacute;s), y haga clic en el bot&oacute;n <b>Install
NuGet</b>.</p>
</div>

Realice los pasos siguientes para instalar el paquete NuGet de SendGrid en su aplicación:

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **References** y, a continuación, en
    **Manage NuGet Packages**.

2.  En el panel izquierdo del cuadro de diálogo **Manage NuGet Packages**, haga clic en **Online**.

3.  Busque **SendGrid** y seleccione el elemento **SendGrid** en la lista
    de resultados.

    ![Paquete NuGet de SendGrid][Paquete NuGet de SendGrid]

4.  Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este
    cuadro de diálogo.

La biblioteca de clases .NET de SendGrid se llama **SendGridMail**. Contiene los siguientes
espacios de nombres:

-   **SendGridMail** para la creación y el trabajo con elementos de correo electrónico.
-   **SendGridMail.Transport** para el envío de correo electrónico mediante el protocolo
    **SMTP** o el protocolo HTTP 1.1 con **Web/REST**.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todos los archivos C#a los que que el servicio de correo electrónico SendGrid tenga acceso mediante programación.
**System.Net** y **System.Net.Mail** son los espacios de nombres de .NET Framework
que están incluidos porque contienen los tipos que utilizará normalmente con
las API de SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header">Direccionamiento del un correo electrónico</span>Creación de un correo electrónico

Utilice el método **SendGrid.GetInstance** estático para crear un mensaje
de correo electrónico del tipo **SendGrid**. Una vez creado el mensaje,
puede utilizar las propiedades y los métodos de **SendGrid** para establecer valores como el
remitente, el destinatario, el asunto y el cuerpo del
correo electrónico.

En el siguiente ejemplo se muestra la forma de crear un
objeto de correo electrónico completo:

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

Para obtener más información acerca de todas las propiedades y métodos admitidos por el tipo
**SendGrid**, consulte [sendgrid-csharp][sendgrid-csharp] (en inglés) en GitHub.

## <a name="sendemail"></a><span class="short-header">Direccionamiento del un correo electrónico</span>

Después de crear un mensaje de correo electrónico, puede enviarlo con
la API Web que proporciona SendGrid. Como alternativa puede [utilizar la biblioteca incorporada en .NET][utilizar la biblioteca incorporada en .NET].

El envío de correos electrónicos requiere que proporcione las
credenciales de su cuenta de SendGrid (nombre de usuario y contraseña). El siguiente código
muestra la forma de unificar sus credenciales en un objeto **NetworkCredential**
:

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

## <a name="addattachment"></a><span class="short-header">Direccionamiento del Agregar datos adjuntos</span> Cómo: un archivo adjunto

Es posible agregar datos adjuntos a un mensaje mediante la llamada al método **AddAttachment**
 y la especificación del nombre y la ruta de acceso del archivo que desea adjuntar.
Puede incluir múltiples datos adjuntos mediante la llamada a este método, que debe utilizar una vez por
cada archivo que desee adjuntar. En el siguiente ejemplo se muestra la incorporación de
datos adjuntos a un mensaje:

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

## <a name="usefilters"></a><span class="short-header">Direccionamiento del filtros para habilitar pies de página, seguimiento y análisis</span>

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de
filtros. Todos ellos son parámetros que se pueden agregar a un mensaje de correo electrónico para
habilitar funciones específicas,
como el seguimiento por clics, Google Analytics, el seguimiento de suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte
[Filter Settings][Filter Settings].

Es posible aplicar filtros a los mensajes de correo electrónico de **SendGrid** con métodos
implementados como parte de una clase de **SendGrid**.

En los siguientes ejemplos se muestra el uso de los filtros de pie de página y
seguimiento por clics:

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

## <a name="useservices"></a><span class="short-header">Direccionamiento del servicios adicionales de SendGrid</span>

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de
SendGrid desde su aplicación de Azure. Para obtener
toda la información al respecto, consulte la [documentación de las API de SendGrid][documentación de las API de SendGrid].

## <a name="nextsteps"></a><span class="short-header">Pasos siguientes</span>Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid,
siga estos vínculos para obtener más información:

-   Repositorio de bibliotecas C# de SendGrid: [sendgrid-csharp][sendgrid-csharp]
-   Documentación sobre la API de SendGrid <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial de SendGrid para clientes de Azure: [][]<http://sendgrid.com></a>

  [Pasos siguientes]: #nextsteps
  [Creación de una cuenta de SendGrid]: #createaccount
  [Incorporación de referencias de la biblioteca de clases de .NET a SendGrid]: #reference
  [Creación de un correo electrónico]: #createemail
  [1]: #sendemail
  [Envío de un archivo adjunto]: #addattachment
  [Uso de filtros para habilitar pies de página, seguimiento y análisis]: #usefilters
  [Uso de servicios adicionales de SendGrid]: #useservices
  [servicio de correo electrónico basado en la nube]: http://sendgrid.com/solutions
  [entrega de correo electrónico transaccional]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [paquete NuGet de SendGrid]: https://www.nuget.org/packages/Sendgrid
  [Paquete NuGet de SendGrid]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [utilizar la biblioteca incorporada en .NET]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [Filter Settings]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [documentación de las API de SendGrid]: http://docs.sendgrid.com/documentation/api/
