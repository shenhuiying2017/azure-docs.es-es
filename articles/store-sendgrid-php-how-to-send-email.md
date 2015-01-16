<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="Uso del servicio de correo electrónico SendGrid (PHP) - Azure" metaKeywords="Azure SendGrid, servicio de correo electrónico de Azure, Azure SendGrid PHP, correo electrónico PHP para Azure" description="Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Ejemplos de código escritos en PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />

# Uso del servicio de correo electrónico SendGrid desde PHP

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en PHP.
Entre los escenarios descritos, se incluyen la **construcción de correo electrónico**, **el envío de correo electrónico**y **la incorporación de datos adjuntos**. Para obtener más información acerca de SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [Qué es el servicio de correo electrónico SendGrid][]
-   [Creación de una cuenta de SendGrid][]
-   [Uso de SendGrid desde una aplicación PHP][]
-   [Eliminación de un correo electrónico][]
-   [Eliminación de un archivo adjunto][]
-   [Direccionamiento del filtros para habilitar pies de página, seguimiento y Twitter][]
-   [Pasos siguientes][]

## <a name="bkmk_WhatIsSendGrid"> </a>¿Qué es el servicio de correo electrónico SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona entrega confiable de
[correo electrónico transaccional], escalabilidad y análisis en tiempo real junto con API flexibles
que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:
:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes de forma mensual
    mensajes electrónicos promocionales y ofertas especiales
-   Recopilación de métricas en tiempo real para correo electrónico bloqueado, por ejemplo, y
    la capacidad de respuesta de los clientes
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
- Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte [https://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Creación de una cuenta de SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Uso de SendGrid desde una aplicación PHP

El uso de SendGrid en una aplicación PHP de Azure no requiere ninguna
codificación o configuración especial. Como SendGrid es un servicio, se puede obtener acceso a él
exactamente de la misma manera desde una aplicación en la nube que desde
una aplicación local.

## <a name="bkmk_HowToSendEmail"> </a>Direccionamiento del un correo electrónico

Puede enviar correo electrónico usando SMTP o la API web que proporciona
SendGrid.

### API SMTP

Para enviar correo electrónico mediante la API SMTP de SendGrid, use *Swift Mailer*, una
biblioteca basada en componentes para enviar correo electrónico desde aplicaciones PHP. Puede
descargar la biblioteca *Swift Mailer* desde
[http://swiftmailer.org/download][]. El envío de correo electrónico con la biblioteca
supone crear instancias de las clases
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> y
<span class="auto-style2">Swift\_Message</span>, mediante la definición de las
propiedades adecuadas y la llamada al método
<span class="auto-style2">Swift\_Mailer::send</span>.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
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

Use la [función curl][] de PHP para enviar correo electrónico mediante la API web de SendGrid.

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

La API web de SendGrid es muy parecida a una API de REST, aunque no es
realmente una API de RESTful dado que, en la mayoría de las llamadas, los verbos GET y POST
se pueden usar indistintamente.

## <a name="bkmk_HowToAddAttachment"> </a>Direccionamiento del un archivo adjunto

### API SMTP

El envío de datos adjuntos mediante la API SMTP supone una línea adicional de
código al script de ejemplo para enviar un correo electrónico con Mailer Swift.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

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
          echo "Something went wrong - "
          print_r($failures);
     }

La línea adicional de código es la siguiente:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Esta línea de código llama al método attach en el objeto
<span class="auto-style2">Swift\_Message</span> y usa el método static
 <span class="auto-style2">fromPath</span> en la clase
<span class="auto-style2">Swift\_Attachment</span> para obtener y
adjuntar un archivo a un mensaje.

### API Web

El envío de datos adjuntos mediante la API web es muy parecido al envío de un
correo electrónico mediante la API web. Sin embargo, tenga en cuenta que en el ejemplo siguiente,
la matriz de parámetros debe contener este elemento:

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

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

## <a name="bkmk_HowToUseFilters"> </a>Direccionamiento del filtros para habilitar pies de página, seguimiento y Twitter

SendGrid proporciona funciones de correo electrónico adicionales mediante el uso de
'filters'. Estos son los valores que se pueden agregar a un mensaje de correo electrónico para
permitir funciones específicas como habilitar el seguimiento del número de clics, el análisis de
Google, el seguimiento de suscripciones, etc.

Los filtros se pueden aplicar a un mensaje usando la propiedad filters. Cada
se especifica mediante un hash que contiene configuración específica del filtro. El
el ejemplo siguiente habilita el filtro de pie de página y especifica un mensaje de texto
que se anexará a la parte inferior del mensaje de correo electrónico:

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' => 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=>'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a>Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga
estos vínculos para obtener más información.

-   Documentación de SendGrid: <https://sendgrid.com/docs>
-   Oferta especial de SendGrid para clientes de Azure: <https://sendgrid.com/windowsazure.html>

  [Pasos siguientes]: #bkmk_NextSteps
  [Qué es el servicio de correo electrónico SendGrid]: #bkmk_WhatIsSendGrid
  [Creación de una cuenta de SendGrid]: #bkmk_CreateSendGrid
  [Uso de SendGrid desde una aplicación PHP]: #bkmk_UsingSendGridfromPHP
  [Eliminación de un correo electrónico]: #bkmk_HowToSendEmail
  [Eliminación de un archivo adjunto]: #bkmk_HowToAddAttachment
  [Direccionamiento del filtros para habilitar pies de página, seguimiento y Twitter]: #bkmk_HowToUseFilters
  [Direccionamiento del servicios adicionales de SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [oferta especial]: https://www.sendgrid.com/windowsazure.html
  [Empaquetado e implementación de aplicaciones PHP para Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [función curl]: http://php.net/curl
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
