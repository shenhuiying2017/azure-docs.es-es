<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Uso de Twilio para funciones de voz y SMS desde Servicios móviles

Este tema muestra cómo realizar algunas tareas comunes a través de la API de Twilio con los Servicios móviles de Azure. En este tutorial, aprenderá a crear scripts de API personalizada que usan la API de Twilio para iniciar una llamada telefónica y enviar un mensaje de Servicio de mensajes cortos (SMS).

## <span id="WhatIs"></span></a>¿Qué es Twilio?

Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

## <span id="Pricing"></span></a>Precios de Twilio y ofertas especiales

Los clientes de Azure reciben una [oferta especial][oferta especial]: 10 $ de regalo en crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (10 $ de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en [ahoy.twilio.com/azure][oferta especial].

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en la [página de precios de Twilio][página de precios de Twilio] (en inglés).

## <span id="Concepts"></span></a>Conceptos

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio][bibliotecas API de Twilio] (en inglés). Hay otros tutoriales disponibles para usar Twilio con cualquier aplicación de Azure escrita en [.NET][.NET], [node.js][node.js], [Java][Java], [PHP][PHP], [Python][Python] o [Ruby][Ruby].

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos de Twilio

La API utiliza los verbos de Twilio, por ejemplo, el verbo **\<Say\>** indica a Twilio que entregue de manera audible un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio. Obtenga información sobre los otros verbos y funciones mediante la [documentación del lenguaje de marcado de Twilio][documentación del lenguaje de marcado de Twilio] (en inglés).

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

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML][documentación del lenguaje de marcado de Twilio]. Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio][API de Twilio] (en inglés).

## <span id="CreateAccount"></span></a>Creación de una cuenta de Twilio

Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [evaluación de Twilio][evaluación de Twilio] (en inglés). Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el identificador de cuenta y el token de autenticación en la [página de la cuenta de Twilio][página de la cuenta de Twilio] (en inglés) en los campos etiquetados como **SID AUTEN** y **TOKEN AUTEN**, respectivamente.

## <span id="VerifyPhoneNumbers"></span></a>Comprobación de números de teléfono

Es necesario comprobar varios números de teléfono con Twilio en la cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes, el número de teléfono se debe comprobar con Twilio como identificador de autor de una llamada de salida. De manera similar, si desea que un número de teléfono reciba mensajes SMS, se debe comprobar con Twilio el número de teléfono de recepción. Para obtener información sobre cómo comprobar un número de teléfono, consulte la [administración de números][administración de números] (en inglés). Parte del código que aparece a continuación se basa en números de teléfono que tendrá que comprobar con Twilio.

Como alternativa a utilizar un número existente para sus aplicaciones, puede comprar un número de teléfono de Twilio. Si desea obtener información sobre cómo comprar un número de teléfono de Twilio, consulte la [ayuda sobre números de teléfono de Twilio][ayuda sobre números de teléfono de Twilio] (en inglés).

## <span id="create_app"></span></a>Creación de un servicio móvil

Un servicio móvil que hospeda una aplicación habilitada para Twilio no es diferente de ningún otro servicio móvil. Solo tiene que agregar la biblioteca node.js de Twilio para poder hacer referencia a esta desde los scripts de la API personalizada del servicio móvil. Para obtener información acerca de cómo crear un servicio móvil inicial, consulte [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

## <span id="VerifyPhoneNumbers"></span></a>Configuración del servicio móvil para usar la biblioteca Node.js de Twilio

Twilio proporciona la biblioteca Node.js, que encapsula varios aspectos de Twilio a fin de proporcionar maneras sencillas y fáciles de interactuar con la API de Twilio REST y el Cliente de Twilio para producir respuestas TwiML.

Para usar la biblioteca node.js de Twilio en el servicio móvil, debe aprovechar la compatibilidad con el módulo npm de Servicios móviles, lo que puede hacer mediante el almacenamiento de los scripts en el control del código fuente. El tutorial [Almacenamiento de scripts en control de código fuente][Almacenamiento de scripts en control de código fuente] muestra cómo configurar el control del código fuente en Servicios móviles por primera vez y cómo almacenar los scripts del servidor en un repositorio Git.

Una vez configurado el control de código fuente para el servicio móvil, abra la pestaña Configure del panel Servicio móvil, busque la dirección URL de Git y cópiela.

Pegue dicha dirección en un explorador y reemplace el nombre del repositorio por */DebugConsole/index.html*.

Por ejemplo, cambie:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

por:

    https://twilioSample.scm.azure-mobile.net/DebugConsole

Cuando se le solicite, escriba las credenciales que usó al configurar el control del código fuente para el servicio. Una vez que haya iniciado sesión, se abrirá la consola de Servicios móviles de Azure.

![Consola de Servicios móviles][Consola de Servicios móviles]

En la consola, cambie el directorio a la carpeta de scripts:

    cd site\wwwroot\App_Data\config\scripts

Una vez en la carpeta de la API, puede instalar el módulo de Node de Twilio mediante la ejecución del comando siguiente:

    npm install twilio

Ahora puede hacer referencia a la biblioteca de Twilio y usarla en la API personalizada y los scripts de tabla.

## <span id="howto_make_call"></span></a>Direccionamiento del llamada saliente

El script siguiente muestra cómo iniciar una llamada saliente desde el servicio móvil mediante la función **makeCall**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

Para obtener más información sobre los parámetros que se pasan a la función **client.makeCall**, consulte [][]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. Podría en cambio usar su propio sitio para proporcionar la respuesta de TwiML. Para obtener más información, consulte [Inserción de respuestas de TwiML desde su propio sitio web][Inserción de respuestas de TwiML desde su propio sitio web].

## <span id="howto_send_sms"></span></a>Direccionamiento del mensaje SMS

El código siguiente muestra cómo enviar un mensaje SMS mediante la función **sendSms**. El número **From** lo proporciona Twilio para las cuentas de evaluación para enviar mensajes SMS. El número **To** se debe comprobar para su cuenta de Twilio antes de ejecutar el código.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {

            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };

## <span id="howto_provide_twiml_responses"></span></a>Direccionamiento del Proporcionar respuestas de TwiML desde su propio sitio web

Cuando la aplicación inicia una llamada a la API de Twilio, por ejemplo, mediante el método client.InitiateOutboundCall, Twilio envía su solicitud a una dirección URL que se espera que devuelva una respuesta de TwiML. El ejemplo en Realización Al realizar una llamada saliente se usa la dirección URL proporcionada por Twilio, <http://twimlets.com/message>, para devolver la respuesta.

<div class="dev-callout">
<b>Nota:</b>
<p>Aun cuando TwiML est&aacute; dise&ntilde;ado para que lo usen los servicios web, puede ver el TwiML en su explorador. Por ejemplo, haga clic en <a href="http://twimlets.com/message">twimlet_message_url</a> para ver un elemento &lt;Response&gt; vac&iacute;o; otro ejemplo, haga clic en <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> para ver un elemento &lt;Response&gt; que consta de un elemento &lt;Say&gt;.</p>
</div>

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio URL que devuelve procesos HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas HTTP. En este tema se asume que va a hospedar la URL desde un controlador de ASP.NET genérico.

El siguiente script da como resultado una respuesta de TwiML que dice Hello World en la llamada.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Para obtener información sobre TwiML, consulte [][1]<https://www.twilio.com/docs/api/twiml></a>.

Después de que haya configurado una manera de proporcionar respuestas de TwiML, puede pasar esa dirección URL al método **client.makeCall**, como se muestra en el ejemplo de código siguiente:

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [oferta especial]: http://ahoy.twilio.com/azure
  [página de precios de Twilio]: http://www.twilio.com/pricing
  [bibliotecas API de Twilio]: https://www.twilio.com/docs/libraries
  [.NET]: /es-es/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /es-es/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /es-es/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /es-es/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /es-es/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /es-es/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [documentación del lenguaje de marcado de Twilio]: http://www.twilio.com/docs/api/twiml
  [API de Twilio]: http://www.twilio.com/api
  [evaluación de Twilio]: https://www.twilio.com/try-twilio
  [página de la cuenta de Twilio]: https://www.twilio.com/user/account
  [administración de números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [ayuda sobre números de teléfono de Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Introducción a los Servicios móviles]: http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started/
  [Almacenamiento de scripts en control de código fuente]: http://www.windowsazure.com/es-es/develop/mobile/tutorials/store-scripts-in-source-control/
  [Consola de Servicios móviles]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [Inserción de respuestas de TwiML desde su propio sitio web]: #howto_provide_twiml_responses
  [1]: https://www.twilio.com/docs/api/twiml
