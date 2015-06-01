<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="Cómo usar el servicio de correo electrónico SendGrid (PHP) - Azure" metaKeywords="Azure SendGrid, servicio de correo electrónico de Azure, Azure SendGrid PHP, correo electrónico PHP de Azure" description="Aprenda a enviar correos electrónicos con el servicio de correo electrónico SendGrid en Azure. Los ejemplos de código están escritos en PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# Uso del servicio de correo electrónico SendGrid desde PHP

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en PHP.
Entre los escenarios descritos, se incluyen la **creación de correos electrónicos**, el **envío de correos electrónicos** y la **incorporación de datos adjuntos**. Para obtener más información acerca de SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [Qué es el servicio de correo electrónico SendGrid][]
-   [Creación de una cuenta de SendGrid][]
-   [Uso de SendGrid desde una aplicación PHP][]
-   [Envío de un correo electrónico][]
-   [Adición de un archivo adjunto][]
-   [Uso de filtros para habilitar pies de página, seguimiento y Twitter][]
-   [Pasos siguientes][]

## <a name="bkmk_WhatIsSendGrid"> </a>¿Qué es el servicio de correo electrónico SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] (en inglés) que proporciona un sistema fiable de [entrega de correos electrónicos transaccional] (en inglés), escalabilidad y análisis en tiempo real, además de API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de diversas métricas en tiempo real, como las direcciones de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
- Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte [https://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Creación de una cuenta de SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Uso de SendGrid desde una aplicación PHP

El uso de SendGrid en una aplicación PHP de Azure no requiere
una configuración ni una codificación especiales. Puesto que SendGrid es un servicio,
se accede a él exactamente desde una aplicación en la nube que desde
una aplicación local.

## <a name="bkmk_HowToSendEmail"> </a>Reproducción de un correo electrónico

Puede enviar correos electrónicos usando SMTP o la API web que proporciona
SendGrid.

### API SMTP

Para enviar correo electrónico mediante la API SMTP de SendGrid, use *Swift Mailer*, una biblioteca basada en componentes para el envío de correo electrónico desde aplicaciones PHP. Puede descargar la biblioteca de *Swift Mailer* de [http://swiftmailer.org/download][] v5.3.0 (use [Compositor] para instalar Swift Mailer). El envío de correo electrónico con la biblioteca implica crear instancias de las clases <span class="auto-style2">Swift_SmtpTransport</span>, <span class="auto-style2">Swift_Mailer</span>y <span class="auto-style2">Swift_Message</span> establecer las propiedades correspondientes y llamar al método <span class="auto-style2">Swift_Mailer::send</span> método.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### API Web

Use la [función curl][] de PHP para enviar correo electrónico usando la API web de SendGrid.

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

La API web de SendGrid es muy similar a la API de REST, aunque no es realmente una API de RESTful porque, en la mayoría de las llamadas, se pueden usar los verbos GET y POST indistintamente.

## <a name="bkmk_HowToAddAttachment"> </a>Reproducción de un archivo adjunto

### API SMTP

El envío de datos adjuntos con la API SMTP implica una línea adicional de código al script de ejemplo para enviar un mensaje de correo electrónico con Swift Mailer.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - ";
          print_r($failures);
     }

La línea adicional de código es la siguiente:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Esta línea de código llama al método attach en el objeto <span class="auto-style2">Swift_Message</span> y usa el método estático <span class="auto-style2">fromPath</span> en la clase <span class="auto-style2">Swift_Attachment</span> para obtener y adjuntar un archivo a un mensaje.

### API Web

El envío de datos adjuntos mediante la API web es muy similar al envío de un mensaje de correo electrónico con la API web. No obstante, tenga en cuenta que, en el ejemplo siguiente, el parámetro array debe contener este elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Ejemplo:

    <?php

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@contoso.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>Reproducción de Uso de filtros para habilitar pies de página, seguimiento y análisis

SendGrid proporciona funcionalidad de correo electrónico adicional mediante el uso de "filtros". Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc.

Los filtros se pueden aplicar a un mensaje usando la propiedad filters. Cada filtro se especifica mediante un hash que contiene la configuración específica del filtro. En el siguiente ejemplo se habilita el filtro de pie de página y se especifica un mensaje de texto que se anexará al final del mensaje de correo electrónico. En este ejemplo usaremos [biblioteca sendgrid-php]. Utilice [Compositor] para instalar la biblioteca:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Ejemplo:    

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Cree el cuerpo del mensaje (texto sin formato y una versión en HTML). 
     # texto es el correo electrónico sin formato 
     # HTML es la versión HTML del correo electrónico
     # si el receptor puede ver correos electrónicos HTML, solo
     # se mostrará el correo electrónico HTML

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="bkmk_NextSteps"> </a>Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga
estos vínculos para obtener más información.

-   Documentación de SendGrid: <https://sendgrid.com/docs>
-   Biblioteca de SendGrid PHP: <https://github.com/sendgrid/sendgrid-php>
-   Oferta especial de SendGrid para clientes de Azure: <https://sendgrid.com/windowsazure.html>

  [Pasos siguientes]: #bkmk_NextSteps
  [Qué es el servicio de correo electrónico SendGrid]: #bkmk_WhatIsSendGrid
  [Creación de una cuenta de SendGrid]: #bkmk_CreateSendGrid
  [Uso de SendGrid desde una aplicación PHP]: #bkmk_UsingSendGridfromPHP
  [Envío de un correo electrónico]: #bkmk_HowToSendEmail
  [Adición de un archivo adjunto]: #bkmk_HowToAddAttachment
  [Uso de filtros para habilitar pies de página, seguimiento y Twitter]: #bkmk_HowToUseFilters
  [Uso de servicios adicionales de SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [oferta especial]: https://www.sendgrid.com/windowsazure.html
  [Empaquetar e implementar aplicaciones PHP para Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [función curl]: http://php.net/curl
  [servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
  [entrega de correo electrónico transaccional]: https://sendgrid.com/transactional-email
  [biblioteca sendgrid-php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Compositor]: https://getcomposer.org/download/

<!--HONumber=35.2-->
