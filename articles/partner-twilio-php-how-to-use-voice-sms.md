<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="waltpo" manager="bjsmith" editor="mollybos" />

Uso de Twilio para capacidades de voz y SMS en PHP
==================================================

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [¿Qué es Twilio?](#WhatIs)
-   [Precios de Twilio](#Pricing)
-   [Conceptos](#Concepts)
-   [Creación de una cuenta de Twilio](#CreateAccount)
-   [Comprobación de números de teléfono](#VerifyPhoneNumbers)
-   [Creación de una aplicación PHP](#create_app)
-   [Configuración de su aplicación para utilizar bibliotecas de Twilio](#configure_app)
-   [Realización de una llamada saliente](#howto_make_call)
-   [Envío de un mensaje SMS](#howto_send_sms)
-   [Inserción de respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses)

¿Qué es Twilio?
---------------

Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que su aplicación envíe y reciba mensajes de texto. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

Precios de Twilio y ofertas especiales
--------------------------------------

Los clientes de Azure reciben una [oferta especial][http://www.twilio.com/azure]: 10 $ de regalo en crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (10 $ de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en: <http://ahoy.twilio.com/azure>.

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en la [página de precios de Twilio](http://www.twilio.com/pricing) (en inglés).

Conceptos
---------

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio](https://www.twilio.com/docs/libraries) (en inglés).

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### Verbos de Twilio

La API usa los verbos de Twilio; por ejemplo, el verbo **&lt;Say\>** indica a Twilio que entregue de manera audible un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio. Obtenga información sobre los otros verbos y funciones mediante la [documentación del lenguaje de marcado de Twilio][http://www.twilio.com/docs/api/twiml] (en inglés).

-   **&lt;Dial\>**: Conecta la persona que llama con otro teléfono.
-   **&lt;Gather\>**: Recopila los dígitos numéricos que se introdujeron en el teclado del teléfono.
-   **&lt;Hangup\>**: Finaliza una llamada.
-   **&lt;Play\>**: Reproduce un archivo de audio.
-   **&lt;Pause\>**: Espera en silencio una cantidad de segundos específica.
-   **&lt;Record\>**: Graba la voz de la persona que llama y devuelve una dirección URL de un archivo que contiene la grabación.
-   **&lt;Redirect\>**: Transfiere el control de una llamada o SMS al TwiML en una URL distinta.
-   **&lt;Reject\>**: Rechaza una llamada entrante a su número de Twilio sin cobrarle.
-   **&lt;Say\>**: Convierte texto en voz para hacer una llamada.
-   **&lt;Sms\>**: Envía un mensaje SMS.

### TwiML

TwiML es un conjunto de instrucciones basadas en XML y en los verbos de Twilio que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Cuando su aplicación llama a la API de Twilio, uno de los parámetros de API es la URL que devuelve la respuesta de TwiML. Con fines de desarrollo, puede usar las URL que ofrece Twilio para proporcionar las respuestas de TwiML que usaron sus aplicaciones. También puede hospedar sus propias URL para producir las repuestas de TwiML; otra opción es usar el objeto **TwiMLResponse**.

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML](http://www.twilio.com/docs/api/twiml). Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio](http://www.twilio.com/api) (en inglés).

Creación de una cuenta de Twilio
--------------------------------

Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [evaluación de Twilio](https://www.twilio.com/try-twilio) (en inglés). Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el identificador de cuenta y el token de autenticación en la [página de la cuenta de Twilio](https://www.twilio.com/user/account) (en inglés) en los campos etiquetados como **SID AUTEN** y **TOKEN AUTEN**, respectivamente.

Comprobación de números de teléfono
-----------------------------------

Es necesario comprobar varios números de teléfono con Twilio en la cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes con su número de teléfono existente para el identificador de autor de la llamada, el número de teléfono se debe comprobar con Twilio. De manera similar, hasta que se realice la actualización, si desea enviar mensajes SMS a un número de teléfono, este se debe comprobar con Twilio. Después de actualizar, puede enviar mensajes SMS a cualquier número sin comprobarlo. Para obtener información sobre cómo comprobar un número de teléfono, consulte la [administración de números](https://www.twilio.com/user/account/phone-numbers/verified#) (en inglés). Parte del código que aparece a continuación se basa en números de teléfono que tendrá que comprobar con Twilio.

Como alternativa a utilizar un número existente para sus aplicaciones, puede comprar un número de teléfono de Twilio. Si desea obtener información sobre cómo comprar un número de teléfono de Twilio, consulte la [ayuda sobre números de teléfono de Twilio](https://www.twilio.com/help/faq/phone-numbers) (en inglés).

Creación de una aplicación PHP
------------------------------

Una aplicación PHP que usa el servicio Twilio y se ejecuta en Azure no es distinta de cualquier otra aplicación PHP que usa dicho servicio. Si bien los servicios Twilio se basan en REST y pueden llamarse desde PHP de diversas formas, este artículo se centrará en cómo usar los servicios Twilio con la [biblioteca de Twilio para PHP de Github](https://github.com/twilio/twilio-php) (en inglés). Para obtener más información acerca de cómo usar la biblioteca de Twilio para PHP, consulte <http://readthedocs.org/docs/twilio-php/en/latest/index.html> (en inglés).

Puede obtener instrucciones detalladas acerca de cómo crear e implementar una aplicación PHP con Twilio en Azure en [Realización de una llamada de teléfono con Twilio en una aplicación PHP en Azure](../partner-twilio-php-make-phone-call).

Configuración de su aplicación para utilizar bibliotecas de Twilio
------------------------------------------------------------------

Puede configurar la aplicación para usar la biblioteca de Twilio para PHP de dos formas:

1.  Descargue la biblioteca de Twilio para PHP desde Github (<https://github.com/twilio/twilio-php>) y agregue el directorio **Services** a la aplicación.

    O

2.  Instale la biblioteca de Twilio para PHP como paquete PEAR. Puede instalarse con los comandos siguientes:

         $ pear channel-discover twilio.github.com/pear
         $ pear install twilio/Services_Twilio

Una vez instalada la biblioteca de Twilio para PHP, puede agregarse la instrucción **require\_once** en la parte superior de los archivos PHP para hacer referencia a la biblioteca:

     require_once 'Services/Twilio.php';

Para obtener más información, consulte <https://github.com/twilio/twilio-php/blob/master/README.md>.

Realización de una llamada saliente
-----------------------------------

A continuación, se indica cómo realizar una llamada saliente con la clase **Services\_Twilio**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

    // Incluir la biblioteca de Twilio para PHP.
    require_once 'Services/Twilio.php';

    // Versión de la biblioteca.
    $version = "2010-04-01";

    // Establecer el ID de cuenta y el token de autenticación.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // El número de teléfono que inicia la llamada.
    // (Debe validarse previamente con Twilio).
    $from_number = "NNNNNNNNNNN";

    // El número de teléfono que recibe la llamada.
    $to_number = "NNNNNNNNNNN";

    // Usar el sitio que proporciona Twilio para la respuesta de TwiML.
    $url = "http://twimlets.com/message";

    // El texto del mensaje de teléfono.
    $message = "Hello world.";

    // Crear el cliente de la llamada.
    $client = new Services_Twilio($sid, $token, $version);

    //Realizar la llamada.
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

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. En lugar de lo anterior, podría utilizar su propio sitio web para proporcionar la respuesta de TwiML; si desea obtener información, consulte [Inserción de respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses).

-   **Nota**: para solucionar errores de validación de certificados SSL, consulte <http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html>.

Envío de un mensaje SMS
-----------------------

A continuación se muestra cómo enviar un mensaje SMS con la clase **Services\_Twilio**. El número **From** lo proporciona Twilio para las cuentas de evaluación para enviar mensajes SMS. El número **To** se debe comprobar para la cuenta de Twilio antes de ejecutar el código.

    // Incluir la biblioteca de Twilio para PHP.
    require_once 'Services/Twilio.php';

    // Versión de la biblioteca.
    $version = "2010-04-01";

    // Establecer el ID de cuenta y el token de autenticación.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // Con la cuenta de evaluación, solo puede enviarse texto desde el número de Twilio.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Crear el cliente de la llamada.
    $client = new Services_Twilio($sid, $token, $version);

    // Enviar el mensaje SMS.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Inserción de respuestas de TwiML desde su propio sitio web
----------------------------------------------------------

Cuando la aplicación inicia una llamada a la API de Twilio, Twilio envía su solicitud a una dirección URL que se espera que devuelva una respuesta de TwiML. El ejemplo anterior utiliza la URL <http://twimlets.com/message> proporcionada por Twilio. (Aunque TwiML está diseñado para su uso por Twilio, puede verlo en el explorador. Por ejemplo, haga clic en <http://twimlets.com/message> para ver un elemento `<Response>` vacío; otro ejemplo, haga clic en <http://twimlets.com/message?Message%5B0%5D=Hello%20World> para ver un elemento `<Response>` que consta de un elemento `<Say>`).

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio que devuelve respuestas HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas XML; en este tema se asume que usará PHP para crear el TwiML.

La siguiente página PHP da como resultado una respuesta de TwiML que dice **Hello World** en la llamada.

	<?php    
		header("content-type: text/xml");    
		echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
	?>
	<Response data-morhtml="true">    
		<Say data-morhtml="true">Hello world.</Say>
	</Response>

Como puede ver en el ejemplo anterior, la respuesta de TwiML es sencillamente un documento XML. La biblioteca de Twilio para PHP contiene clases que generarán TwiML de manera automática. El siguiente ejemplo produce la respuesta equivalente como se muestra arriba, pero usa la clase **Services\_Twilio\_Twiml** en la biblioteca de Twilio para PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Para obtener información sobre TwiML, consulte <https://www.twilio.com/docs/api/twiml>.

Una vez que tenga configurada la página PHP para proporcionar respuestas de TwiML, utilice la URL de la página PHP dado que la URL se transmitió al método `Services_Twilio->account->calls->create`. Por ejemplo, si tiene una aplicación web denominada **MyTwiML** implementada en un servicio hospedado en Azure y el nombre de la página PHP es **mytwiml.php**, la dirección URL se puede transmitir a **Services\_Twilio-\>account-\>calls-\>create** tal y como se muestra en el ejemplo siguiente:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // El texto del mensaje de teléfono.
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

Si desea obtener más información acerca de cómo usar Twilio en Azure con PHP, consulte [Realización de una llamada de teléfono con Twilio en una aplicación PHP en Azure](../partner-twilio-php-make-phone-call).

Uso de servicios Twilio adicionales
-----------------------------------

Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en Web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para obtener los detalles completos, consulte [Twilio API documentation](http://www.twilio.com/api).

Pasos siguientes
----------------

Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

-   [Twilio Security Guidelines](http://www.twilio.com/docs/security)
-   [Twilio HowTo's and Example Code](http://www.twilio.com/docs/howto)
-   [Twilio Quickstart Tutorials](http://www.twilio.com/docs/quickstart)
-   [Twilio on GitHub](https://github.com/twilio)
-   [Talk to Twilio Support](http://www.twilio.com/help/contact)

