<properties pageTitle="Uso de Twilio para capacidades de voz y SMS | Centro de desarrollo móvil" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/03/2014" ms.author="MicrosoftHelp@twilio.com" />


<h1>Uso de Twilio para funciones de voz y SMS desde Servicios móviles</h1>

Este tema muestra cómo realizar algunas tareas comunes a través de la API de Twilio con los Servicios móviles de Azure. En este tutorial, aprenderá a crear scripts de API personalizada que usan la API de Twilio para iniciar una llamada telefónica y enviar un mensaje de Servicio de mensajes cortos (SMS). 

<h2><a id="WhatIs"></a>¿Qué es Twilio?</h2>
Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

<h2><a id="Pricing"></a>Precios de Twilio y ofertas especiales</h2>
Los clientes de Azure reciben una [oferta especial][special_offer]: 10 $ de regalo en crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (10 $ de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en [ahoy.twilio.com/azure][special_offer].

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en la [página de precios de Twilio][twilio_pricing] (en inglés).  

<h2><a id="Concepts"></a>Conceptos</h2>
La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio] [twilio_libraries] (en inglés).  Hay otros tutoriales disponibles para usar Twilio con cualquier aplicación de Azure escrita en [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] o [Ruby][azure_twilio_howto_ruby].

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

<h3><a id="Verbs"></a>Verbos de Twilio</h3>
La API utiliza los verbos de Twilio, por ejemplo, el verbo **&lt;Say&gt;** indica a Twilio que entregue de manera audible un mensaje en una llamada. 

A continuación se presenta una lista de verbos de Twilio.  Obtenga información sobre los otros verbos y funciones mediante la [documentación del lenguaje de marcado de Twilio](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: Conecta la persona que llama con otro teléfono.
* **&lt;Gather&gt;**: Recopila los dígitos numéricos que se introdujeron en el teclado del teléfono.
* **&lt;Hangup&gt;**: Finaliza una llamada.
* **&lt;Play&gt;**: Reproduce un archivo de audio.
* **&lt;Pause&gt;**: Espera en silencio una cantidad de segundos específica.
* **&lt;Record&gt;**: Graba la voz de la persona que llama y devuelve una dirección URL de un archivo que contiene la grabación.
* **&lt;Redirect&gt;**: Transfiere el control de una llamada o SMS al TwiML en una URL distinta.
* **&lt;Reject&gt;**: Rechaza una llamada entrante a su número de Twilio sin cobrarle.
* **&lt;Say&gt;**: Convierte texto en voz para hacer una llamada.
* **&lt;Sms&gt;**: Envía un mensaje SMS.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML es un conjunto de instrucciones basadas en XML y en los verbos de Twilio que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Cuando su aplicación llama a la API de Twilio, uno de los parámetros de API es la URL que devuelve la respuesta de TwiML. Con fines de desarrollo, puede usar las URL que ofrece Twilio para proporcionar las respuestas de TwiML que usaron sus aplicaciones. También puede hospedar sus propias URL para producir las repuestas de TwiML; otra opción es usar el objeto **TwiMLResponse**.

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML][twiml]. Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Creación de una cuenta de Twilio</h2>
Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [evaluación de Twilio][try_twilio]. Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el identificador de cuenta y el token de autenticación en la [página de la cuenta de Twilio][twilio_account], en los campos etiquetados como **ACCOUNT SID** y **AUTH TOKEN**, respectivamente.

<h2><a id="VerifyPhoneNumbers"></a>Comprobación de números de teléfono</h2>
Es necesario comprobar varios números de teléfono con Twilio en la cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes, el número de teléfono se debe comprobar con Twilio como identificador de autor de una llamada de salida. De manera similar, si desea que un número de teléfono reciba mensajes SMS, se debe comprobar con Twilio el número de teléfono de recepción. Para obtener información sobre cómo comprobar un número de teléfono, consulte la [administración de números] [verify_phone] (en inglés). Parte del código que aparece a continuación se basa en números de teléfono que tendrá que comprobar con Twilio.

Como alternativa a utilizar un número existente para sus aplicaciones, puede comprar un número de teléfono de Twilio. Si desea obtener información sobre cómo comprar un número de teléfono de Twilio, consulte la [ayuda sobre números de teléfono de Twilio](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Creación de un servicio móvil</h2>
Un servicio móvil que hospeda una aplicación habilitada para Twilio no es diferente de ningún otro servicio móvil. Solo tiene que agregar la biblioteca node.js de Twilio para poder hacer referencia a esta desde los scripts de la API personalizada del servicio móvil. Para obtener información acerca de cómo crear un servicio móvil inicial, consulte [Introducción a Servicios móviles](/es-es/documentation/articles/mobile-services-ios-get-started/).

<h2><a id="ConfigureMobileService"></a>Configuración del servicio móvil para usar la biblioteca Node.js de Twilio</h2>
Twilio proporciona la biblioteca Node.js, que encapsula varios aspectos de Twilio a fin de proporcionar maneras sencillas y fáciles de interactuar con la API de Twilio REST y el Cliente de Twilio para producir respuestas TwiML.

Para usar la biblioteca node.js de Twilio en el servicio móvil, debe aprovechar la compatibilidad con el módulo npm de Servicios móviles, lo que puede hacer mediante el almacenamiento de los scripts en el control del código fuente. 

1. Complete el tutorial [Scripts de almacén en control de código fuente](/es-es/documentation/articles/mobile-services-store-scripts-source-control/). Este tutorial le guiará por la configuración del control de código fuente en los servicios móviles y el almacenamiento de los scripts de servidor en un repositorio Git.

2. Una vez haya configurado el control de código fuente para el servicio móvil, abra el repositorio en el equipo local, vaya a la subcarpeta `\services`, abra el archivo package.json en un editor de texto y agregue el siguiente campo al objeto **dependencies**:

		"twilio": "~1.7.0"
 
3. Después de agregar la referencia del paquete de Twilio al objeto **dependencies**, el archivo package.json debe tener este aspecto:

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Windows Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[WACOM.NOTE]La dependencia de Twilio debe agregarse como `"twilio": "~1.7.0"`, con un (~). No se admite una referencia con un símbolo de intercalación (^). 

4. Confirme esta actualización de archivo y devuelva la actualización al servicio móvil.

	Esta actualización del archivo package.json reiniciará el servicio móvil.
	
El servicio móvil ahora instala y carga el paquete de Twilio para que pueda hacer referencia a la biblioteca de Twilio y usarla en la API personalizada y los scripts de tabla.

<h2><a id="howto_make_call"></a>Direccionamiento del una llamada saliente</h2>
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

Si desea obtener más información acerca de los parámetros transmitidos al método **client.makeCall**, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. Podría en cambio usar su propio sitio para proporcionar la respuesta de TwiML. Para obtener más información, consulte [Inserción de respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Direccionamiento del mensaje SMS</h2>
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


<h2><a id="howto_provide_twiml_responses"></a>Direccionamiento del Proporcionar respuestas de TwiML desde su propio sitio web</h2>

Cuando la aplicación inicia una llamada a la API de Twilio, por ejemplo, mediante el método client.InitiateOutboundCall, Twilio envía su solicitud a una dirección URL que se espera que devuelva una respuesta de TwiML. El ejemplo en Realización de una llamada saliente usa la dirección URL proporcionada por Twilio, http://twimlets.com/message, para devolver la respuesta.

<div class="dev-callout">
<b>Nota:</b>
<p>Aun cuando TwiML está diseñado para que lo usen los servicios web, puede ver el TwiML en su explorador. 	Por ejemplo, haga clic en <a href="http://twimlets.com/message">twimlet_message_url</a> para ver un elemento &lt;Response&gt; vacío; otro ejemplo, haga clic en <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> para ver un elemento &lt;Response&gt; que contiene un elemento &lt;Say&gt;.</p>
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

Para obtener información sobre TwiML, consulte [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

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


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /es-es/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /es-es/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /es-es/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /es-es/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /es-es/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /es-es/develop/php/how-to-guides/twilio-voice-and-sms-service/
