<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Uso de Twilio para capacidades de voz y SMS en Java

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [¿Qué es Twilio?][]
-   [Precios de Twilio][]
-   [Conceptos][]
-   [Creación de una cuenta de Twilio][]
-   [Comprobación de números de teléfono][]
-   [Creación de una aplicación Java][]
-   [Configuración de su aplicación para utilizar bibliotecas de Twilio][]
-   [Direccionamiento del una llamada saliente][]
-   [Direccionamiento del mensaje SMS][]
-   [Direccionamiento del TwiML desde su propio sitio web][]
-   [Direccionamiento del servicios Twilio adicionales][]
-   [Pasos siguientes][]

## <span id="WhatIs"></span></a>¿Qué es Twilio?

Twilio es una API de servicio web de telefonía que le permite utilizar las habilidades y lenguajes web existentes para crear aplicaciones de voz y SMS. Twilio es un servicio de terceros (no una característica de Azure ni tampoco un producto de Microsoft).

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite que sus aplicaciones habiliten la comunicación por voz a través de conexiones existentes a Internet, incluidas las conexiones móviles.

## <span id="Pricing"></span></a>Precios de Twilio y ofertas especiales

En [Twilio Pricing][] (en inglés) puede encontrar información sobre el precio de Twilio. Los clientes de Azure reciben una [oferta especial][]: un crédito gratis de 1000 textos o 1000 minutos entrantes. Para registrarse para obtener esta oferta o si desea más información, visite [][oferta especial]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Conceptos

La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Las bibliotecas de cliente están disponibles en varios idiomas; para ver una lista, consulte las [bibliotecas API de Twilio][] (en inglés).

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### <span id="Verbs"></span></a>Verbos de Twilio

La API utiliza los verbos de Twilio, por ejemplo, el verbo **\<Say\>** indica a Twilio que entregue de manera audible un mensaje en una llamada.

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

Es necesario comprobar varios números de teléfono con Twilio en la cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes, el número de teléfono se debe comprobar con Twilio como identificador de autor de una llamada de salida. De manera similar, si desea que un número de teléfono reciba mensajes SMS, se debe comprobar con Twilio el número de teléfono de recepción. Para obtener más información sobre cómo comprobar un número de teléfono, consulte la [administración de números][]. Parte del código que aparece a continuación se basa en números de teléfono que tendrá que comprobar con Twilio.

Como alternativa a utilizar un número existente para sus aplicaciones, puede comprar un número de teléfono de Twilio. Si desea obtener información sobre cómo comprar un número de teléfono de Twilio, consulte la [ayuda sobre números de teléfono de Twilio][] (en inglés).

## <span id="create_app"></span></a>Creación de una aplicación Java

1.  Obtenga el archivo JAR de Twilio y agréguelo a su ruta de acceso de compilación de Java y a su ensamblado de implementación de WAR. En [][]<https://github.com/twilio/twilio-java></a> puede descargar los orígenes de GitHub y crear su propio archivo JAR, o bien, puede descargar un archivo JAR generado previamente (con o sin dependencias).
2.  Asegúrese de que el KeyStore **cacerts** del JDK contenga el certificado Entidad de certificación segura de Equifax con la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 es D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este es el certificado de la entidad de certificación (CA) para el servicio [][1]<https://api.twilio.com></a>, que se llama cuando se utilizan las API de Twilio. Para obtener información sobre cómo asegurarse de que el KeyStore **cacerts** de su JDK contiene el certificado de CA, consulte [Incorporación de un certificado al almacén de certificados CA de Java][].

Puede obtener instrucciones detalladas sobre el uso de la biblioteca de cliente de Twilio para Java en [Realización de una llamada de teléfono con Twilio en una aplicación de Java en Azure][].

## <span id="configure_app"></span></a>Configuración de su aplicación para utilizar bibliotecas de Twilio

Dentro de su código, puede agregar instrucciones **import** en la parte superior de los archivos de origen para las clases o paquetes de Twilio que desea utilizar en su aplicación.

Para archivos de origen de Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Para archivos de origen de Java Server Page (JSP).

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

Dependiendo del paquete o la clase que desee utilizar, sus instrucciones **import** pueden ser distintas.

## <span id="howto_make_call"></span></a>Direccionamiento del llamada saliente

A continuación, se indica cómo realizar una llamada saliente con la clase **CallFactory**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Si desea obtener más información acerca de los parámetros transmitidos al método **CallFactory.create**, consulte [][2]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. En lugar de lo anterior, podría utilizar su propio sitio web para proporcionar la respuesta de TwiML; si desea obtener información, consulte [Inserción de respuestas de TwiML en una aplicación Java en Azure][Direccionamiento del TwiML desde su propio sitio web].

## <span id="howto_send_sms"></span></a>Direccionamiento del mensaje SMS

A continuación se muestra cómo enviar un mensaje SMS con la clase **SmsFactory**. Twilio proporciona el número **From** **4155992671**, que sirve para cuentas de evaluación de Twilio para enviar mensajes SMS. El número **To** se debe comprobar para la cuenta de Twilio antes de ejecutar el código.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

Si desea obtener más información acerca de los parámetros que se transmitieron al método **SmsFactory.create**, consulte [][3]<http://www.twilio.com/docs/api/rest/sending-sms></a>.

## <span id="howto_provide_twiml_responses"></span></a>Direccionamiento del Proporcionar respuestas de TwiML desde su propio sitio web

Cuando la aplicación inicia una llamada a la API de Twilio, por ejemplo, a través del método **CallFactory.create**, Twilio envía su solicitud a una URL que debe devolver una respuesta de TwiML. El ejemplo anterior utiliza la URL [][4]<http://twimlets.com/message></a> proporcionada por Twilio. Aun cuando TwiML está diseñado para que lo usen los servicios web, puede ver el TwiML en su explorador. Por ejemplo, haga clic en [][4]<http://twimlets.com/message></a> para ver un elemento **\<Response\>** vacío; otro ejemplo, haga clic en [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> para ver un elemento **\<Response\>** que consta de un elemento **\<Say\>**).

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio URL que devuelve procesos HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas HTTP; en este tema se asume que hospedará la URL en una página de JSP.

La siguiente página de código JSP resultó en una respuesta de TwiML que dice **Hello World** en la llamada.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

La siguiente página JSP resulta en una respuesta de TwiML que indica algún texto, tiene varias pausas y difunde información acerca de la versión de la API de Twilio y del nombre de rol de Azure.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

El parámetro **ApiVersion** está disponible en solicitudes de voz de Twilio (no en solicitudes de SMS). Para ver los parámetros de solicitud disponibles para solicitudes de voz y de SMS de Twilio, consulte <https://www.twilio.com/docs/api/twiml/twilio_request> y <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. La variable de entorno **RoleName** está disponible como parte de una implementación de Azure. (Si desea agregar variables de entorno personalizadas, para que se puedan elegir en **System.getenv**, consulte la sección de variables de entorno en [Ajustes de configuración de rol misceláneo][]).

Una vez que tenga configurada su página de JSP para proporcionar respuestas de TwiML, utilice la URL de la página de JSP dado que la URL se transmitió al método **CallFactory.create**. Por ejemplo, si tiene una aplicación web llamada MyTwiML implementada en un servicio hospedado de Azure y el nombre de la página de JSP es mytwiml.jsp; la URL se puede transmitir a **CallFactory.create**, tal como aparece a continuación:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Otra opción para responder con TwiML es mediante la clase **TwiMLResponse**, que está disponible en el paquete **com.twilio.sdk.verbs**.

Si desea obtener más información acerca de cómo es usar Twilio en Azure con Java, consulte [Realización de una llamada de teléfono con Twilio en una aplicación de Java en Azure][].

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
  [Creación de una aplicación Java]: #create_app
  [Configuración de su aplicación para utilizar bibliotecas de Twilio]: #configure_app
  [Direccionamiento del una llamada saliente]: #howto_make_call
  [Direccionamiento del mensaje SMS]: #howto_send_sms
  [Direccionamiento del TwiML desde su propio sitio web]: #howto_provide_twiml_responses
  [Direccionamiento del servicios Twilio adicionales]: #AdditionalServices
  [Twilio Pricing]: http://www.twilio.com/pricing
  [oferta especial]: http://ahoy.twilio.com/azure
  [bibliotecas API de Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API de Twilio]: http://www.twilio.com/api
  [evaluación de Twilio]: https://www.twilio.com/try-twilio
  [página de la cuenta de Twilio]: https://www.twilio.com/user/account
  [administración de números]: https://www.twilio.com/user/account/phone-numbers/verified#
  [ayuda sobre números de teléfono de Twilio]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [1]: https://api.twilio.com
  [Incorporación de un certificado al almacén de certificados CA de Java]: ../java-add-certificate-ca-store
  [Realización de una llamada de teléfono con Twilio en una aplicación de Java en Azure]: ../partner-twilio-java-phone-call-example
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://www.twilio.com/docs/api/rest/sending-sms
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [Ajustes de configuración de rol misceláneo]: http://msdn.microsoft.com/es-es/library/windowsazure/hh690945.aspx
  [Twilio Security Guidelines]: http://www.twilio.com/docs/security
  [Twilio HowTo's and Example Code]: http://www.twilio.com/docs/howto
  [Twilio Quickstart Tutorials]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub]: https://github.com/twilio
  [Talk to Twilio Support]: http://www.twilio.com/help/contact
