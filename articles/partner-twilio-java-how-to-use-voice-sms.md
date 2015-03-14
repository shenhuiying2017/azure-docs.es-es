<properties 
	pageTitle="Uso de Twilio para voz y SMS (Java) - Azure" 
	description="Aprenda a realizar llamadas telefónicas y a enviar mensajes SMS con el servicio de la API de Twilio en Azure. Ejemplos de código escritos en Java." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Uso de Twilio para capacidades de voz y SMS en Java

Esta guía describe cómo realizar tareas comunes de programación con el servicio de API de Twilio en Azure. Entre los escenarios descritos se incluyen realizar una llamada telefónica y enviar un mensaje de servicio de mensajes cortos (SMS). Para obtener más información acerca de Twilio y cómo usar el servicio de voz y SMS en sus aplicaciones, consulte la sección [Pasos siguientes](#NextSteps) .

## Tabla de contenido
* [¿Qué es Twilio?](#WhatIs)
* [Precios de Twilio](#Pricing)
* [Conceptos](#Concepts)
* [Creación de una cuenta de Twilio](#CreateAccount)
* [Comprobación de números de teléfono](#VerifyPhoneNumbers)
* [Creación de una aplicación Java](#create_app)
* [Configuración de su aplicación para utilizar bibliotecas de Twilio](#configure_app)
* [Cómo realizar una llamada saliente](#howto_make_call)
* [Cómo enviar un mensaje SMS](#howto_send_sms)
* [Cómo proporcionar respuestas de TwiML desde su propio sitio web](#howto_provide_twiml_responses)
* [Cómo usar los servicios Twilio adicionales](#AdditionalServices)
* [Pasos siguientes](#NextSteps)

<h2><a id="WhatIs"></a>¿Qué es Twilio?</h2>
Twilio es una API de servicio web de telefonía que le permite utilizar las habilidades y lenguajes web existentes para crear aplicaciones de voz y SMS. Twilio es un servicio de terceros (no una característica de Azure ni tampoco un producto de Microsoft).

**Twilio Voice** permite que sus aplicaciones realicen y reciban llamadas. **Twilio SMS** permite que sus aplicaciones envíen y reciban mensajes SMS. **Twilio Client** permite que sus aplicaciones habiliten la comunicación por voz a través de conexiones existentes a Internet, incluidas las conexiones móviles.

<h2><a id="Pricing"></a>Precios de Twilio y ofertas especiales</h2>
Puede conseguir más información acerca de los precios de Twilio en [Precios de Twilio] [twilio_pricing]. Los clientes de Azure recibirán una [oferta especial][special_offer]: 1000 mensajes o 1000 minutos para llamar totalmente gratuitos. Si desea suscribirse para conseguir esta oferta o para tener más información, visite [http://ahoy.twilio.com/azure][special_offer].  

<h2><a id="Concepts"></a>Conceptos</h2>
La API de Twilio es una API de RESTful que proporciona funciones de voz y SMS para las aplicaciones. Dispone de las bibliotecas de cliente en varios idiomas; si desea consultar la lista de idiomas, visite las [bibliotecas API de Twilio] [twilio_libraries].

Aspectos fundamentales de la API de Twilio son los verbos de Twilio y el lenguaje de marcado de Twilio (TwiML).

<h3><a id="Verbs"></a>Verbos de Twilio</h3>
La API usa los verbos de Twilio; por ejemplo, el verbo **&lt;Say&gt;** indica a Twilio que entregue de manera audible un mensaje en una llamada. 

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

<h3><a id="TwiML"></a>TwiML</h3>
TwiML es un conjunto de instrucciones basadas en XML y en los verbos de Twilio que informan a Twilio sobre cómo procesar una llamada o un SMS.

Como ejemplo, el siguiente TwiML convierte el texto **Hello World** en voz.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Cuando su aplicación llama a la API de Twilio, uno de los parámetros de API es la URL que devuelve la respuesta de TwiML. Con fines de desarrollo, puede usar las URL que ofrece Twilio para proporcionar las respuestas de TwiML que usaron sus aplicaciones. También puede hospedar sus propias URL para producir las repuestas de TwiML; otra opción es usar el objeto **TwiMLResponse**.

Para obtener más información acerca de los verbos de Twilio, sus atributos y TwiML, vea [TwiML] [twiml]. Para obtener información adicional acerca de la API de Twilio, vea la [API de Twilio] [twilio_api] (en inglés).

<h2><a id="CreateAccount"></a>Creación de una cuenta de Twilio</h2>
Cuando desee tener una cuenta de Twilio, puede registrarse en [Probar Twilio] [try_twilio]. Puede empezar con una cuenta gratuita y, posteriormente, actualizarla.

Cuando se registre para obtener una cuenta de Twilio, recibirá un identificador de cuenta y un token de autenticación. Necesitará ambos para realizar llamadas a la API de Twilio. Para evitar el acceso no autorizado a su cuenta, mantenga protegido el token de autenticación. Puede ver el id. de cuenta y el token de autenticación en la [página de la cuenta de Twilio] [twilio_account], en los campos etiquetados **ACCOUNT SID** y **AUTH TOKEN**, respectivamente.

<h2><a id="create_app"></a>Creación de una aplicación Java</h2>
1. Obtenga el archivo JAR de Twilio y agréguelo a su ruta de acceso de compilación de Java y a su ensamblado de implementación de WAR. En [https://github.com/twilio/twilio-java][twilio_java], puede descargar el código fuente de GitHub y crear su propio archivo JAR, o bien puede descargar un archivo JAR previamente generado (con o sin dependencias).
2. Asegúrese de que el KeyStore **cacerts** del JDK contenga el certificado Entidad de certificación segura de Equifax con la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 es D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este es el certificado del servicio [https://api.twilio.com][twilio_api_service] emitido por la entidad de certificados (CA) que se tendrá en consideración cada vez que use las API de Twilio. Para obtener información sobre cómo asegurarse de que su almacén (KeyStore) **de certificados CA** de JDK contiene el certificado CA correcto, consulte [Agregar un certificado al almacén de certificados CA de Java][add_ca_cert].

Puede conseguir instrucciones detalladas sobre el uso de la biblioteca de cliente de Twilio para Java en [Cómo realizar una llamada de teléfono con Twilio en una aplicación de Java en Azure][howto_phonecall_java].

<h2><a id="configure_app"></a>Configuración de su aplicación para utilizar bibliotecas de Twilio</h2>
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

<h2><a id="howto_make_call"></a>Cómo realizar una llamada saliente</h2>
A continuación, se indica cómo realizar una llamada saliente con la clase **CallFactory**. Este código también utiliza un sitio que proporciona Twilio para devolver la respuesta de lenguaje de marcado de Twilio (TwiML). Reemplace los valores de los números de teléfono **From** y **To** y asegúrese de comprobar el número de teléfono **From** de su cuenta de Twilio antes de ejecutar el código.

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

Si desea obtener más información acerca de los parámetros transmitidos al método **CallFactory.create**, consulte [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Como se mencionó, este código usa el sitio proporcionado por Twilio para devolver la respuesta de TwiML. En lugar de lo anterior, podría utilizar su propio sitio web para proporcionar la respuesta de TwiML; si desea obtener información, vea [Inserción de respuestas de TwiML en una aplicación Java en Azure](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Configuración mensaje SMS</h2>
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
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Si desea obtener más información acerca de los parámetros transmitidos al método **SmsFactory.create**, consulte [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

<h2><a id="howto_provide_twiml_responses"></a>Cómo: Proporcionar respuestas de TwiML desde su propio sitio web</h2>
Cuando la aplicación inicia una llamada a la API de Twilio, por ejemplo, a través del método **CallFactory.create**, Twilio envía su solicitud a una URL que debe devolver una respuesta de TwiML. El ejemplo anterior usa la URL [http://twimlets.com/message][twimlet_message_url] proporcionada por Twilio. Aun cuando TwiML está diseñado para que lo usen los servicios web, puede ver el TwiML en su explorador. Por ejemplo, haga clic en [http://twimlets.com/message][twimlet_message_url] para ver un elemento **&lt;Response&gt;** vacío; asimismo, puede ver otro ejemplo haciendo clic en [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] para ver un elemento **&lt;Response&gt;** que contenga un elemento **&lt;Say&gt;**.

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

El parámetro **ApiVersion** está disponible en solicitudes de voz de Twilio (no en solicitudes de SMS). Para ver los parámetros de solicitud disponibles para solicitudes de SMS y de voz de Twilio, vea <https://www.twilio.com/docs/api/twiml/twilio_request> y <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, respectivamente. La variable de entorno **RoleName** está disponible como parte de una implementación de Azure. (Si desea agregar variables de entorno personalizadas para que se puedan elegir en **System.getenv**, consulte la sección de variables de entorno en [Ajustes de configuración de rol][misc_role_config_settings]).

Una vez que tenga configurada su página de JSP para proporcionar respuestas de TwiML, utilice la URL de la página de JSP dado que la URL se transmitió al método **CallFactory.create**. Por ejemplo, si tiene una aplicación web llamada MyTwiML implementada en un servicio hospedado de Azure y el nombre de la página de JSP es mytwiml.jsp; la URL se puede transmitir a **CallFactory.create**, tal como aparece a continuación:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Otra opción para responder con TwiML es mediante la clase **TwiMLResponse**, que está disponible en el paquete **com.twilio.sdk.verbs**.

Si desea obtener más información acerca de cómo usar Twilio en Azure con Java, consulte [Cómo hacer una llamada de teléfono con Twilio en una aplicación Java en Azure][howto_phonecall_java].

<h2><a id="AdditionalServices"></a>Cómo usar los servicios Twilio adicionales</h2>
Además de los ejemplos aquí mostrados, Twilio ofrece API basadas en Web que puede utilizar para aprovechar las funciones adicionales de Twilio desde su aplicación de Azure. Para más información, consulte la [Documentación de API de Twilio] [twilio_api_documentation].

<h2><a id="NextSteps"></a>Pasos siguientes</h2>
Ahora que conoce los fundamentos del servicio Twilio, siga estos vínculos para obtener más información:

* [Directrices de seguridad de Twilio] [twilio_security_guidelines]
* [Twilio HowTo's and Example Code] [twilio_howtos]
* [Tutoriales de inicio rápido de Twilio][twilio_quickstarts] 
* [Twilio on GitHub] [twilio_on_github]
* [Talk to Twilio Support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: ../java-add-certificate-ca-store
[howto_phonecall_java]: ../partner-twilio-java-phone-call-example
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
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

<!--HONumber=45-->
