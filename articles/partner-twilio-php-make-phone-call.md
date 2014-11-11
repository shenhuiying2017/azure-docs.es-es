<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Realización de una llamada telefónica con Twilio en una aplicación PHP en Azure

El siguiente ejemplo muestra cómo se puede usar Twilio para realizar una llamada desde una página web PHP hospedada en Azure. La aplicación resultante le pedirá al usuario los valores de una llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y PHP][Formulario de llamada de Azure con Twilio y PHP]

Tendrá que hacer lo siguiente para utilizar el código de este tema:

1.  Adquiera una cuenta de Twilio y un token de autenticación. Para empezar con Twilio, evalúe los precios en [][]<http://www.twilio.com/pricing></a>. Puede registrarse para obtener una cuenta de prueba en [][1]<https://www.twilio.com/try-twilio></a>. Para obtener información acerca de la API proporcionada por Twilio, consulte [][2]<http://www.twilio.com/api></a>.
2.  Compruebe su número de teléfono como identificador de llamadas salientes con Twilio. Para obtener información acerca de cómo comprobar su número de teléfono, consulte [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Como alternativa al uso de un número existente, puede adquirir un número de teléfono en Twilio.
    A efectos de este ejemplo, use el número de teléfono comprobado como el valor **From** de **caliform.php** (descrito más adelante).
3.  Obtenga la biblioteca de Twilio para PHP. Puede descargarla desde Github ([][4]<https://github.com/twilio/twilio-php></a>) o instalarla como paquete PEAR. Para obtener más información, consulte [][5]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.
4.  Instale el SDK de Azure para PHP. Para obtener información general sobre el SDK e instrucciones sobre cómo instalarlo, consulte [Set up the Azure SDK for PHP][Set up the Azure SDK for PHP].

## Creación de un formulario web para hacer una llamada

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

## Creación del código para realizar la llamada

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

Además de realizar la llamada, **makecall.php** muestra algunos metadatos de llamada (en la captura de pantalla siguiente se muestra un ejemplo). Para obtener información acerca de los metadatos de llamada, consulte [][6]<https://www.twilio.com/docs/api/rest/call#instance-properties></a>.

![Respuesta de llamada de Azure con Twilio y PHP][Respuesta de llamada de Azure con Twilio y PHP]

## Ejecución de la aplicación

El paso siguiente consiste en implementar la aplicación en Sitios web Azure. Los artículos siguientes contienen la información necesaria para crear un sitio web e implementar el código con Git, FTP o WebMatrix (si bien no toda la información de cada uno de los artículos es relevante):

-   [Creación de un sitio web Azure de PHP-MySQL e implementación mediante Git][Creación de un sitio web Azure de PHP-MySQL e implementación mediante Git]
-   [Creación de un sitio web PHP-MySQL de Azure e implementación mediante FTP][Creación de un sitio web PHP-MySQL de Azure e implementación mediante FTP]
-   [Creación de un sitio web Azure de PHP-MySQL e implementación mediante WebMatrix][Creación de un sitio web Azure de PHP-MySQL e implementación mediante WebMatrix]

## Pasos siguientes

Este código se ha proporcionado para mostrar la funcionalidad básica usando Twilio en PHP en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

-   En lugar de usar un formulario web, puede usar blobs de almacenamiento de Azure o una base de datos SQL para almacenar los números de teléfono y el texto de llamada. Para obtener información sobre el uso de blobs de almacenamiento de Azure en PHP, consulte [Using Azure Storage with PHP Applications][Using Azure Storage with PHP Applications]. Para obtener información sobre el uso de bases de datos SQL en PHP, consulte [Usar Base de datos SQL de Azure con aplicaciones PHP][Usar Base de datos SQL de Azure con aplicaciones PHP].
-   El código de **makecall.php** usa una dirección URL ([][7]<http://twimlets.com/message></a>) de Twilio para proporcionar una respuesta de Lenguaje de marcado de Twilio (TwiML) que indica a Twilio cómo proceder con la llamada. Por ejemplo, el TwiML que se devuelve puede contener un verbo `<Say>` que resulta en texto que se comunica al destinatario de la llamada. En lugar de usar la dirección URL proporcionada por Twilio, podría construir su propio servicio para responder a la solicitud de Twilio; para obtener más información, consulte [Uso de Twilio para capacidades de voz y SMS en PHP][Uso de Twilio para capacidades de voz y SMS en PHP]. Puede encontrar más información sobre TwiML en [][8]<http://www.twilio.com/docs/api/twiml></a>, y más información sobre `<Say>` y otros verbos de Twilio en [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Lea las directrices de seguridad de Twilio en [][10]<https://www.twilio.com/docs/security></a>.

Para obtener información adicional acerca de Twilio, consulte [][11]<https://www.twilio.com/docs></a>.

## Otras referencias

-   [Uso de Twilio para capacidades de voz y SMS en PHP][Uso de Twilio para capacidades de voz y SMS en PHP]

  [Formulario de llamada de Azure con Twilio y PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: https://github.com/twilio/twilio-php
  [5]: https://github.com/twilio/twilio-php/blob/master/README.md
  [Set up the Azure SDK for PHP]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
  [6]: https://www.twilio.com/docs/api/rest/call#instance-properties
  [Respuesta de llamada de Azure con Twilio y PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
  [Creación de un sitio web Azure de PHP-MySQL e implementación mediante Git]: https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-git/
  [Creación de un sitio web PHP-MySQL de Azure e implementación mediante FTP]: https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-ftp/
  [Creación de un sitio web Azure de PHP-MySQL e implementación mediante WebMatrix]: https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-webmatrix/
  [Using Azure Storage with PHP Applications]: http://msdn.microsoft.com/es-es/library/windowsazure/hh674502(v=vs.103).aspx
  [Usar Base de datos SQL de Azure con aplicaciones PHP]: http://msdn.microsoft.com/es-es/library/windowsazure/hh674500(v=vs.103).aspx
  [7]: http://twimlets.com/message
  [Uso de Twilio para capacidades de voz y SMS en PHP]: ../partner-twilio-php-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
