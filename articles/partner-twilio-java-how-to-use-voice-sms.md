<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Uso de Twilio para capacidades de voz y SMS en Java
===================================================

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información sobre Twilio y el uso de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [¿Qué es Twilio?](#WhatIs)
-   [Precios de Twilio](#Pricing)
-   [Conceptos](#Concepts)
-   [Creación de una cuenta de Twilio](#CreateAccount)
-   [Comprobación de números de teléfono](#VerifyPhoneNumbers)
-   [Creación de una aplicación Java](#create_app)
-   [Configuración de su aplicación para utilizar bibliotecas de Twilio](#configure_app)
-   [Realización de una llamada saliente](#howto_make_call)
-   [Envío de un mensaje SMS](#howto_send_sms)
-   [Respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses)
-   [Uso de servicios Twilio adicionales](#AdditionalServices)
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

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

### Verbos de Twilio

La API utiliza los verbos de Twilio, por ejemplo, el verbo **&lt;Say\>** indica a Twilio que entregue de manera audible un mensaje en una llamada.

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

Es necesario comprobar varios números de teléfono con Twilio en la cuenta. Por ejemplo, si desea realizar llamadas de teléfono salientes, el número de teléfono se debe comprobar con Twilio como identificador de autor de una llamada de salida. De manera similar, si desea que un número de teléfono reciba mensajes SMS, se debe comprobar con Twilio el número de teléfono de recepción. Para obtener más información sobre cómo comprobar un número de teléfono, consulte la [administración de números](https://www.twilio.com/user/account/phone-numbers/verified#). Parte del código que aparece a continuación se basa en números de teléfono que tendrá que comprobar con Twilio.

Como alternativa a utilizar un número existente para sus aplicaciones, puede comprar un número de teléfono de Twilio. Si desea obtener información sobre cómo comprar un número de teléfono de Twilio, consulte la [ayuda sobre números de teléfono de Twilio](https://www.twilio.com/help/faq/phone-numbers) (en inglés).

Creación de una aplicación Java
-------------------------------

1.  Obtenga el archivo JAR de Twilio y agréguelo a su ruta de acceso de compilación de Java y a su ensamblado de implementación de WAR. En <https://github.com/twilio/twilio-java> puede descargar los orígenes de GitHub y crear su propio archivo JAR, o bien, puede descargar un archivo JAR generado previamente (con o sin dependencias).
2.  Asegúrese de que el KeyStore **cacerts** del JDK contenga el certificado Entidad de certificación segura de Equifax con la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 es D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este es el certificado de la entidad de certificación (CA) para el servicio <https://api.twilio.com>, que se llama cuando se utilizan las API de Twilio. Para obtener información sobre cómo asegurarse de que el KeyStore **cacerts** de su JDK contiene el certificado de CA, consulte [Incorporación de un certificado al almacén de certificados CA de Java](../java-add-certificate-ca-store).

Puede obtener instrucciones detalladas sobre el uso de la biblioteca de cliente de Twilio para Java en [Realización de una llamada de teléfono con Twilio en una aplicación de Java en Azure](../partner-twilio-java-phone-call-example).

Configuración de su aplicación para utilizar bibliotecas de Twilio
------------------------------------------------------------------

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

Realización de una llamada saliente
-----------------------------------

A continuación, se indica cómo realizar una llamada saliente con la clase **CallFactory**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de mercado de Twilio (TwiML). Sustituya los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

    // Usar el SID de su cuenta y su token de autenticación en vez
    // de los marcadores de posición que se muestran aquí.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Crear una instancia del cliente de Twilio.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Recuperar la cuenta, más adelante el usuario intentará crear una instancia de CallFactory.
    Account account = client.getAccount();

    // Usar el sitio que proporciona Twilio para la respuesta de TwiML.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Colocar los valores de llamada From, To y URL en un mapa hash. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Utilice su propio valor para el segundo parámetro.
    params.put("To", "NNNNNNNNNN");   // Utilice su propio valor para el segundo parámetro.
    params.put("Url", Url);

    // Crear una instancia de la clase CallFactory.
    CallFactory callFactory = account.getCallFactory();

    // Realizar la llamada.
    Call call = callFactory.create(params);

Si desea obtener más información acerca de los parámetros transmitidos al método **CallFactory.create**, consulte <http://www.twilio.com/docs/api/rest/making-calls>.

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. En lugar de lo anterior, podría utilizar su propio sitio web para proporcionar la respuesta de TwiML; si desea obtener información, consulte [Inserción de respuestas de TwiML en una aplicación Java en Azure](#howto_provide_twiml_responses).

Envío de un mensaje SMS
-----------------------

A continuación se muestra cómo enviar un mensaje SMS con la clase **SmsFactory**. Twilio proporciona el número **From** **4155992671**, que sirve para cuentas de evaluación de Twilio para enviar mensajes SMS. El número **To** se debe comprobar para la cuenta de Twilio antes de ejecutar el código.

    // Usar el SID de su cuenta y su token de autenticación en vez
    // de los marcadores de posición que se muestran aquí.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Crear una instancia del cliente de Twilio.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Recuperar la cuenta, más adelante el usuario intentará crear una instancia de SmsFactory.
    Account account = client.getAccount();

    // Enviar un mensaje SMS.
    // Colocar los valores de llamada From, To y Body en un mapa hash. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // El segundo parámetro es un número de teléfono que
                                         // Twilio conoce para cuentas de evaluación.
    smsParams.put("To", "NNNNNNNNNN");   // Use su propio valor para el segundo parámetro.
    smsParams.put("Body", "This is my SMS message.");

    // Crear una instancia de la clase SmsFactory.
    SmsFactory smsFactory = account.getSmsFactory();

    // Enviar el mensaje.
    Sms sms = smsFactory.create(smsParams);

Si desea obtener más información acerca de los parámetros que se transmitieron al método **SmsFactory.create**, consulte <http://www.twilio.com/docs/api/rest/sending-sms>.

Respuestas de TwiML desde su propio sitio web
---------------------------------------------

Cuando la aplicación inicia una llamada a la API de Twilio, por ejemplo, a través del método **CallFactory.create**, Twilio envía su solicitud a una URL que debe devolver una respuesta de TwiML. El ejemplo anterior utiliza la URL <http://twimlets.com/message> proporcionada por Twilio. Aun cuando TwiML está diseñado para que lo usen los servicios web, puede ver el TwiML en su explorador. Por ejemplo, haga clic en <http://twimlets.com/message> para ver un elemento **&lt;Response\>** vacío; otro ejemplo, haga clic en <http://twimlets.com/message?Message%5B0%5D=Hello%20World> para ver un elemento **&lt;Response\>** que consta de un elemento **&lt;Say\>**).

En lugar de confiar en la URL que Twilio proporciona, puede crear su propio sitio URL que devuelve procesos HTTP. Puede crear el sitio en cualquier lenguaje que devuelva respuestas HTTP; en este tema se asume que hospedará la URL en una página de JSP.

La siguiente página de código JSP resultó en una respuesta de TwiML que dice **Hello World** en la llamada.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

La siguiente página JSP resulta en una respuesta de TwiML que indica algún texto, tiene varias pausas y difunde información acerca de la versión de la API de Twilio y del nombre de rol de Azure.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hola, desde Azure</Say>
        <Pause></Pause>
        <Say>La versión de la API de Twilio es <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>El nombre de función de Azure es <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Adiós.</Say>
    </Response>

El parámetro **ApiVersion** está disponible en solicitudes de voz de Twilio (no en solicitudes de SMS). Para ver los parámetros de solicitud disponibles para solicitudes de voz y SMS de Twilio, consulte <https://www.twilio.com/docs/api/twiml/twilio\_request> y <https://www.twilio.com/docs/api/twiml/sms/twilio\_request>, respectivamente. La variable de entorno **RoleName** está disponible como parte de una implementación de Azure. (Si desea agregar variables de entorno personalizadas, para que se puedan elegir en **System.getenv**, consulte la sección de variables de entorno en [Ajustes de configuración de rol misceláneo](http://msdn.microsoft.com/en-us/library/windowsazure/hh690945.aspx)).

Una vez que tenga configurada su página de JSP para proporcionar respuestas de TwiML, utilice la URL de la página de JSP dado que la URL se transmitió al método **CallFactory.create**. Por ejemplo, si tiene una aplicación web llamada MyTwiML implementada en un servicio hospedado de Azure y el nombre de la página de JSP es mytwiml.jsp; la URL se puede transmitir a **CallFactory.create**, tal como aparece a continuación:

    // Colocar los valores From, To y URL de la llamada en el mapa hash. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Otra opción para responder con TwiML es mediante la clase **TwiMLResponse**, que está disponible en el paquete **com.twilio.sdk.verbs**.

Si desea obtener más información acerca de cómo es usar Twilio en Azure con Java, consulte [Realización de una llamada de teléfono con Twilio en una aplicación de Java en Azure](../partner-twilio-java-phone-call-example).

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

