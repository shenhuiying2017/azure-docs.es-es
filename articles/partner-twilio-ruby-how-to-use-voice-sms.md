<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="" solutions="" manager="" editor="" />

Uso de Twilio para capacidades de voz y SMS en Ruby
===================================================

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [¿Qué es Twilio?](#WhatIs)
-   [Precios de Twilio](#Pricing)
-   [Conceptos](#Concepts)
-   [Creación de una cuenta de Twilio](#CreateAccount)
-   [Creación de una aplicación de Ruby Sinatra](#create_app)
-   [Configuración de su aplicación para utilizar bibliotecas de Twilio](#configure_app)
-   [Realización de una llamada saliente](#howto_make_call)
-   [Recepción de un mensaje SMS](#howto_recieve_sms)
-   [Uso de servicios Twilio adicionales](#additional_services)
-   [Pasos siguientes](#NextSteps)

¿Qué es Twilio?
---------------

Twilio es una API de servicio web de telefonía que le permite utilizar las habilidades y lenguajes web existentes para crear aplicaciones de voz y SMS. Twilio es un servicio de terceros (no una característica de Azure ni tampoco un producto de Microsoft).

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite que sus aplicaciones habiliten la comunicación por voz a través de conexiones existentes a Internet, incluidas las conexiones móviles.

Precios de Twilio y ofertas especiales
--------------------------------------

En [Twilio Pricing](http://www.twilio.com/pricing) (en inglés) puede encontrar información sobre el precio de Twilio. Los clientes de Azure reciben una [oferta especial](http://ahoy.twilio.com/azure): un crédito gratis de 1000 textos o 1000 minutos entrantes. Para registrarse para obtener esta oferta o si desea más información, visite <http://ahoy.twilio.com/azure>.

Conceptos
---------

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio](https://www.twilio.com/docs/libraries) (en inglés).

### TwiML

TwiML es un conjunto de instrucciones basadas en XML que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <xml version="1.0" encoding="UTF-8">
    <Response>
       <Say>Hello World</Say>
    </Response>

Todos los documentos de TwinML disponen de `<Response>` como elemento raíz. A partir de ahí, puede usar los verbos de Twilio para definir el comportamiento de la aplicación.

### Verbos de TwiML

Los verbos de Twilio son etiquetas XML que indican a Twilio qué **hacer**. Por ejemplo, el verbo **&lt;Say\>** indica a Twilio que se envíe de forma audible un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio.

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

Para obtener más información sobre los verbos de Twilio, sus atributos y TwiML, consulte [TwiML](http://www.twilio.com/docs/api/twiml). Para obtener información adicional sobre la API de Twilio, consulte la [API de Twilio](http://www.twilio.com/api) (en inglés).

Creación de una cuenta de Twilio
--------------------------------

Cuando esté preparado para obtener una cuenta de Twilio, regístrese en la [evaluación de Twilio](https://www.twilio.com/try-twilio) (en inglés). Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando registre la cuenta Twilio, obtendrá un número de teléfono gratuito para la aplicación. Recibirá también un SID de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el SID de cuenta y el token de autenticación en la [página de la cuenta de Twilio](https://www.twilio.com/user/account) (en inglés) en los campos etiquetados como **SID AUTEN** y **TOKEN AUTEN**, respectivamente.

### Comprobación de números de teléfono

Además del número proporcionado por Twilio, también puede comprobar los números que controle (es decir, el número de teléfono de casa o del móvil) para usarlos en las aplicaciones.

Para obtener más información sobre cómo comprobar un número de teléfono, consulte la [administración de números](https://www.twilio.com/user/account/phone-numbers/verified#).

Creación de una aplicación de Ruby
----------------------------------

Una aplicación de Ruby que usa el servicio Twilio y que se ejecuta en Azure no es distinta a otra aplicación Ruby que use el servicio Twilio. Mientras que los servicios Twilio son RESTful y pueden llamarse a partir de Ruby de determinadas formas, este artículo se centrará en cómo usar los servicios Twilio con la [biblioteca auxiliar de Twilio para Ruby](https://www.twilio.com/docs/ruby/install) (en inglés).

Primero, [configure una nueva VM de Linux de Azure](http://www.windowsazure.com/es-es/develop/ruby/tutorials/web-app-with-linux-vm/) para que actúe como host para la nueva aplicación web de Ruby. Ignore los pasos relacionados con la creación de una aplicación Rails, solo configure la VM. Asegúrese de crear un extremo con un puerto externo de 80 y un puerto interno de 5000.

En los ejemplos siguientes, usaremos [Sinatra](http://www.sinatrarb.com/), un marco web simple para Ruby. Sin embargo, puede usar verdaderamente la biblioteca auxiliar de Twilio para Ruby con otro marco web, incluido Ruby en Rails.

SSH en la nueva VM y cree un directorio para la nueva aplicación. Dentro del directorio, cree un archivo llamado Gemfile y copie el siguiente código en él:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

En la línea de comandos, ejecute `bundle install`. De esta forma, se instalarán las dependencias anteriores. A continuación, cree un archivo denominado `web.rb`. Será donde se encuentre el código para la aplicación web. Pegue el siguiente código en él:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

En este momento, debe poder ejecutar el comando `ruby web.rb -p 5000`. De esta forma, podrá ejecutar un servidor web reducido en el puerto 5000. Debe poder buscar en esta aplicación en el explorador visitando la dirección URL que configure para la VM de Azure. Una vez que pueda obtener acceso a la aplicación web en el explorador, podrá comenzar a generar una aplicación Twilio.

Configuración de su aplicación para usar Twilio
-----------------------------------------------

Puede configurar la aplicación web para usar la biblioteca de Twilio mediante la actualización de `Gemfile` para incluir esta línea:

    gem 'twilio-ruby'

En la línea de comandos, ejecute `bundle install`. Ahora abra `web.rb` e incluya esta línea en la parte superior:

    require 'twilio-ruby'

Ahora tiene todo configurado para usar la biblioteca auxiliar de Twilio para Ruby en la aplicación web.

Realización de una llamada saliente
-----------------------------------

A continuación se muestra cómo realizar una llamada saliente. Entre los conceptos clave se incluyen el uso de la biblioteca auxiliar de Twilio para Ruby para realizar llamadas de la API REST y la representación de TwiML. Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

Agregue esta función a `web.md`:

    # Establecer el ID de cuenta y el token de autenticación.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # El número de teléfono que inicia la llamada.
    # Debe ser un número Twilio o un número que haya comprobado
    from = "NNNNNNNNNNN";

    # El número de teléfono que recibe la llamada.
    to = "NNNNNNNNNNN";

    # Usar el sitio que proporciona Twilio para la respuesta de TwiML.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Crear el cliente de llamada.
      client = Twilio::REST::Client.create(sid, token);
      
      # Realizar la llamada
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Si abre `http://yourdomain.cloudapp.net/make_call` en un explorador, activará la llamada en la API de Twilio para realizar la llamada telefónica. Los primeros dos parámetros en `client.account.calls.create` son totalmente explicativos: el número de la llamada es `from` y el número de la llamada es `to`.

El tercer parámetro (`url`) es la dirección URL que Twilio solicita para obtener instrucciones sobre qué hacer cuando la llamada se conecta. En este caso, configuramos una dirección URL (`http://yourdomain.cloudapp.net`) que devuelve un documento TwiML simple y usa el verbo `<Say>` para pasar texto a voz y decir "Hello Monkey" a la persona que recibe la llamada.

Recepción de un mensaje SMS
---------------------------

En el ejemplo anterior, iniciamos una llamada telefónica **saliente**. Esta vez, usaremos un número de teléfono que proporcionó Twilio durante el registro para procesar un mensaje SMS **entrante**.

Primero, inicie sesión en el [panel de Twilio](https://www.twilio.com/user/account). Haga clic en "Numbers" en el panel de navegación superior y haga clic en el número de Twilio proporcionado. Ahora verá las dos direcciones URL que puede configurar. Una dirección URL de solicitud de voz y una dirección de URL de solicitud de SMS. Estas son las direcciones URL a las que llama Twilio cuando se realiza una llamada telefónica o se envía un SMS a su número. Las direcciones URL también se conocen como "enlaces web".

Nos gustaría procesar los mensajes SMS entrantes, por lo que vamos a actualizar la dirección URL a `http://yourdomain.cloudapp.net/sms_url`. Continúe y haga clic en la opción para guardar los cambios en la parte inferior de la página. Ahora vuelva a `web.rb`. Se programará la aplicación para llevar a cabo la administración.

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Después de realizar el cambio, asegúrese de reiniciar la aplicación web. Ahora, use el teléfono para enviar un SMS al número de Twilio. Recibirá pronto una respuesta al SMS que le dará las gracias por hacer ping, y le indicará que Twilio y Azure son perfectos.

Uso de servicios Twilio adicionales
-----------------------------------

Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en Web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para obtener los detalles completos, consulte [Twilio API documentation](http://www.twilio.com/api).

### Pasos siguientes

Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

-   [Twilio Security Guidelines](http://www.twilio.com/docs/security)
-   [Twilio HowTos and Example Code](http://www.twilio.com/docs/howto)
-   [Twilio Quickstart Tutorials](http://www.twilio.com/docs/quickstart)
-   [Twilio on GitHub](https://github.com/twilio)
-   [Talk to Twilio Support](http://www.twilio.com/help/contact)

