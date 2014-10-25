<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Uso de Twilio para capacidades de voz y SMS en PHP

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [¿Qué es Twilio?][]
-   [Precios de Twilio][]
-   [Conceptos][]
-   [Creación de una cuenta de Twilio][]
-   [Comprobación de números de teléfono][]
-   [Creación de una aplicación PHP][]
-   [Configuración de su aplicación para utilizar bibliotecas de Twilio][]
-   [Direccionamiento del una llamada saliente][]
-   [Direccionamiento del mensaje SMS][]
-   [Direccionamiento del TwiML desde su propio sitio web][]

## <span id="WhatIs"></span></a>¿Qué es Twilio?

Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que su aplicación envíe y reciba mensajes de texto. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

## <span id="Pricing"></span></a>Precios de Twilio y ofertas especiales

Los clientes de Azure reciben una [oferta especial][<http://www.twilio.com/azure>]: 10 $ de regalo en crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (10 $ de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en: [][]<http://ahoy.twilio.com/azure></a>.

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en la [página de precios de Twilio][] (en inglés).

## <span id="Concepts"></span></a>Conceptos

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio][] (en inglés).

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos de Twilio

La API utiliza los verbos de Twilio, por ejemplo, el verbo **\<Say\>** indica a Twilio que entregue de manera audible un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio. Obtenga información sobre otros verbos y capacidades a través de [Documentación de lenguaje de marcado de Twilio][<http://www.twilio.com/docs/api/twiml>].

-   **\<Dial\>**: Conecta la persona que llama con otro teléfono.
-   **\<Gather\>**: Recopila los dígitos numéricos que se introdujeron en el teclado del teléfono.
-   **\<Hangup\>**: Finaliza una llamada.
-   **\<Play\>**: Reproduce un archivo de audio.
-   **\<Pause\>**: Espera en silencio una cantidad de segundos específica.
-   **\<Record\>**: Graba la voz de la persona que llama y devuelve una dirección URL de un archivo que contiene la grabación.
-   **\<Redirect\>**: Transfiere el control de una llamada o SMS al TwiML en una URL distinta.
-   **\<Reject\>**: Rechaza una llamada entrante a su número de Twilio sin cobrarle.
-   **\<Say\>**: Convierte texto en voz para hacer una llamada.
-   **\<Sms\>**: Envía un mensaje SMS.

### <span id="TwiML"></span></a>TwiML

TwiML es un conjunto de instrucciones basadas en XML y en los verbos de Twilio que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Cuando su aplicación llama a la API de Twilio, uno de los parámetros de API es la URL que devuelve la respuesta de TwiML. Con fines de desarrollo, puede usar las URL que ofrece Twilio para proporcionar las respuestas de TwiML que usaron sus aplicaciones. También puede hospedar sus propias URL para producir las repuestas de TwiML; otra opción es usar el objeto **TwiMLResponse**.

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML][]. Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio][] (en inglés).

## <span id="CreateAccount"></span></a>Creación de una cuenta de Twilio

Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [evaluación de Twilio][] (en inglés). Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el identificador de cuenta y el token de autenticación en la [página de la cuenta de Twilio][] (en inglés) en los campos etiquetados como **SID AUTEN** y **TOKEN AUTEN**, respectivamente.

## <span id="VerifyPhoneNumbers"></span></a>Comprobación de números de teléfono

Es necesario comprobar varios números de teléfono con Twilio en la cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes con su número de teléfono existente para el identificador de autor de la llamada, el número de teléfono se debe comprobar con Twilio. De igual forma, hasta que actualice, si desea enviar mensajes SMS a un número de teléfono, Twilio debe realizar la comprobación. Después de la actualización, podrá enviar mensajes SMS a cualquier número sin comprobarlo. Para obtener más información sobre cómo comprobar un número de teléfono, consulte la [administración de números][]. Parte del código que aparece a continuación se basa en números de teléfono que tendrá que comprobar con Twilio.

Como alternativa a utilizar un número existente para sus aplicaciones, puede comprar un número de teléfono de Twilio. Si desea obtener información sobre cómo comprar un número de teléfono de Twilio, consulte la [ayuda sobre números de teléfono de Twilio][] (en inglés).

## <span id="create_app"></span></a>Creación de una aplicación PHP

Una aplicación PHP que usa el servicio Twilio y que se ejecuta en Azure no es distinta a otra aplicación PHP que use el servicio Twilio. Mientras que los servicios Twilio se basan en REST y pueden llamarse desde PHP de varias formas, este artículo se centrará en cómo usar los servicios Twilio con la [biblioteca de Twilio para PHP desde Github][] (en inglés). Para obtener más información sobre el uso de la biblioteca de Twilio para PHP, consulte [][1]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>.

Las instrucciones detalladas para la generación e implementación de una aplicación PHP/Twilio en Azure se encuentran disponibles en [Realización de una llamada de teléfono con Twilio en una aplicación PHP en Azure][].

## <span id="configure_app"></span></a>Configuración de su aplicación para utilizar bibliotecas de Twilio

Puede configurar la aplicación para que use la biblioteca de Twilio para PHP de dos formas:

1.  Descargue la biblioteca de Twilio para PHP desde Github ([][biblioteca de Twilio para PHP desde Github]<https://github.com/twilio/twilio-php></a>) y agregue el directorio **Services** a la aplicación.

    -O-

2.  Instale la biblioteca de Twilio para PHP como paquete PEAR. Puede instalarse con los siguientes comandos:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Una vez que haya instalado la biblioteca de Twilio para PHP, puede agregar a continuación una instrucción **require\_once** en la parte superior de los archivos PHP para hacer referencia a la biblioteca:

        require_once 'Services/Twilio.php';

Para obtener más información, consulte [][2]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.

## <span id="howto_make_call"></span></a>Direccionamiento del llamada saliente

A continuación se indica cómo realizar una llamada saliente usando la clase **Services\_Twilio**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. En lugar de lo anterior, podría utilizar su propio sitio web para proporcionar la respuesta de TwiML; si desea obtener información, consulte [Inserción de respuestas de TwiML desde su propio sitio web][Direccionamiento del TwiML desde su propio sitio web].

-   **Nota**: para solucionar los errores de validación de certificados SSL, consulte [][3]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>

## <span id="howto_send_sms"></span></a>Direccionamiento del mensaje SMS

A continuación se muestra cómo enviar un mensaje SMS con la clase **Services\_Twilio**. El número **From** lo proporciona Twilio para las cuentas de evaluación para enviar mensajes SMS. El número **To** se debe comprobar para la cuenta de Twilio antes de ejecutar el código.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>Direccionamiento del Proporcionar respuestas de TwiML desde su propio sitio web

Cuando la aplicación inicia una llamada a la API de Twilio, Twilio envía su solicitud a una URL que debe devolver una respuesta de TwiML. El ejemplo anterior utiliza la URL [][4]<http://twimlets.com/message></a> proporcionada por Twilio. (Aunque TwiML está diseñado para su uso por Twilio, puede verlo en el explorador. Por ejemplo, haga clic en [][4]<http://twimlets.com/message></a> para ver un elemento `<Response>` vacío; otro ejemplo, haga clic en [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> para ver un elemento `<Response>` que consta de un elemento `<Say>`).

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio que devuelve respuestas HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas XML; en este tema se asume que usará PHP para crear el TwiML.

La siguiente página PHP da como resultado una respuesta de TwiML que dice **Hello World** en la llamada.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Como puede ver en el ejemplo anterior, la respuesta de TwiML es sencillamente un documento XML. La biblioteca de Twilio para PHP contiene clases que generarán TwiML de manera automática. El siguiente ejemplo produce la respuesta equivalente como se muestra arriba, pero usa la clase **Services\_Twilio\_Twiml** en la biblioteca de Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para obtener información sobre TwiML, consulte [][6]<https://www.twilio.com/docs/api/twiml></a>.

Una vez tenga configurada su página de PHP para proporcionar respuestas de TwiML, utilice la URL de la página de PHP como la URL que se pasa al método `Services_Twilio->account->calls->create`. Por ejemplo, si tiene una aplicación web llamada **MyTwiML** implementada en un servicio hospedado de Azure y el nombre de la página de PHP es **mytwiml.php**, la dirección URL se puede transmitir a **Services\_Twilio-\>account-\>calls-\>create**, tal como aparece a continuación:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Si desea obtener más información acerca de cómo usar Twilio en Azure con PHP, consulte [Realización de una llamada de teléfono con Twilio en una aplicación PHP en Azure][].

## <span id="AdditionalServices"></span></a>Direccionamiento del servicios Twilio adicionales

Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en Web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para obtener los detalles completos, consulte [Twilio API documentation][API de Twilio].

## <span id="NextSteps"></span></a>Pasos siguientes

Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

-   [Twilio Security Guidelines][]
-   [Twilio HowTo's and Example Code][]
-   [Twilio Quickstart Tutorials][]
-   [Twilio on GitHub][]
-   [Talk to Twilio Support][]

  [Pasos siguientes]: #NextSteps
  [¿Qué es Twilio?]: #WhatIs
  [Precios de Twilio]: #Pricing
  [Conceptos]: #Concepts
  [Creación de una cuenta de Twilio]: #CreateAccount
  [Comprobación de números de teléfono]: #VerifyPhoneNumbers
  [Creación de una aplicación PHP]: #create_app
  [Configuración de su aplicación para utilizar bibliotecas de Twilio]: #configure_app
  [Direccionamiento del una llamada saliente]: #howto_make_call
  [Direccionamiento del mensaje SMS]: #howto_send_sms
  [Direccionamiento del TwiML desde su propio sitio web]: #howto_provide_twiml_responses
  []: http://ahoy.twilio.com/azure
  [página de precios de Twilio]: http://www.twilio.com/pricing
  [bibliotecas API de Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API de Twilio]: http://www.twilio.com/api
  [evaluación de Twilio]: https://www.twilio.com/try-twilio
  [página de la cuenta de Twilio]: https://www.twilio.com/user/account
  [administración de números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [ayuda sobre números de teléfono de Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [biblioteca de Twilio para PHP desde Github]: https://github.com/twilio/twilio-php
  [1]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Realización de una llamada de teléfono con Twilio en una aplicación PHP en Azure]: ../partner-twilio-php-make-phone-call
  [2]: https://github.com/twilio/twilio-php/blob/master/README.md
  [3]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [6]: https://www.twilio.com/docs/api/twiml
  [Twilio Security Guidelines]: http://www.twilio.com/docs/security
  [Twilio HowTo's and Example Code]: http://www.twilio.com/docs/howto
  [Twilio Quickstart Tutorials]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub]: https://github.com/twilio
  [Talk to Twilio Support]: http://www.twilio.com/help/contact
