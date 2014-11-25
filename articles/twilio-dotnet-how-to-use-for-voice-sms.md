<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />





# Uso de Twilio para funciones de voz y SMS desde Azure

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y mensajes SMS en sus aplicaciones, consulte la sección [Pasos siguientes][Pasos siguientes].

## Tabla de contenido

-   [¿Qué es Twilio?][¿Qué es Twilio?]
-   [Precio de Twilio][Precio de Twilio]
-   [Conceptos][Conceptos]
-   [Creación de una cuenta de Twilio][Creación de una cuenta de Twilio]
-   [Verificación de números de teléfono][Verificación de números de teléfono]
-   [Creación de una aplicación de Azure][Creación de una aplicación de Azure]
-   [Configuración de su aplicación para el uso de bibliotecas de Twilio][Configuración de su aplicación para el uso de bibliotecas de Twilio]
-   [Realización de una llamada saliente][Realización de una llamada saliente]
-   [Envío de un mensaje SMS][Envío de un mensaje SMS]
-   [Proporcionar respuestas de TwiML desde su propio sitio web][Proporcionar respuestas de TwiML desde su propio sitio web]
-   [Uso de servicios Twilio adicionales][Uso de servicios Twilio adicionales]
-   [Pasos siguientes][Pasos siguientes]

## <span id="WhatIs"></span></a>¿Qué es Twilio?

Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

## <span id="Pricing"></span></a>Precios de Twilio y ofertas especiales

Los clientes de Azure reciben una [oferta especial][oferta especial]: 10 $ de regalo en crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (10 $ de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en [ahoy.twilio.com/azure][ahoy.twilio.com/azure].

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en la [página de precios de Twilio][página de precios de Twilio] (en inglés).

## <span id="Concepts"></span></a>Conceptos

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio][bibliotecas API de Twilio] (en inglés).

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

## <span id="create_app"></span></a>Creación de una aplicación de Azure

Una aplicación de Azure que hospeda una aplicación habilitada para Twilio no es diferente de ninguna otra aplicación de Azure. Solo tiene que agregar la biblioteca .NET de Twilio y configurar el rol para usar bibliotecas .NET de Twilio.
Para obtener información sobre la creación de un proyecto inicial de Azure, consulte [Crear un proyecto de Azure con Visual Studio][Crear un proyecto de Azure con Visual Studio].

## <span id="configure_app"></span></a>Configuración de su aplicación para el uso de bibliotecas de Twilio

Twilio proporciona un conjunto de bibliotecas auxiliares .NET que encapsulan varios aspectos de Twilio a fin de proporcionar maneras sencillas y fáciles de interactuar con la API de Twilio REST y el Cliente de Twilio para producir respuestas TwiML.

Twilio proporciona cinco bibliotecas para desarrolladores de .NET:

<table border="1">
    <tr>
        <th>Biblioteca<th>
        </th>Descripción</th>
    </tr>
    <tr>
        <td>Twilio.API</td>
        <td>La biblioteca central de Twilio que encapsula la API de Twilio REST en una biblioteca .NET fácil de usar. Esta biblioteca está disponible para .NET, Silverlight y Windows Phone 7.</td>
    </tr>
    <tr>
        <td>Twilio.TwiML</td>
    <td>Proporciona un método .NET fácil de usar para generar una marca TwiML.</td>
    </tr>
    <tr>
        <td>Twilio.MVC</td>
        <td>Para los desarrolladores que usan ASP.NET MVC, esta biblioteca incluye un atributo TwilioController, TwiML ActionResult y de validación de solicitudes.</td>
    </tr>
    <tr>
        <td>Twilio.WebMatrix</td>
        <td>Para los desarrolladores que usan la herramienta de desarrollo WebMatrix gratuita de Microsoft, esta biblioteca contiene aplicaciones auxiliares de sintaxis Razor para diversas acciones de Twilio.</td>
    </tr>
    <tr>
        <td>Twilio.Client.Capability</td>
        <td>Contiene el generador de token de capacidad para usarlo con el SDK de JavaScript del Cliente de Twilio.</td>
    </tr>
</table>

Tenga en cuenta que todas las bibliotecas requieren .NET 3.5, Silverlight 4 o Windows Phone 7 o posterior.

Las muestras que se proporcionan en esta guía usan la biblioteca Twilio.API.

Las bibliotecas se pueden [instalar usando la extensión del administrador de paquetes de NuGet][instalar usando la extensión del administrador de paquetes de NuGet] disponible para Visual Studio 2010 y 2012. El código fuente se hospeda en [GitHub][GitHub], que incluye un Wiki que contiene documentación completa para usar las bibliotecas.

De manera predeterminada, Microsoft Visual Studio 2010 instala la versión 1.2 de NuGet. La instalación de las bibliotecas de Twilio requiere la versión 1.6 de NuGet o posterior. Para obtener información sobre la instalación o actualización de NuGet, consulte <http://nuget.org/></a>.

<div class="dev-callout">
<b>Nota:</b>
<p>Para instalar la última versión de NuGet, primero debe desinstalar la versión cargada usando el Administrador de extensiones de Visual Studio. Para ello, debe ejecutar Visual Studio como administrador. De lo contrario, el botón de desinstalación estará deshabilitado.</p>
</div>

### <span id="use_nuget"></span></a>Para agregar dos bibliotecas de Twilio a su proyecto de Visual Studio:

1.  Abra su solución en Visual Studio.
2.  Haga clic con el botón secundario en **Referencias**.
3.  Haga clic en **Administrar paquetes de NuGet...**
4.  Haga clic en **En línea**.
5.  En el cuadro de búsqueda en línea, escriba *twilio*.
6.  Haga clic en **Instalar** en el paquete de Twilio.

## <span id="howto_make_call"></span></a>Realización de una llamada saliente

A continuación se indica cómo realizar una llamada saliente usando la clase **TwilioRestClient**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

Para obtener más información sobre los parámetros que se pasan al método **client.InitiateOutboundCall**, consulte <http://www.twilio.com/docs/api/rest/making-calls></a>.

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. Podría en cambio usar su propio sitio para proporcionar la respuesta de TwiML. Para obtener más información, consulte [Proporcionar respuestas de TwiML desde su propio sitio web][Proporcionar respuestas de TwiML desde su propio sitio web].

## <span id="howto_send_sms"></span></a>Envío de un mensaje SMS

A continuación se indica cómo enviar un mensaje SMS usando la clase **TwilioRestClient**. El número **From** lo proporciona Twilio para las cuentas de evaluación para enviar mensajes SMS. El número **To** se debe comprobar para su cuenta de Twilio antes de ejecutar el código.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <span id="howto_provide_twiml_responses"></span></a>Proporcionar respuestas de TwiML desde su propio sitio web

Cuando su aplicación inicia una llamada a la API de Twilio, por ejemplo, mediante el método **client.InitiateOutboundCall**, Twilio envía su solicitud a una URL que se espera devuelva una respuesta de TwiML. El ejemplo de [Realización de una llamada saliente][Realización de una llamada saliente] usa la dirección URL <http://twimlets.com/message></a> de Twilio para devolver la respuesta.

<div class="dev-callout">
<b>Nota:</b>
<p>Aun cuando TwiML est&aacute; dise&ntilde;ado para que lo usen los servicios web, puede ver el TwiML en su explorador. Por ejemplo, haga clic en [http://twimlets.com/message](twimlet_message_url) para ver un elemento &lt;Response&gt; vac&iacute;o; otro ejemplo, haga clic en [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world) para ver un elemento &lt;Response&gt; que contiene un elemento &lt;Say&gt;.</p>
</div>

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio URL que devuelve procesos HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas HTTP. En este tema se asume que va a hospedar la URL desde un controlador de ASP.NET genérico.

El siguiente controlador de ASP.NET elabora una respuesta de TwiML que dice **Hello World** en la llamada.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Como puede ver en el ejemplo anterior, la respuesta de TwiML es sencillamente un documento XML. La biblioteca Twilio.TwiML contiene clases que generarán TwiML de manera automática. El siguiente ejemplo produce la respuesta equivalente como se muestra arriba, pero usa la clase TwilioResponse.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Para obtener información sobre TwiML, consulte <https://www.twilio.com/docs/api/twiml></a>.

Después de que haya configurado una manera de proporcionar respuestas de TwiML, puede pasar esa URL al método **client.InitiateOutboundCall**. Por ejemplo, si tiene una aplicación web llamada MyTwiML implementada en un servicio en la nube de Azure y el nombre de su controlador de ASP.NET es mytwiml.ashx, la URL puede traspasarse a **client.InitiateOutboundCall** como se muestra en el siguiente ejemplo de código:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);

Para obtener información adicional sobre el uso de Twilio en Azure con ASP.NET, consulte los [pasos para realizar una llamada de teléfono usando Twilio en un rol web en Azure][pasos para realizar una llamada de teléfono usando Twilio en un rol web en Azure] (en inglés).

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [Pasos siguientes]: #NextSteps
  [¿Qué es Twilio?]: #WhatIs
  [Precio de Twilio]: #Pricing
  [Conceptos]: #Concepts
  [Creación de una cuenta de Twilio]: #CreateAccount
  [Verificación de números de teléfono]: #VerifyPhoneNumbers
  [Creación de una aplicación de Azure]: #create_app
  [Configuración de su aplicación para el uso de bibliotecas de Twilio]: #configure_app
  [Realización de una llamada saliente]: #howto_make_call
  [Envío de un mensaje SMS]: #howto_send_sms
  [Proporcionar respuestas de TwiML desde su propio sitio web]: #howto_provide_twiml_responses
  [Uso de servicios Twilio adicionales]: #AdditionalServices
  [oferta especial]: http://www.twilio.com/azure
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [página de precios de Twilio]: http://www.twilio.com/voice/pricing
  [bibliotecas API de Twilio]: https://www.twilio.com/docs/libraries
  [documentación del lenguaje de marcado de Twilio]: http://www.twilio.com/docs/api/twiml
  [API de Twilio]: http://www.twilio.com/api
  [evaluación de Twilio]: https://www.twilio.com/try-twilio
  [página de la cuenta de Twilio]: https://www.twilio.com/user/account
  [administración de números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [ayuda sobre números de teléfono de Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Crear un proyecto de Azure con Visual Studio]: http://msdn.microsoft.com/es-es/library/windowsazure/ee405487.aspx
  [instalar usando la extensión del administrador de paquetes de NuGet]: http://www.twilio.com/docs/csharp/install
  [GitHub]: https://github.com/twilio/twilio-csharp
  http://nuget.org/
  http://www.twilio.com/docs/api/rest/making-calls
  http://twimlets.com/message
  https://www.twilio.com/docs/api/twiml
  [pasos para realizar una llamada de teléfono usando Twilio en un rol web en Azure]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/
