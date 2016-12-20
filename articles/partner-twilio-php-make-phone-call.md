---
title: "Realización de una llamada telefónica desde Twilio (PHP) | Microsoft Docs"
description: "Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos para la aplicación PHP."
documentationcenter: php
services: 
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 22a1ac74fbed508053c3fb605fa37f6d213e05d5


---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Realización de una llamada telefónica con Twilio en una aplicación PHP en Azure
El siguiente ejemplo muestra cómo se puede usar Twilio para realizar una llamada desde una página web PHP hospedada en Azure. La aplicación resultante le pedirá al usuario los valores de una llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y PHP][twilio_php]

Tendrá que hacer lo siguiente para utilizar el código de este tema:

1. Adquiera una cuenta de Twilio y un token de autenticación. Para comenzar a usar Twilio, puede considerar los precios en [http://www.twilio.com/pricing][twilio_pricing]. Puede registrarse en [https://www.twilio.com/try-twilio][try_twilio]. para obtener una cuenta de prueba. Para obtener información sobre la API proporcionada por Twilio, consulte [http://www.twilio.com/api][twilio_api].
2. Instale la [biblioteca de Twilio para PHP](https://github.com/twilio/twilio-php) como paquete PEAR. Para obtener más información, consulte el [archivo Léame](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Instale el SDK de Azure para PHP. Para obtener información general sobre SDK e instrucciones para instalarlo, consulte [Instalar Azure SDK para PHP][setup_php_sdk].

## <a name="create-a-web-form-for-making-a-call"></a>Creación de un formulario web para hacer una llamada
El código HTML siguiente muestra cómo crear una página web (**callform.html**) que recupera datos de usuarios para realizar una llamada:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
      <form action="makecall.php" method="post">
       <table>
         <tr>
               <td>To:</td>
               <td><input type="text" size=50 name="callTo" value=""></td>
         </tr>
         <tr>
               <td>From:</td>
               <td><input type="text" size=50 name="callFrom" value=""></td>
         </tr>
         <tr>
               <td>Call message:</td>
               <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
         </tr>
         <tr>
               <td colspan=2><input type="submit" value="Make this call"></td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Creación del código para realizar la llamada
El código siguiente muestra cómo crear una página web (**makecall.php**) a la que se llama cuando el usuario envía el formulario mostrado por **callform.html**. El código que se muestra a continuación crea el mensaje de llamada y genera la llamada. (Use su cuenta Twilio y token de autenticación en lugar de los valores de marcador de posición asignados a **$sid** y **$token** en el código siguiente).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number,
        $to_number,
          'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Además de realizar la llamada, **makecall.php** muestra algunos metadatos de llamada (en la captura de pantalla siguiente se muestra un ejemplo). Para obtener más información sobre los metadatos de llamada, consulte [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Respuesta de llamada de Azure con Twilio y PHP][twilio_php_response]

## <a name="run-the-application"></a>Ejecución de la aplicación
El paso siguiente consiste en implementar la aplicación en Sitios web Azure. Los artículos siguientes contienen la información necesaria para crear un sitio web e implementar el código con Git, FTP o WebMatrix (si bien no toda la información de cada uno de los artículos es relevante):

* [Creación de un sitio web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante Git](app-service-web/web-sites-php-mysql-deploy-use-git.md)
* [Creación de un sitio web PHP-MySQL en el Servicio de aplicaciones de Azure e implementación mediante FTP](app-service-web/web-sites-php-mysql-deploy-use-ftp.md)

## <a name="next-steps"></a>Pasos siguientes
Este código se ha proporcionado para mostrar la funcionalidad básica usando Twilio en PHP en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

* En lugar de usar un formulario web, puede usar blobs de almacenamiento de Azure o una base de datos SQL para almacenar los números de teléfono y el texto de llamada. Para obtener información sobre el uso de blobs de almacenamiento de Azure en PHP, consulte el artículo sobre cómo [usar Azure Storage con aplicaciones PHP][howto_blob_storage_php]. Para obtener información sobre el uso de SQL Database en PHP, consulte [Usar las bases de datos SQL con aplicaciones PHP][howto_sql_azure_php].
* El código **makecall.php** usa una dirección URL ([http://twimlets.com/message][twimlet_message_url]) de Twilio para proporcionar una respuesta de Lenguaje de marcado de Twilio (TwiML) que indica a Twilio cómo proceder con la llamada. Por ejemplo, la TwiML que se devuelve puede contener un verbo `<Say>` que se traduce en el texto que se está hablando con el destinatario de la llamada. En lugar de usar la dirección URL proporcionada por Twilio, puede crear su propio servicio para responder a la solicitud de Twilio; para obtener más información, consulte [Usar Twilio para capacidades de voz y SMS en PHP][howto_twilio_voice_sms_php]. Puede encontrar más información sobre TwiML en [http://www.twilio.com/docs/api/twiml][twiml] y sobre `<Say>` y otros verbos de Twilio en [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Puede leer las directrices de seguridad de Twilio en [https://www.twilio.com/docs/security][twilio_docs_security].

Para obtener más información sobre Twilio, consulte [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Otras referencias
* [Uso de Twilio para capacidades de voz y SMS en PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php



<!--HONumber=Nov16_HO3-->


