<properties urlDisplayName="SendGrid Email Service" pageTitle="Cómo usar el servicio de correo electrónico SendGrid (Java) - Azure" metaKeywords="Azure SendGrid, servicio de correo electrónico de Azure, Azure SendGrid Java, correo electrónico Java de Azure" description="Aprenda a enviar correos electrónicos con el servicio de correo electrónico SendGrid en Azure. Los ejemplos de código están escritos en Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Envío de correo electrónico con SendGrid desde Java

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en Java. Los escenarios cubiertos incluyen **crear un correo electrónico**, **enviar un correo electrónico**, **agregar archivos adjuntos**, **utilizar filtros** y **actualizar propiedades**. Para obtener más información acerca de SendGrid y el envío de correos electrónicos, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [¿Qué es el servicio de correo electrónico SendGrid?][]
-   [Creación de una cuenta de SendGrid][]
-   [Uso de las bibliotecas javax.mail][]
-   [Creación de un correo electrónico][]
-   [Envío de un correo electrónico][]
-   [Adición de un archivo adjunto][]
-   [Uso de filtros para habilitar pies de página, seguimiento y análisis][]
-   [Actualización de las propiedades del correo electrónico][]
-   [Uso de servicios adicionales de SendGrid][]
-   [Pasos siguientes][]

## <a name="bkmk_WhatIsSendGrid"> </a>¿Qué es el servicio de correo electrónico SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] (en inglés) que proporciona un sistema fiable de [entrega de correos electrónicos transaccional] (en inglés), escalabilidad y análisis en tiempo real, además de API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de métricas en tiempo real para correo electrónico bloqueado, por ejemplo, y la capacidad de respuesta del cliente
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
- Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Creación de una cuenta de SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Reproducción de las bibliotecas javax.mail

Obtenga las bibliotecas javax.mail, por ejemplo desde <http://www.oracle.com/technetwork/java/javamail> e impórtelas a su código. En un alto nivel, el proceso para utilizar la biblioteca javax.mail para enviar correo electrónico a través de SMTP es el siguiente:

1.  Especifique los valores de SMTP, incluido el servidor SMTP que,
    para SendGrid, es smtp.sendgrid.net.
    
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
	       private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
	       private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
        
		   public static void main(String[] args) throws Exception{
         	  new MyEmailer().SendMail();
           }
        
		   public void SendMail() throws Exception
           {
              Properties properties = new Properties();
           	  properties.put("mail.transport.protocol", "smtp");
           	  properties.put("mail.smtp.host", SMTP_HOST_NAME);
           	  properties.put("mail.smtp.port", 587);
           	  properties.put("mail.smtp.auth", "true");
           	  // ...

2.  Extender la clase <span class="auto-style1">javax.mail.Authenticator</span>     y, en la implementación del método     <span class="auto-style1">getPasswordAuthentication</span> , devuelva su nombre de usuario de SendGrid y la contraseña.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Cree una sesión de correo electrónico autenticado a través de un objeto
    <span class="auto-style1">javax.mail.Session</span> .  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Cree su mensaje y asigne los valores **Para**, **De**, **Asunto** y los valores de contenido. Esto aparece en la sección [Envío de un correo electrónico](#bkmk_HowToCreateEmail) .
5.  Envíe el mensaje a través de un objeto     <span class="auto-style1">javax.mail.Transport</span> . Esto aparece en la sección [Envío de un correo electrónico][How to: Send an Email].

## <a name="bkmk_HowToCreateEmail"> </a>Reproducción de un correo electrónico

A continuación se muestra cómo especificar valores para un correo electrónico.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
		"<p>Hello,</p>
		<p>Your Contoso order has <b>shipped</b>.</p>
		<p>Thank you,<br>John</br></p>",
		"text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="bkmk_HowToSendEmail"> </a>Reproducción de un correo electrónico

A continuación se muestra cómo enviar un correo electrónico.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Reproducción de un archivo adjunto

El siguiente código muestra cómo agregar un archivo adjunto.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Reproducción de filtros para habilitar pies de página, seguimiento y análisis

SendGrid proporciona funcionalidad de correo electrónico adicional mediante el uso de *filtros*. Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings][].

-   El siguiente código muestra cómo insertar un filtro de pie de página que hace que aparezca
    texto HTML en la parte inferior del correo electrónico que se envía.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Otro ejemplo de un filtro es el seguimiento de clics. Digamos que el texto de su correo electrónico contiene un hipervínculo, como el siguiente, y que quiere hacer un seguimiento del número de clics:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   To enable the click tracking, use the following code:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Reproducción de las propiedades del correo electrónico

Es posible sobrescribir algunas propiedades de correo electrónico con **set*Property*** o anexarlas con **add*Property***.

Por ejemplo, para especificar direcciones de respuesta en **ReplyTo**, utilice el siguiente código:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Para agregar a un destinatario **Cc**, utilice el siguiente código:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Reproducción de servicios adicionales de SendGrid

SendGrid ofrece API basadas en web que puede usar para aprovechar la
funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener
toda la información al respecto, consulte la [documentación de la API de SendGrid][].

## <a name="bkmk_NextSteps"> </a>Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga
estos vínculos para obtener más información.

* Ejemplo que demuestra el uso de SendGrid en una implementación de Azure: [Envío de correos electrónicos con SendGrid desde Java en una implementación de Azure](../store-sendgrid-java-how-to-send-email-example/)
* SDK de Java de SendGrid: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentación sobre la API de SendGrid <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial de SendGrid para clientes de Azure: <https://sendgrid.com/windowsazure.html>

  [Pasos siguientes]: #bkmk_NextSteps
  [¿Qué es el servicio de correo electrónico SendGrid?]: #bkmk_WhatIsSendGrid
  [Creación de una cuenta de SendGrid]: #bkmk_CreateSendGridAcct
  [Uso de las bibliotecas javax.mail]: #bkmk_HowToUseJavax
  [Creación de un correo electrónico]: #bkmk_HowToCreateEmail
  [Envío de un correo electrónico]: #bkmk_HowToSendEmail
  [Adición de un archivo adjunto]: #bkmk_HowToAddAttachment
  [Uso de filtros para habilitar pies de página, seguimiento y Twitter]: #bkmk_HowToUseFilters
  [Actualización de las propiedades del correo electrónico]: #bkmk_HowToUpdateEmail
  [Uso de servicios adicionales de SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Configuración del filtro]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Documentación sobre la API de SendGrid]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email

<!--HONumber=35.2-->
