<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Java) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Java, Azure email Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="waltpo" solutions="" manager="" editor="mollybos" />

Envío de correo electrónico con SendGrid desde Java
===================================================

Esta guía describe cómo realizar tareas de comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en Java. Los escenarios cubiertos incluyen **crear un correo electrónico**, **enviar un correo electrónico**, **agregar archivos adjuntos**, **utilizar filtros** y **actualizar propiedades**. Para obtener más información acerca de SendGrid y el envío de correos electrónicos, consulte la sección [Pasos siguientes](#bkmk_NextSteps).

Tabla de contenido
------------------

-   [¿Qué es el servicio de correo electrónico SendGrid?](#bkmk_WhatIsSendGrid)
-   [Creación de una cuenta de SendGrid](#bkmk_CreateSendGridAcct)
-   [Uso de las bibliotecas javax.mail](#bkmk_HowToUseJavax)
-   [Creación de un correo electrónico](#bkmk_HowToCreateEmail)
-   [Envío de un correo electrónico](#bkmk_HowToSendEmail)
-   [Incorporación de un archivo adjunto](#bkmk_HowToAddAttachment)
-   [Uso de filtros para habilitar pies de página, seguimiento y análisis](#bkmk_HowToUseFilters)
-   [Actualización de las propiedades del correo electrónico](#bkmk_HowToUpdateEmail)
-   [Uso de servicios adicionales de SendGrid](#bkmk_HowToUseAdditionalSvcs)
-   [Pasos siguientes](#bkmk_NextSteps)

¿Qué es el servicio de correo electrónico SendGrid?
---------------------------------------------------

SendGrid es un [servicio de correo electrónico basado en la nube](http://sendgrid.com/solutions) (en inglés) que proporciona un sistema fiable de [entrega de correos electrónicos transaccional](http://sendgrid.com/transactional-email) (en inglés), escalabilidad y análisis en tiempo real, además de API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de métricas en tiempo real para correo electrónico bloqueado, por ejemplo, y la capacidad de respuesta del cliente
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
-   Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte <http://sendgrid.com>.

Creación de una cuenta de SendGrid
----------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Uso de las bibliotecas javax.mail
---------------------------------

Obtenga las bibliotecas javax.mail, por ejemplo desde <http://www.oracle.com/technetwork/java/javamail>, e impórtelas a su código. En un alto nivel, el proceso para utilizar la biblioteca javax.mail para enviar correo electrónico a través de SMTP es el siguiente:

1.  Especifique los valores de SMTP, incluido el servidor SMTP que, para SendGrid, es smtp.sendgrid.net.

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "su_nombredeusuario_sendgrid";
           private static final String SMTP_AUTH_PWD = "su_contraseña_sendgrid";
            
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
              // …

2.  Extienda la clase javax.mail.Authenticator y, en su implementación del método getPasswordAuthentication, devuelva su nombre de usuario y contraseña de SendGrid.

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Cree una sesión de correo electrónico autenticado a través de un objeto javax.mail.Session.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Cree su mensaje y asigne los valores **Para**, **De**, **Asunto** y los valores de contenido. Esto aparece en la sección [Envío de un correo electrónico](#bkmk_HowToCreateEmail).
5.  Envíe el mensaje a través de un objeto javax.mail.Transport. Esto aparece en la sección [Envío de un correo electrónico](#bkmk_HowToSendEmail).

Creación de un correo electrónico
---------------------------------

A continuación se muestra cómo especificar valores para un correo electrónico.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hola, ya se envió su pedido de Contoso. Gracias, Juan");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hola,</p>
        <p>Ya se envió su pedido de <b>Contoso</b>.</p>
        <p>Gracias,<br>Juan</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("juan@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("alguien@ejemplo.com"));
    message.setSubject("Su pedido reciente");
    message.setContent(multipart);

Envío de un correo electrónico
------------------------------

A continuación se muestra cómo enviar un correo electrónico.

    Transport transport = mailSession.getTransport();
    // Conecte el objeto de transporte.
    transport.connect();
    // Envíe el mensaje.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Cierre la conexión.
    transport.close();

Incorporación de un archivo adjunto
-----------------------------------

El siguiente código muestra cómo agregar un archivo adjunto.

    // Nombre y ruta del archivo local.
    String attachmentName = "miarchivo.zip";
    String attachmentPath = "c:\misarchivos\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Especifique el archivo local para adjuntar.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // Este ejemplo utiliza el nombre de archivo local como nombre del archivo adjunto.
    // Pueden ser distintos si así lo prefiere.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

Uso de filtros para habilitar pies de página, seguimiento y análisis
--------------------------------------------------------------------

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de *filtros*. Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings](http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html).

-   El siguiente código muestra cómo insertar un filtro de pie de página que hace que aparezca texto HTML en la parte inferior del correo electrónico que se envía.

		message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
			{\"enable\":1,\"text/html\": 
			\"<html data-morhtml="true"><b data-morhtml="true">Gracias</b> por su negocio.</html>\"}}}}");

-   Otro ejemplo de un filtro es el seguimiento de clics. Digamos que el texto de su correo electrónico contiene un hipervínculo, como el siguiente, y que desea hacer un seguimiento del número de clics:

        messagePart.setContent(
            "Hola,
            <p>Este es el cuerpo del mensaje. Visite 
            <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
            Gracias.", 
            "text/html");

-   Para permitir el seguimiento de los clics, utilice el siguiente código:

		message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
				{\"enable\":1}}}}")

Actualización de las propiedades del correo electrónico
-------------------------------------------------------

Es posible sobrescribir algunas propiedades de correo electrónico con **set*propiedad*** o anexarlas con **add*propiedad***.

Por ejemplo, para especificar direcciones de respuesta en **ReplyTo**, utilice el siguiente código:

    InternetAddress addresses[] = 
        { new InternetAddress("juan@contoso.com"),
          new InternetAddress("andrea@contoso.com") };

    message.setReplyTo(addresses);

Para agregar a un destinatario **Cc**, utilice el siguiente código:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("juan@contoso.com"));

Uso de servicios adicionales de SendGrid
----------------------------------------

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte [SendGrid API documentation](http://sendgrid.com/docs/API_Reference/index.html).

Pasos siguientes
----------------

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información.

-   Ejemplo que demuestra el uso de SendGrid en una implementación de Azure: [Envío de correo electrónico con SendGrid desde Java en una implementación de Azure](/es-es/develop/java/how-to-guides/sendgrid-sample/)
-   Información sobre Java de SendGrid: <http://sendgrid.com/docs/Code\_Examples/java.html>
-   Documentación sobre la API de SendGrid <http://sendgrid.com/docs/API\_Reference/index.html>
-   Oferta especial de SendGrid para clientes de Azure: <http://sendgrid.com/azure.html>


