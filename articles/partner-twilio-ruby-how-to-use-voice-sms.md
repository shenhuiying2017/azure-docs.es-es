<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Uso de Twilio para capacidades de voz y SMS en Ruby

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes][Pasos siguientes].

## Tabla de contenido

-   [¿Qué es Twilio?][¿Qué es Twilio?]
-   [Precios de Twilio][Precios de Twilio]
-   [Conceptos][Conceptos]
-   [Creación de una cuenta de Twilio][Creación de una cuenta de Twilio]
-   [Creación de una aplicación de Ruby Sinatra][Creación de una aplicación de Ruby Sinatra]
-   [Configuración de su aplicación para utilizar bibliotecas de Twilio][Configuración de su aplicación para utilizar bibliotecas de Twilio]
-   [Direccionamiento del una llamada saliente][Direccionamiento del una llamada saliente]
-   [Direccionamiento del de un mensaje SMS][Direccionamiento del de un mensaje SMS]
-   [Direccionamiento del adicionales][Direccionamiento del adicionales]
-   [Pasos siguientes][Pasos siguientes]

## <span id="WhatIs"></span></a>¿Qué es Twilio?

Twilio es una API de servicio web de telefonía que le permite utilizar las habilidades y lenguajes web existentes para crear aplicaciones de voz y SMS. Twilio es un servicio de terceros (no una característica de Azure ni tampoco un producto de Microsoft).

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite que sus aplicaciones habiliten la comunicación por voz a través de conexiones existentes a Internet, incluidas las conexiones móviles.

## <span id="Pricing"></span></a>Precios de Twilio y ofertas especiales

En [Twilio Pricing][Twilio Pricing] (en inglés) puede encontrar información sobre el precio de Twilio. Los clientes de Azure reciben una [oferta especial][oferta especial]: un crédito gratis de 1000 textos o 1000 minutos entrantes. Para registrarse para obtener esta oferta o si desea más información, visite [][oferta especial]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Conceptos

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio][bibliotecas API de Twilio] (en inglés).

### <span id="TwiML"></span></a>TwiML

TwiML es un conjunto de instrucciones basadas en XML que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos los documentos de TwinML disponen de `<Response>` como elemento raíz. A partir de ahí, puede usar los verbos de Twilio para definir el comportamiento de la aplicación.

### <span id="Verbs"></span></a>Verbos de TwiML

Los verbos de Twilio son etiquetas XML que indican a Twilio qué **hacer**. Por ejemplo, el verbo **\<Say\>** indica a Twilio que se envíe de forma audible un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio.

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

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML][TwiML]. Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio][API de Twilio] (en inglés).

## <span id="CreateAccount"></span></a>Creación de una cuenta de Twilio

Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [evaluación de Twilio][evaluación de Twilio] (en inglés). Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando registre la cuenta Twilio, obtendrá un número de teléfono gratuito para la aplicación. Recibirá también un SID de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el SID de cuenta y el token de autenticación en la [página de la cuenta de Twilio][página de la cuenta de Twilio] (en inglés) en los campos etiquetados como **SID AUTEN** y **TOKEN AUTEN**, respectivamente.

### <span id="VerifyPhoneNumbers"></span></a>Comprobación de números de teléfono

Además del número proporcionado por Twilio, también puede comprobar los números que controle (es decir, el número de teléfono de casa o del móvil) para usarlos en las aplicaciones.

Para obtener más información sobre cómo comprobar un número de teléfono, consulte la [administración de números][administración de números].

## <span id="create_app"></span></a>Creación de una aplicación de Ruby

Una aplicación de Ruby que usa el servicio Twilio y que se ejecuta en Azure no es distinta a otra aplicación Ruby que use el servicio Twilio. Mientras que los servicios Twilio son RESTful y pueden llamarse a partir de Ruby de determinadas formas, este artículo se centrará en cómo usar los servicios Twilio con la [biblioteca auxiliar de Twilio para Ruby][biblioteca auxiliar de Twilio para Ruby] (en inglés).

Primero, [configure una nueva VM de Linux de Azure][configure una nueva VM de Linux de Azure] para que actúe como host para la nueva aplicación web de Ruby. Ignore los pasos relacionados con la creación de una aplicación Rails, solo configure la VM. Asegúrese de crear un extremo con un puerto externo de 80 y un puerto interno de 5000.

En los ejemplos siguientes, usaremos [Sinatra][Sinatra], un marco web simple para Ruby. Sin embargo, puede usar verdaderamente la biblioteca auxiliar de Twilio para Ruby con otro marco web, incluido Ruby en Rails.

SSH en la nueva VM y cree un directorio para la nueva aplicación. Dentro del directorio, cree un archivo llamado Gemfile y copie el siguiente código en él:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

En la línea de comandos, ejecute `bundle install`. De esta forma, se instalarán las dependencias anteriores. A continuación, cree un archivo de nombre `web.rb`. Será donde se encuentre el código para la aplicación web. Pegue el siguiente código en él:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

En este momento, debe poder ejecutar el comando `ruby web.rb -p 5000`. De esta forma, podrá ejecutar un servidor web reducido en el puerto 5000. Debe poder buscar en esta aplicación en el explorador visitando la dirección URL que configure para la VM de Azure. Una vez que pueda obtener acceso a la aplicación web en el explorador, podrá comenzar a generar una aplicación Twilio.

## <span id="configure_app"></span></a>Configuración de su aplicación para usar Twilio

Puede configurar la aplicación web para usar la biblioteca de Twilio mediante la actualización de `Gemfile` para incluir esta línea:

    gem 'twilio-ruby'

En la línea de comandos, ejecute `bundle install`. Ahora abra `web.rb` e incluya esta línea en la parte superior:

    require 'twilio-ruby'

Ahora tiene todo configurado para usar la biblioteca auxiliar de Twilio para Ruby en la aplicación web.

## <span id="howto_make_call"></span></a>Direccionamiento del llamada saliente

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

Si abre `http://yourdomain.cloudapp.net/make_call` en un explorador, activará la llamada en la API de Twilio para realizar la llamada telefónica. Los primeros dos parámetros en `client.account.calls.create` son los siguientes: el número del que procede la llamada, `from`, y el número al que se llama, `to`.

El tercer parámetro (`url`) es la dirección URL que Twilio solicita para obtener instrucciones sobre qué hacer cuando la llamada se conecta. En este caso, configuramos una dirección URL (`http://yourdomain.cloudapp.net`) que devuelve un documento TwiML simple y usa el verbo `<Say>` para pasar texto a voz y decir "Hello Monkey" a la persona que recibe la llamada.

## <span id="howto_recieve_sms"></span></a>Direccionamiento del mensaje SMS

En el ejemplo anterior, iniciamos una llamada telefónica **saliente**. Esta vez, usaremos un número de teléfono que proporcionó Twilio durante el registro para procesar un mensaje SMS **entrante**.

Primero, inicie sesión en el [panel de Twilio][página de la cuenta de Twilio]. Haga clic en "Numbers" en el panel de navegación superior y haga clic en el número de Twilio proporcionado. Ahora verá las dos direcciones URL que puede configurar. Una dirección URL de solicitud de voz y una dirección de URL de solicitud de SMS. Estas son las direcciones URL a las que llama Twilio cuando se realiza una llamada telefónica o se envía un SMS a su número. Las direcciones URL también se conocen como "enlaces web".

Nos gustaría procesar los mensajes SMS entrantes, por lo que vamos a actualizar la dirección URL a `http://yourdomain.cloudapp.net/sms_url`. Continúe y haga clic en la opción para guardar los cambios en la parte inferior de la página. Ahora vuelva a `web.rb`. Se programará la aplicación para llevar a cabo gestionar esto:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Después de realizar el cambio, asegúrese de reiniciar la aplicación web. Ahora, use el teléfono para enviar un SMS al número de Twilio. Recibirá pronto una respuesta al SMS que le dará las gracias por hacer ping, y le indicará que Twilio y Azure son perfectos.

## <span id="additional_services"></span></a>Direccionamiento del servicios Twilio adicionales

Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en Web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para obtener los detalles completos, consulte [Twilio API documentation][API de Twilio].

### <span id="NextSteps"></span></a>Pasos siguientes

Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

-   [Twilio Security Guidelines][Twilio Security Guidelines]
-   [Twilio HowTos and Example Code][Twilio HowTos and Example Code]
-   [Twilio Quickstart Tutorials][Twilio Quickstart Tutorials]
-   [Twilio on GitHub][Twilio on GitHub]
-   [Talk to Twilio Support][Talk to Twilio Support]

  [Pasos siguientes]: #NextSteps
  [Precios de Twilio]: #Pricing
  [Conceptos]: #Concepts
  [Creación de una cuenta de Twilio]: #CreateAccount
  [Creación de una aplicación de Ruby Sinatra]: #create_app
  [Configuración de su aplicación para utilizar bibliotecas de Twilio]: #configure_app
  [Direccionamiento del una llamada saliente]: #howto_make_call
  [Direccionamiento del de un mensaje SMS]: #howto_recieve_sms
  [Direccionamiento del adicionales]: #additional_services
  [Twilio Pricing]: http://www.twilio.com/pricing
  [oferta especial]: http://ahoy.twilio.com/azure
  [bibliotecas API de Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API de Twilio]: http://www.twilio.com/api
  [evaluación de Twilio]: https://www.twilio.com/try-twilio
  [página de la cuenta de Twilio]: https://www.twilio.com/user/account
  [administración de números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [biblioteca auxiliar de Twilio para Ruby]: https://www.twilio.com/docs/ruby/install
  [configure una nueva VM de Linux de Azure]: http://www.windowsazure.com/es-es/develop/ruby/tutorials/web-app-with-linux-vm/
  [Sinatra]: http://www.sinatrarb.com/
  [Twilio Security Guidelines]: http://www.twilio.com/docs/security
  [Twilio HowTos and Example Code]: http://www.twilio.com/docs/howto
  [Twilio Quickstart Tutorials]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub]: https://github.com/twilio
  [Talk to Twilio Support]: http://www.twilio.com/help/contact
