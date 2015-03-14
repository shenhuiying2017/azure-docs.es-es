<properties 
	pageTitle="Cómo usar Twilio para voz y SMS (Ruby) - Azure" 
	description="Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos de código escritos en Ruby." 
	services="" 
	documentationCenter="ruby" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="MicrosoftHelp@twilio.com"/>





# Uso de Twilio para capacidades de voz y SMS en Ruby
Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información acerca de Twilio y cómo usar el servicio de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps) .

## Tabla de contenido
* [¿Qué es Twilio?](#WhatIs)
* [Precios de Twilio](#Pricing)
* [Conceptos](#Concepts)
* [Creación de una cuenta de Twilio](#CreateAccount)
* [Creación de una aplicación de Ruby Sinatra](#create_app)
* [Configuración de su aplicación para utilizar bibliotecas de Twilio](#configure_app)
* [Cómo realizar una llamada saliente](#howto_make_call)
* [Cómo recibir de un mensaje SMS](#howto_recieve_sms)
* [Cómo conseguir servicios Twilio adicionales](#additional_services)
* [Pasos siguientes](#NextSteps)

## <a id="WhatIs"></a>¿Qué es Twilio?
Twilio es una API de servicio web de telefonía que le permite utilizar las habilidades y lenguajes web existentes para crear aplicaciones de voz y SMS. Twilio es un servicio de terceros (no una característica de Azure ni tampoco un producto de Microsoft).

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite que sus aplicaciones habiliten la comunicación por voz a través de conexiones existentes a Internet, incluidas las conexiones móviles.

## <a id="Pricing"></a>Precios de Twilio y ofertas especiales
Puede conseguir más información acerca de los precios de Twilio en [Precios de Twilio] [twilio_pricing]. Los clientes de Azure conseguirán una [oferta especial][special_offer]: un crédito gratis de 1000 textos o 1000 minutos entrantes. Si desea suscribirse para conseguir esta oferta o para tener más información, visite [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Conceptos
La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Dispone de las bibliotecas de cliente en varios idiomas; si desea consultar la lista de idiomas, visite las [bibliotecas API de Twilio] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML es un conjunto de instrucciones basadas en XML que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos los documentos de TwinML disponen de `<Response>` como elemento raíz. A partir de ahí, puede usar los verbos de Twilio para definir el comportamiento de la aplicación.

### <a id="Verbs"></a>Verbos de TwiML
Los verbos de Twilio son etiquetas XML que indican a Twilio qué **hacer**. Por ejemplo, el verbo **&lt;Say&gt;** indica a Twilio que entregue de manera audible un mensaje en una llamada. 

A continuación se presenta una lista de verbos de Twilio.

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

Para obtener más información acerca de los verbos de Twilio, sus atributos y TwiML, vea [TwiML] [twiml]. Para obtener información adicional acerca de la API de Twilio, vea la [API de Twilio] [twilio_api] (en inglés).

## <a id="CreateAccount"></a>Creación de una cuenta de Twilio
Cuando desee tener una cuenta de Twilio, puede registrarse en [Probar Twilio] [try_twilio]. Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando registre la cuenta Twilio, obtendrá un número de teléfono gratuito para la aplicación. Recibirá también un SID de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el SID de cuenta y el token de autenticación en la [página de la cuenta de Twilio][twilio_account] (en inglés), en aquellos campos etiquetados como **ACCOUNT SID** y **TOKEN AUTEN**, respectivamente.

### <a id="VerifyPhoneNumbers"></a>Comprobación de números de teléfono
Además del número proporcionado por Twilio, también puede comprobar los números que controle (es decir, el número de teléfono de casa o del móvil) para usarlos en las aplicaciones. 

Para obtener más información sobre cómo comprobar un número de teléfono, vea [Administrar números] [verify_phone].

## <a id="create_app"></a>Creación de una aplicación de Ruby
Una aplicación de Ruby que usa el servicio Twilio y que se ejecuta en Azure no es distinta a otra aplicación Ruby que use el servicio Twilio. A pesar de que los servicios Twilio están completamente basados en la técnica REST y pueden llamarse desde Ruby de determinadas formas, este artículo se centrará en cómo usar los servicios Twilio con la [biblioteca auxiliar de Twilio para Ruby][twilio_ruby] (en inglés).

Primero, [deberá configurar una nueva máquina virtual (VM) de Azure que ejecute Linux][azure_vm_setup] para que haga de host para su nueva aplicación web Ruby. Ignore los pasos relacionados con la creación de una aplicación Rails, solo configure la VM. Asegúrese de crear un extremo con un puerto externo de 80 y un puerto interno de 5000.

En los siguientes ejemplos usaremos [Sinatra][sinatra], un marco web simple para Ruby. Sin embargo, puede usar verdaderamente la biblioteca auxiliar de Twilio para Ruby con otro marco web, incluido Ruby en Rails.

SSH en la nueva VM y cree un directorio para la nueva aplicación. Dentro del directorio, cree un archivo llamado Gemfile y copie el siguiente código en él:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

En la línea de comandos ejecute `bundle install`. De esta forma se instalarán las dependencias anteriores. A continuación, cree un archivo que se llame `web.rb`. Será donde se encuentre el código para la aplicación web. Pegue el siguiente código en él:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Ahora debería poder ejecutar el comando `ruby web.rb -p 5000`. De esta forma, podrá ejecutar un servidor web pequeño en el puerto 5000. Debe poder buscar en esta aplicación en el explorador visitando la dirección URL que configure para la VM de Azure. Una vez que pueda obtener acceso a la aplicación web en el explorador, podrá comenzar a generar una aplicación Twilio.

## <a id="configure_app"></a>Configuración de su aplicación para usar Twilio
Puede configurar la aplicación web para que use la biblioteca de Twilio actualizando su  `Gemfile` para que incluya esta línea:

    gem 'twilio-ruby'

En la línea de comandos, ejecute `bundle install`. Ahora abra `web.rb` e incluya esta línea en la parte superior:

    require 'twilio-ruby'

Ahora tiene todo configurado para usar la biblioteca auxiliar de Twilio para Ruby en la aplicación web.

## <a id="howto_make_call"></a>Cómo realizar una llamada saliente
A continuación se muestra cómo realizar una llamada saliente. Entre los conceptos clave se incluyen el uso de la biblioteca auxiliar de Twilio para Ruby para realizar llamadas de la API REST y la representación de TwiML. Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

Agregue esta función a `web.md`:

    # Set your account ID and authentication token.
	sid = "your_twilio_account_sid";
	token = "your_twilio_authentication_token";

	# The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
	from = "NNNNNNNNNNN";

	# The number of the phone receiving call.
	to = "NNNNNNNNNNN";

	# Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
	  # Create the call client.
	  client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Si abre `http://yourdomain.cloudapp.net/make_call` en un explorador, activará la llamada en la API de Twilio para realizar la llamada telefónica. Los dos primeros parámetros en `client.account.calls.create` no necesitan mucha explicación: el número del que procede la llamada es `from` y el número al que se llama es `to`. 

El tercer parámetro (`url`) es la dirección URL que Twilio solicita para obtener instrucciones sobre qué hacer cuando la llamada se conecta. En este caso, configuramos una dirección URL (`http://yourdomain.cloudapp.net`) que devuelve un documento TwiML simple y usa el verbo `<Say>` para pasar el texto a voz y decir "Hola caracola" a la persona que recibe la llamada.

## <a id="howto_recieve_sms"></a>Cómo recibir un mensaje SMS
En el ejemplo anterior, iniciamos una llamada telefónica **saliente**. Esta vez, usaremos un número de teléfono que proporcionó Twilio durante el registro para procesar un mensaje SMS **entrante**.

Primero, inicie sesión en su [panel de Twilio][twilio_account]. Haga clic en "Numbers" en el panel de navegación superior y haga clic en el número de Twilio proporcionado. Ahora verá las dos direcciones URL que puede configurar. Una dirección URL de solicitud de voz y una dirección de URL de solicitud de SMS. Estas son las direcciones URL a las que llama Twilio cuando se realiza una llamada telefónica o se envía un SMS a su número. Las direcciones URL también se conocen como "enlaces web".

Nos gustaría procesar los mensajes SMS entrantes, así que vamos a actualizar la URL a  `http://yourdomain.cloudapp.net/sms_url`. Continúe y haga clic en la opción para guardar los cambios en la parte inferior de la página. A continuación vuelva a `web.rb` y programaremos nuestra aplicación para controlar esto:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Después de realizar el cambio, asegúrese de reiniciar la aplicación web. Ahora, use el teléfono para enviar un SMS al número de Twilio. Recibirá pronto una respuesta al SMS que le dará las gracias por hacer ping, y le indicará que Twilio y Azure son perfectos.

## <a id="additional_services"></a>Cómo usar los servicios Twilio adicionales
Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en Web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para más información, consulte la [Documentación de API de Twilio] [twilio_api_documentation].

### <a id="NextSteps"></a>Pasos siguientes
Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio] [twilio_security_guidelines]
* [Procedimientos y código de ejemplo de Twilio] [twilio_howtos]
* [Tutoriales de inicio rápido de Twilio][twilio_quickstarts] 
* [Twilio on GitHub] [twilio_on_github]
* [Talk to Twilio Support][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://azure.microsoft.com/develop/ruby/tutorials/web-app-with-linux-vm/

<!--HONumber=45-->
