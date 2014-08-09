<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="" solutions="" manager="" editor="" />

Uso de Twilio para funciones de voz y SMS desde Azure
=====================================================

Esta guía describe cómo realizar tareas comunes de programación con el servicio API Twilio en Azure. Las situaciones cubiertas incluyen realizar una llamada telefónica y enviar un mensaje de Servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y mensajes SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [¿Qué es Twilio?](#WhatIs)
-   [Precio de Twilio](#Pricing)
-   [Conceptos](#Concepts)
-   [Creación de una cuenta de Twilio](#CreateAccount)
-   [Verificación de números de teléfono](#VerifyPhoneNumbers)
-   [Creación de una aplicación de Azure](#create_app)
-   [Configuración de su aplicación para el uso de bibliotecas de Twilio](#configure_app)
-   [Realización de una llamada saliente](#howto_make_call)
-   [Envío de un mensaje SMS](#howto_send_sms)
-   [Proporcionar respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses)
-   [Uso de servicios Twilio adicionales](#AdditionalServices)
-   [Pasos siguientes](#NextSteps)

¿Qué es Twilio?
---------------

Twilio está potenciando el futuro de las comunicaciones empresariales, al permitir que los desarrolladores inserten voz, VoIP y mensajería en las aplicaciones. Pueden virtualizar toda la infraestructura necesaria en un entorno global basado en la nube y exponerlo a través de la plataforma API de comunicaciones de Twilio. Las aplicaciones son sencillas de compilar y pueden escalarse. Disfrute de la flexibilidad de pagar por lo que utiliza y aproveche la confiabilidad de la nube.

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite realizar llamadas VoIP desde cualquier teléfono, tableta o explorador, y es compatible con WebRTC.

Precios de Twilio y ofertas especiales
--------------------------------------

Los clientes de Azure reciben una [oferta especial](http://www.twilio.com/azure): \$10 de regalo en crédito Twilio al actualizar su cuenta de Twilio. Este crédito Twilio se puede aplicar a cualquier uso de Twilio (\$10 de crédito equivalen al envío de aproximadamente 1.000 mensajes SMS o recibir hasta 1.000 minutos de voz entrante, según la ubicación del número de teléfono y el destino del mensaje o de la llamada). Canjee este crédito Twilio y comience en [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio es un servicio de pago por uso. No hay comisiones establecidas y puede cerrar su cuenta en cualquier momento. Puede encontrar más detalles en la [página de precios de Twilio](http://www.twilio.com/voice/pricing) (en inglés).

Conceptos
---------

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio](https://www.twilio.com/docs/libraries) (en inglés).

Los aspectos clave de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### Verbos de Twilio

La API usa dos verbos de Twilio; por ejemplo, el verbo **&lt;Say\>** indica a Twilio que entregue de manera audible un mensaje en una llamada.

A continuación se presenta una lista de verbos de Twilio. Obtenga información sobre los otros verbos y funciones mediante la [documentación del lenguaje de marcado de Twilio](http://www.twilio.com/docs/api/twiml) (en inglés).

-   **&lt;Dial\>**: Conecta la persona que llama con otro teléfono.
-   **&lt;Gather\>**: Recopila los dígitos numéricos que se introdujeron en el teclado del teléfono.
-   **&lt;Hangup\>**: Finaliza una llamada.
-   **&lt;Play\>**: Reproduce un archivo de audio.
-   **&lt;Pause\>**: Espera en silencio una cantidad de segundos específica.
-   **&lt;Record\>**: Graba la voz de la persona que llama y devuelve una dirección URL de un archivo que contiene la grabación.
-   **&lt;Redirect\>**: Transfiere el control de una llamada o SMS al TwiML en una URL diferente.
-   **&lt;Reject\>**: Rechaza una llamada entrante a su número de Twilio sin cobrarle.
-   **&lt;Say\>**: Convierte texto a voz que se crea en una llamada.
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

Cuando esté listo para obtener una cuenta de Twilio, regístrese en [Try Twilio](https://www.twilio.com/try-twilio). Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, guarde su token de autenticación en un lugar seguro. Su Id. de cuenta y token de autenticación se pueden ver en la [página de cuenta de Twilio](https://www.twilio.com/user/account) (en inglés), en los campos etiquetados **ACCOUNT SID** y **AUTH TOKEN**, respectivamente.

Verificación de números de teléfono
-----------------------------------

Es necesario verificar varios números de teléfono con Twilio para su cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes, el número de teléfono debe verificarse como un Id. saliente de la persona que llama con Twilio. De manera similar, si desea que un número de teléfono reciba mensajes SMS, el número de teléfono receptor debe verificarse con Twilio. Para obtener información sobre cómo verificar un número de teléfono, consulte la [administración de números](https://www.twilio.com/user/account/phone-numbers/verified#) (en inglés). Parte del código siguiente depende de los números de teléfono que necesitará verificar con Twilio.

Como alternativa al uso de un número existente para sus aplicaciones, puede adquirir un número de teléfono en Twilio. Para obtener información sobre la adquisición de un número teléfono de Twilio, consulte la [ayuda sobre los números de teléfono de Twilio](https://www.twilio.com/help/faq/phone-numbers) (en inglés).

Creación de una aplicación de Azure
-----------------------------------

Una aplicación de Azure que hospeda una aplicación habilitada para Twilio no es diferente de ninguna otra aplicación de Azure. Simplemente agregue la biblioteca .NET de Twilio y configure el rol que para usar las bibliotecas .NET de Twilio. Para obtener información sobre la creación de un proyecto inicial de Azure, consulte [Crear un proyecto de Azure con Visual Studio](http://msdn.microsoft.com/es-es/library/windowsazure/ee405487.aspx).

Configuración de su aplicación para el uso de bibliotecas de Twilio
-------------------------------------------------------------------

Twilio proporciona un conjunto de bibliotecas auxiliares .NET que encapsulan varios aspectos de Twilio a fin de proporcionar maneras sencillas y fáciles de interactuar con la API de Twilio REST y el Cliente de Twilio para producir respuestas TwiML.

Twilio proporciona cinco bibliotecas para desarrolladores de .NET:

<table  border="1">
    <tr>
        <th>Biblioteca</th>

        <th>Descripción</th>

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

Las bibliotecas se pueden [instalar usando la extensión del administrador de paquetes de NuGet](http://www.twilio.com/docs/csharp/install) disponible para Visual Studio 2010 y 2012. El código fuente se hospeda en [GitHub](https://github.com/twilio/twilio-csharp), que incluye un Wiki que contiene documentación completa para usar las bibliotecas.

De manera predeterminada, Microsoft Visual Studio 2010 instala la versión 1.2 de NuGet. La instalación de las bibliotecas de Twilio requiere la versión 1.6 de NuGet o posterior. Para obtener información sobre la instalación o actualización de NuGet, consulte <http://nuget.org/>.

**Nota:**

Para instalar la última versión de NuGet, primero debe desinstalar la versión cargada usando el Administrador de extensiones de Visual Studio. Para ello, debe ejecutar Visual Studio como administrador. De lo contrario, el botón de desinstalación estará deshabilitado.

### Para agregar dos bibliotecas de Twilio a su proyecto de Visual Studio:

1.  Abra su solución en Visual Studio.
2.  Haga clic con el botón secundario en **Referencias**.
3.  Haga clic en **Administrar paquetes de NuGet...**
4.  Haga clic en **En línea**.
5.  En el cuadro de búsqueda en línea, escriba *twilio*.
6.  Haga clic en **Install** en el paquete de Twilio.

Realización de una llamada saliente
-----------------------------------

A continuación se indica cómo realizar una llamada saliente usando la clase **TwilioRestClient**. Este código usa también un sitio proporcionado por Twilio para devolver la respuesta del Lenguaje de marcado de Twilio (TwiML). Reemplace sus valores por los números de teléfono de **De** y **A** y asegúrese de verificar el número de teléfono de **De** para su cuenta de Twilio antes de ejecutar el código.

    // Usar el SID de su cuenta y su token de autenticación en vez
    // de los marcadores de posición que se muestran aquí.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Crear una instancia del cliente de Twilio.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Usar el sitio que proporciona Twilio para la respuesta de TwiML.
    String Url="http://twimlets.com/message";
    Url = Url + "
    Message%5B0%5D=Hello%20World";

    // Crear una instancia de las opciones de llamada que se pasan
    // a la llamada saliente
    CallOptions options = new CallOptions();

    // Establecer los valores De, A y URL de la llamada que se van a usar para la llamada.
    // Esta muestra usa el número de Sandbox que proporciona
    // Twilio para realizar la llamada.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Realizar la llamada.
    var call = client.InitiateOutboundCall(options);

Para obtener más información sobre los parámetros que se pasan al método **client.InitiateOutboundCall**, consulte <http://www.twilio.com/docs/api/rest/making-calls>.

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. Podría en cambio usar su propio sitio para proporcionar la respuesta de TwiML. Para obtener más información, consulte [Entrega de respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses).

Envío de un mensaje SMS
-----------------------

A continuación se indica cómo enviar un mensaje SMS usando la clase **TwilioRestClient**. El número **De** lo proporciona Twilio para las cuentas de evaluación para enviar mensajes SMS. El número **To** se debe verificar para su cuenta de Twilio antes de ejecutar el código.

    // Usar el SID de su cuenta y su token de autenticación en vez
        // de los marcadores de posición que se muestran aquí.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Crear una instancia del cliente de Twilio.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Enviar un mensaje SMS.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //se produjo una excepción al realizar la llamada REST
            string message = result.RestException.Message;
        }

Entrega de respuestas de TwiML desde su propio sitio web
--------------------------------------------------------

Cuando su aplicación inicia una llamada a la API de Twilio, por ejemplo, mediante el método **client.InitiateOutboundCall**, Twilio envía su solicitud a una URL que se espera devuelva una respuesta de TwiML. El ejemplo en [Realización de una llamada saliente](#howto_make_call) usa la URL proporcionada por Twilio <http://twimlets.com/message> para devolver la respuesta.

**Nota:**

Aun cuando TwiML está diseñado para que lo usen los servicios web, puede ver el TwiML en su explorador. Por ejemplo, haga clic en [http://twimlets.com/message](twimlet\_message\_url) para ver un elemento &lt;Response\> vacío; otro ejemplo, haga clic en [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet\_message\_url\_hello\_world) para ver un elemento &lt;Response\> que contiene un elemento &lt;Say\>.

En vez de depender de la URL proporcionada por Twilio, puede crear su propio sitio URL que devuelve las respuestas HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas HTTP. En este tema se asume que va a hospedar la URL desde un controlador de ASP.NET genérico.

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

Para obtener información sobre TwiML, consulte <https://www.twilio.com/docs/api/twiml>.

Después de que haya configurado una manera de proporcionar respuestas de TwiML, puede pasar esa URL al método **client.InitiateOutboundCall**. Por ejemplo, si tiene una aplicación web llamada MyTwiML implementada en un servicio en la nube de Azure y el nombre de su controlador de ASP.NET es mytwiml.ashx, la URL puede traspasarse a **client.InitiateOutboundCall** como se muestra en el siguiente ejemplo de código:

    // Colocar los valores De, A y URL de la llamada en el mapa de hash.
    // Esta muestra usa el número de Sandbox que proporciona Twilio para realizar la llamada.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Realizar la llamada.
    var call = client.InitiateOutboundCall(options);

Para obtener información adicional sobre el uso de Twilio en Azure con ASP.NET, consulte los [pasos para realizar una llamada de teléfono usando Twilio en un rol web en Azure](../partner-twilio-cloud-services-dotnet-phone-call-web-role/) (en inglés).

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../includes/twilio_additional_services_and_next_steps.md)]

