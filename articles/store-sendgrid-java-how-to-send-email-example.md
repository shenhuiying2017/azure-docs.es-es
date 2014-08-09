<properties  linkid="store-requestform-preview" urlDisplayName="Request Azure Store Integration" pageTitle="How to Send Email Using SendGrid from Java in an Azure Deployment" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Send Email Using SendGrid from Java in an Azure Deployment" authors="waltpo" solutions="" manager="" editor="mollybos" videoId="" scriptId="" />

# Envío de correo electrónico con SendGrid desde Java en una implementación de Azure

En el ejemplo siguiente se muestra cómo puede utilizar SendGrid para enviar correos electrónicos desde una página web hospedada en Azure. La aplicación resultante solicitará al usuario valores de correo electrónico, tal como se muestra en la siguiente captura de pantalla.

![Formulario de correo
electrónico](./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg)

El correo electrónico resultante debería ser similar a la siguiente captura de pantalla.

![Mensaje de correo
electrónico](./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg)

Tendrá que hacer lo siguiente para utilizar el código de este tema:

1.  Obtenga los JAR javax.mail, por ejemplo desde <http
    data-morhtml="true"://www.oracle.com/technetwork/java/javamail/index.html>.
2.  Agregue los JAR a la ruta de acceso de la compilación Java. 
3.  Si está utilizando Eclipse para crear esta aplicación Java, puede
    incluir las bibliotecas SendGrid en el archivo de implementación de
    aplicación (WAR) utilizando la característica de ensamblado de
    implementación de Eclipse. Si no está utilizando Eclipse para crear
    esta aplicación Java, asegúrese de que las bibliotecas se incluyen
    en el mismo rol de Azure que la aplicación Java y que se agregan a
    la ruta de acceso de clase de la aplicación.

También debe tener su propio nombre de usuario y contraseña de SendGrid para poder enviar el correo electrónico. Para comenzar con SendGrid, consulte [Envío de correo electrónico con SendGrid desde Java](../store-sendgrid-java-how-to-send-email).

Además, se recomienda estar familiarizado con la información que encontrará en [Creación de una aplicación Hello World para Azure en Eclipse][1] o con otras técnicas para hospedar aplicaciones Java en Azure si no está utilizando Eclipse.

## Creación de un formulario web para el envío del correo electrónico

El código siguiente muestra cómo crear un formulario web para recuperar datos de usuario para el envío de correo electrónico. Para los fines de este contenido, el archivo JSP se denomina **emailform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta  http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Formulario de correo electrónico</title>
    </head>
    <body>
     <p>Rellene los campos y haga clic en <b>Send this email</b>.</p>
     <br  />
      <form  action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>A:</td>
           <td><input  type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>De:</td>
           <td><input  type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Asunto:</td>
           <td><input  type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Texto:</td>
           <td><input  type="text" size=400 name="emailText" value="Hello,<p>Este es mi mensaje.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>Nombre de usuario de SendGrid:</td>
           <td><input  type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>Contraseña de SendGrid:</td>
           <td><input  type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td  colspan=2><input  type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br  />
    </body>
    </html>

## Creación del código para enviar el correo electrónico

El código siguiente, que se llama cuando completa el formulario en emailform.jsp, crea el mensaje de correo electrónico y lo envía. Para los fines de este contenido, el archivo JSP se denomina
**sendemail.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta  http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>El procesamiento del correo electrónico tiene lugar aquí</title>
    </head>
    <body>
        <b>Esta es mi página de envío de correo.</b><p data-morhtml="true"/>
     <%
     
     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");
     
     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;
          
            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {
         
         // El servidor SendGrid SMTP.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";
    
         Properties properties;
        
         properties = new Properties();
         
         // Especificar valores SMTP.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");
         
         // Mostrar los campos de correo electrónico especificados por el usuario. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br data-morhtml="true"/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br data-morhtml="true"/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br data-morhtml="true"/>");
         out.println("Value entered for email    Text: " + "<br data-morhtml="true"/>" + request.getParameter("emailText") + "<br data-morhtml="true"/>");
    
         // Crear el objeto autenticador.
         Authenticator authenticator = new SMTPAuthenticator();
         
         // Crear el objeto de sesión de correo.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);
         
         // Mostrar información de depuración en stdout, útil al utilizar el
         // emulador de proceso durante el desarrollo.
         mailSession.setDebug(true);
    
         // Crear el mensaje y los objetos de parte de mensaje.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 
         
         message = new MimeMessage(mailSession);
         
         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            
    
         // Especificar los campos Para, De, Asunto y Contenido del correo electrónico. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);
         
         // Quitar el comentario de lo siguiente si desea agregar un pie de página.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
    
         // Quitar el comentario de lo siguiente si desea habilitar el seguimiento por clics.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
         
         Transport transport;
         transport = mailSession.getTransport();
         // Conectar el objeto de transporte.
         transport.connect();
         // Enviar el mensaje.
         transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
         // Cerrar la conexión.
         transport.close();
     
        out.println("<p>Email processing completed.</p>");
         
     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>
    
    </body>
    </html>

Además de enviar el correo electrónico, emailform.jsp ofrece un resultado al usuario; un ejemplo es la siguiente captura de pantalla:

![Resultado del envío del
correo](./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg)

## Pasos siguientes

Implemente la aplicación en el emulador de proceso y, en un explorador, ejecute emailform.jsp, introduzca los valores en el formulario, haga clic en **Send this email** y consulte los resultados en sendemail.jsp.

Este código se incluye para mostrar cómo utilizar SendGrid de Java en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

* Puede utilizar los blogs de almacenamiento de Azure o la Base de datos
  SQL para almacenar direcciones de correo electrónico y mensajes de
  correo electrónico, en lugar de utilizar un formulario web. Para
  obtener más información acerca de cómo utilizar los blobs de
  almacenamiento de Azure en Java, consulte [Uso del servicio de
  almacenamiento de blobs desde Java][2]. Para obtener más información
  acerca de cómo utilizar la Base de datos SQL en Java, consulte [Uso de
  bases de datos SQL en Java][3].
* Puede utilizar `RoleEnvironment.getConfigurationSettings` para
  recuperar el nombre de usuario y la contraseña de SendGrid de la
  configuración de implementación, en lugar de utilizar el formulario
  web para recuperar estos valores. Para obtener más información acerca
  de la clase `RoleEnvironment`, consulte [Uso de la biblioteca de
  tiempo de ejecución del servicio de Azure en JSP][4] y la
  documentación del paquete en tiempo de ejecución del servicio de Azure
  en <http://dl.windowsazure.com/javadoc>.
* Para obtener más información acerca de cómo utilizar SendGrid en Java,
  consulte [Envío de correo electrónico con SendGrid desde
  Java](../store-sendgrid-java-how-to-send-email).



[1]: http://msdn.microsoft.com/es-es/library/windowsazure/hh690944
[2]: http://www.windowsazure.com/es-es/develop/java/how-to-guides/blob-storage/
[3]: http://www.windowsazure.com/es-es/develop/java/how-to-guides/using-sql-azure-in-java/
[4]: http://msdn.microsoft.com/es-es/library/windowsazure/hh690948

