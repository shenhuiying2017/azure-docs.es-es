<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="waltpo" manager="bjsmith" editor="mollybos" />

Realización de una llamada telefónica con Twilio en una aplicación PHP en Azure
===============================================================================

El siguiente ejemplo muestra cómo se puede usar Twilio para realizar una llamada desde una página web PHP hospedada en Azure. La aplicación resultante le pedirá al usuario los valores de una llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y PHP](./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg)

Tendrá que hacer lo siguiente para usar el código de este tema:

1.  Adquiera una cuenta de Twilio y un token de autenticación. Para empezar con Twilio, evalúe los precios en <http://www.twilio.com/pricing>. Puede registrarse para obtener una cuenta de prueba en [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio). Para obtener información acerca de la API proporcionada por Twilio, consulte <http://www.twilio.com/api>.
2.  Compruebe su número de teléfono como identificador de llamadas salientes con Twilio. Para obtener información acerca de cómo comprobar el número de teléfono, consulte [https://www.twilio.com/user/account/phone-numbers/verified \#](https://www.twilio.com/user/account/phone-numbers/verified#). Como alternativa al uso de un número existente, puede adquirir un número de teléfono en Twilio. Para efectos de este ejemplo, use el número de teléfono comprobado como el valor **From** de **caliform.php** (descrito más adelante).
3.  Obtenga la biblioteca de Twilio para PHP. Puede descargarla desde Github (<https://github.com/twilio/twilio-php>) o instalarla como paquete PEAR. Para obtener más información, consulte <https://github.com/twilio/twilio-php/blob/master/README.md>.
4.  Instale el SDK de Azure para PHP. Para obtener información general sobre el SDK e instrucciones sobre cómo instalarlo, consulte [Set up the Azure SDK for PHP](http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php).

Creación de un formulario web para hacer una llamada
----------------------------------------------------

El código HTML siguiente muestra cómo crear una página web (**callform.html**) que recupera datos de usuarios para realizar una llamada:

    <html>
    <head>
        <title>Formulario para llamada automatizada</title>
    </head>
    <body>
    <h1>Formulario para llamada automatizada</h1>
	<p>Rellene todos los campos y haga clic en <b>Make this call</b>.</p> 
	<form action="makecall.php" method="post">
	<table>
         <tr>
            <td>A:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>De:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Mensaje de la llamada:</td>
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

Creación del código para realizar la llamada
--------------------------------------------

El código siguiente muestra cómo crear una página web (**makecall.php**) a la que se llama cuando el usuario envía el formulario mostrado por **callform.html**. El código que se muestra a continuación crea el mensaje de llamada y genera la llamada. (Use su cuenta Twilio y token de autenticación en lugar de los valores de marcador de posición asignados a **$sid** y **$token** en el código siguiente).

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Las llamadas deben realizarse desde un número Twilio registrado.
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

Además de realizar la llamada, **makecall.php** muestra algunos metadatos de llamada (en la captura de pantalla siguiente se muestra un ejemplo). Para obtener información acerca de los metadatos de llamada, consulte <https://www.twilio.com/docs/api/rest/call#instance-properties>.

![Respuesta de llamada de Azure con Twilio y PHP](./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg)

Ejecución de la aplicación
--------------------------

El paso siguiente consiste en implementar la aplicación en Sitios web Azure. Los artículos siguientes contienen la información necesaria para crear un sitio web e implementar el código con Git, FTP o WebMatrix (si bien no toda la información de cada uno de los artículos es relevante):

-   [Creación de un sitio web Azure de PHP-MySQL e implementación mediante Git](https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-git/)
-   [Creación de un sitio web PHP-MySQL de Azure e implementación mediante FTP](https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-ftp/)
-   [Creación de un sitio web Azure de PHP-MySQL e implementación mediante WebMatrix](https://www.windowsazure.com/es-es/develop/php/tutorials/website-w-mysql-and-webmatrix/)

Pasos siguientes
----------------

Este código se ha proporcionado para mostrar la funcionalidad básica usando Twilio en PHP en Azure. Antes de realizar la implementación en el entorno de producción de Azure, es posible que desee agregar un mayor control de errores u otras características. Por ejemplo:

-   En lugar de usar un formulario web, puede usar blobs de almacenamiento de Azure o una base de datos SQL para almacenar los números de teléfono y el texto de llamada. Para obtener información sobre el uso de blobs de almacenamiento de Azure en PHP, consulte [Using Azure Storage with PHP Applications](http://msdn.microsoft.com/es-es/library/windowsazure/hh674502(v=vs.103).aspx). Para obtener información sobre el uso de bases de datos SQL en PHP, consulte [Usar Base de datos SQL de Azure con aplicaciones PHP](http://msdn.microsoft.com/es-es/library/windowsazure/hh674500(v=vs.103).aspx).
-   El código de **makecall.php** usa una dirección URL (<http://twimlets.com/message>) de Twilio para proporcionar una respuesta de Lenguaje de marcado de Twilio (TwiML) que indica a Twilio cómo proceder con la llamada. Por ejemplo, la TwiML que se devuelve puede contener un verbo `<Say>` que se traduce en el texto que se está hablando con el destinatario de la llamada. En lugar de usar la dirección URL proporcionada por Twilio, podría construir su propio servicio para responder a la solicitud de Twilio; para obtener más información, consulte [Uso de Twilio para capacidades de voz y SMS en PHP](../partner-twilio-php-how-to-use-voice-sms). Se puede encontrar más información sobre TwiML en <http://www.twilio.com/docs/api/twiml>, y más información sobre `<Say>` y otros verbos de Twilio en <http://www.twilio.com/docs/api/twiml/say>.
-   Lea las directrices de seguridad de Twilio en [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).

Para obtener información adicional acerca de Twilio, consulte [https://www.twilio.com/docs](http://www.twilio.com/docs).

Otras referencias
-----------------

-   [Uso de Twilio para capacidades de voz y SMS en PHP](../partner-twilio-php-how-to-use-voice-sms)

