<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="" solutions="" manager="" editor="" />

Envío de correos electrónicos con SendGrid y Azure
==================================================

Esta guía describe cómo realizar tareas de comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en C\# y utilizan la API .NET. Entre los escenarios descritos, se incluyen la **creación de correos electrónicos**, el **envío de correos electrónicos**, la **incorporación de datos adjuntos** y el **uso de filtros**. Para obtener más información acerca de SendGrid y el envío de correos electrónicos, consulte la sección [Pasos siguientes](#nextsteps).

Tabla de contenido
------------------

[¿Qué es el servicio de correo electrónico SendGrid?](#whatis)

[Creación de una cuenta de SendGrid](#createaccount)

[Incorporación de referencias de la biblioteca de clases .NET de SendGrid](#reference)

[Creación de un correo electrónico](#createemail)

[Envío de un correo electrónico](#sendemail)

[Incorporación de un archivo adjunto](#addattachment)

[Uso de filtros para habilitar pies de página, seguimiento y análisis](#usefilters)

[Uso de servicios adicionales de SendGrid](#useservices)

[Pasos siguientes](#nextsteps)

¿Qué es el servicio de correo electrónico SendGrid?¿Qué es el servicio de correo electrónico SendGrid?
------------------------------------------------------------------------------------------------------

SendGrid es un [servicio de correo electrónico basado en la nube](http://sendgrid.com/solutions) (en inglés) que proporciona un sistema fiable de [entrega de correos electrónicos transaccional](http://sendgrid.com/transactional-email) (en inglés), escalabilidad y análisis en tiempo real, además de API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a clientes.
-   Administración de las listas de distribución para el envío mensual de mensajes electrónicos promocionales y ofertas especiales a clientes.
-   Recopilación de diversas métricas en tiempo real como, por ejemplo, las direcciones de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias.
-   Reenvío de las consultas de los clientes.

Para obtener más información, consulte <http://sendgrid.com> (en inglés).

Creación de una cuenta de SendGridCreación de una cuenta de SendGrid
--------------------------------------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Incorporación de referencias de la biblioteca de clases .NET de SendGridIncorporación de referencias de la biblioteca de clases .NET de SendGrid
------------------------------------------------------------------------------------------------------------------------------------------------

El paquete NuGet de SendGrid es la forma más fácil de obtener la API de SendGrid y configurar su aplicación con todas las dependencias. NuGet es una extensión de Visual Studio incluida en Microsoft Visual Studio 2012 que facilita la instalación y la actualización de las bibliotecas y las herramientas.

**Nota:**

Para instalar NuGet, si está ejecutando una versión de Visual Studio anterior a Visual Studio 2012, visite <http://www.nuget.org> (en inglés), y haga clic en el botón **Install NuGet**.

Realice los pasos siguientes para instalar el paquete NuGet de SendGrid en su aplicación:

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **References** y, a continuación, en **Manage NuGet Packages**.

2.  En el panel izquierdo del cuadro de diálogo **Manage NuGet Packages**, haga clic en **Online**.

3.  Busque **SendGrid** y seleccione el elemento **SendGrid** en la lista de resultados.

    ![Paquete NuGet de SendGrid](./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png)

4.  Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.

La biblioteca de clases .NET de SendGrid se llama **SendGridMail**. Contiene los siguientes espacios de nombres:

-   **SendGridMail** para la creación y el trabajo con elementos de correo electrónico.
-   **SendGridMail.Transport** para el envío de correo electrónico mediante el protocolo **SMTP** o el protocolo HTTP 1.1 con **Web/REST**.

Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C\# en el que desee obtener acceso al servicio de correo electrónico SendGrid mediante programación: **System.Net** y **System.Net.Mail** son los espacios de nombres de .NET Framework que están incluidos porque contienen los tipos que utilizará normalmente con las API de SendGrid.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

Creación de un correo electrónicoCreación de un correo electrónico
------------------------------------------------------------------

Utilice el método **SendGrid.GetInstance** estático para crear un mensaje de correo electrónico del tipo **SendGrid**. Una vez creado el mensaje, puede utilizar las propiedades y los métodos de **SendGrid** para establecer valores como el remitente, el destinatario, el asunto y el cuerpo del correo electrónico.

El siguiente ejemplo muestra la forma de crear un objeto de correo electrónico completo:

    // Configuración de las propiedades del correo electrónico.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Creación de un correo electrónico, en el que se pasan las ocho propiedades como argumentos.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

El siguiente ejemplo muestra la forma de crear un objeto de correo electrónico vacío:

    // Creación en primer lugar de un objeto de correo electrónico y, a continuación, incorporación de las propiedades.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Incorporación de las propiedades del mensaje.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Incorporación de múltiples direcciones en el campo Para.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Incorporación de un cuerpo de mensaje en formato HTML.
    myMessage.Html = "<p>Hello World!</p>";

    // Incorporación del asunto.
    myMessage.Subject = "Testing the SendGrid Library";

Para obtener más información acerca de todas las propiedades y métodos admitidos por el tipo **SendGrid**, consulte [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp) (en inglés) en GitHub.

Envío de un correo electrónicoEnvío de un correo electrónico
------------------------------------------------------------

Después de crear un mensaje de correo electrónico, puede enviarlo con SMTP o la API Web que proporciona SendGrid. Para obtener más información acerca de los pros y los contras de cada API, consulte [SMTP vs. Web API](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/) (en inglés) en la documentación de SendGrid.

El envío de correos electrónicos con cualquiera de los protocolos requiere que proporcione las credenciales de su cuenta de SendGrid (nombre de usuario y contraseña). El siguiente código demuestra la forma de unificar sus credenciales en un objeto **NetworkCredential**:

    // Creación de las credenciales de red para obtener acceso a su cuenta de SendGrid.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

Para enviar un mensaje de correo electrónico, utilice el método **Deliver** de la clase **SMTP**, que utiliza el protocolo SMTP, o la clase de transporte **REST**, que llama a la API Web de SendGrid. Los siguientes ejemplos muestran la forma de enviar un mensaje con SMTP y la API Web.

### SMTP

    // Creación en primer lugar de un objeto de correo electrónico y, a continuación, incorporación de las propiedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Creación de las credenciales mediante la especificación del nombre de usuario y la contraseña.
    var credentials = new NetworkCredential("username", "password");

    // Creación de un transporte SMTP para el envío del correo electrónico.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Envío del correo electrónico.
    transportSMTP.Deliver(myMessage);

### API Web

    // Creación en primer lugar de un objeto de correo electrónico y, a continuación, incorporación de las propiedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Creación de las credenciales mediante la especificación del nombre de usuario y la contraseña.
    var credentials = new NetworkCredential("username", "password");

    // Creación de un transporte REST para el envío del correo electrónico.
    var transportREST = Web.GetInstance(credentials);

    // Envío del correo electrónico.
    transportREST.Deliver(myMessage);

Incorporación de un archivo adjuntoIncorporación de un archivo adjunto
----------------------------------------------------------------------

Es posible agregar datos adjuntos a un mensaje mediante la llamada al método **AddAttachment** y la especificación del nombre y la ruta del archivo que desea adjuntar. Puede incluir múltiples datos adjuntos mediante la llamada a este método, que debe utilizar una vez por cada archivo que desee adjuntar. El siguiente ejemplo demuestra la incorporación de datos adjuntos a un mensaje:

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Uso de filtros para habilitar pies de página, seguimiento y análisisUso de filtros para habilitar pies de página, seguimiento y análisis
----------------------------------------------------------------------------------------------------------------------------------------

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de filtros. Todos ellos son parámetros que se pueden agregar a un mensaje de correo electrónico para habilitar funciones específicas, como el seguimiento por clics, Google Analytics, el seguimiento de suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

Es posible aplicar filtros a los mensajes de correo electrónico de **SendGrid** con métodos implementados como parte de una clase de **SendGrid**. Antes de que pueda habilitar los filtros en un mensaje de correo electrónico, debe inicializar en primer lugar la lista de filtros disponibles mediante la llamada al método **InitalizeFilters**.

Los siguientes ejemplos demuestran el uso de los filtros de pie de página y seguimiento por clics:

### Pie de página

    // Creación en primer lugar de un objeto de correo electrónico y, a continuación, incorporación de las propiedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Incorporación de un pie de página en el mensaje.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Seguimiento por clics

    // Creación en primer lugar de un objeto de correo electrónico y, a continuación, incorporación de las propiedades.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // "true" indica que los vínculos en fragmentos de texto plano del correo electrónico
    // también deben sobrescribirse para poder realizar el seguimiento del vínculo. myMessage.EnableClickTracking(true);

Uso de servicios adicionales de SendGridUso de servicios adicionales de SendGrid
--------------------------------------------------------------------------------

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte [SendGrid API documentation](http://docs.sendgrid.com/documentation/api/).

Pasos siguientesPasos siguientes
--------------------------------

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

-   Repositorio de bibliotecas C\# de SendGrid: [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)
-   Documentación sobre la API de SendGrid <http://docs.sendgrid.com/documentation/api/>
-   Oferta especial de SendGrid para clientes de Azure: <http://sendgrid.com>

