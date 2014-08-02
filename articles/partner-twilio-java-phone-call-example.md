<properties linkid="develop-java-how-to-twilio-phone-call" urlDisplayName="How to Make a Phone Call from Twilio in Java" pageTitle="How to Make a phone call from Twilio (Java) - Azure" metaKeywords="Azure Twilio call, Twilio call website, Azure Twilio Java" description="Learn how to make a phone call from a web page using Twilio in a Java application on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to Make a Phone Call Using Twilio in a Java Application on Azure" authors="waltpo" videoId="" scriptId="" solutions="" manager="bjsmith" editor="mollybos" />

Realización de una llamada telefónica con Twilio en una aplicación Java en Azure
================================================================================

El siguiente ejemplo muestra cómo se puede usar Twilio para realizar una llamada desde una página web hospedada en Azure. La aplicación resultante le pedirá al usuario los valores de una llamada telefónica, como se muestra en la siguiente captura de pantalla.

![Formulario de llamada de Azure con Twilio y Java](./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg)

Tendrá que hacer lo siguiente para usar el código de este tema:

1.  Adquiera una cuenta de Twilio y un token de autenticación. Para empezar con Twilio, evalúe los precios en <http://www.twilio.com/pricing>. Puede registrarse en [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio). Para obtener información acerca de la API proporcionada por Twilio, consulte <http://www.twilio.com/api>.
2.  Compruebe su número de teléfono como identificador de llamadas salientes con Twilio. Para obtener información acerca de cómo comprobar el número de teléfono, consulte [https://www.twilio.com/user/account/phone-numbers/verified \#](https://www.twilio.com/user/account/phone-numbers/verified#). Como alternativa al uso de un número existente, puede adquirir un número de teléfono en Twilio. Para efectos de este ejemplo, use el número de teléfono comprobado como el valor **From** de callform.jsp (descrito más adelante).
3.  Obtenga el JAR de Twilio. En [https://github.com/twilio/twilio-java](http://github.com/twilio/twilio-java) puede descargar los orígenes de GitHub y crear su propio archivo JAR, o bien, puede descargar un archivo JAR generado previamente (con o sin dependencias). El código de este tema se escribió usando JAR TwilioJava 3.3.8 con dependencias previamente compilado.
4.  Agregue el JAR a su ruta de acceso de compilación Java.
5.  Si está usando Eclipse para crear esta aplicación Java, incluya el JAR de Twilio en su archivo de implementación de la aplicación (WAR) utilizando la característica de ensamblado de implementación de Eclipse. Si no va a usar Eclipse para crear esta aplicación Java, asegúrese de que el JAR de Twilio se incluya dentro del mismo rol de Azure que la aplicación Java y se agregue a la ruta de clase de la aplicación.
6.  Asegúrese de que el keystore de certificados CA contenga el certificado de la entidad de certificación Equifax Secure con la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (el número de serie es 35:DE:F4:CF y la huella digital SHA1 es D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Este es el certificado de la entidad de certificación (CA) para el servicio [https://api.twilio.com](http://api.twilio.com), que se llama cuando se utilizan las API de Twilio. Para obtener información acerca de cómo agregar este certificado CA a su almacén de certificados CA de JDK, consulte [Incorporación de un certificado al almacén de certificados CA de Java](../java-add-certificate-ca-store).

Además, es muy recomendable familiarizarse con la información que se incluye en [Creación de una aplicación Hello World con el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)](http://msdn.microsoft.com/en-us/library/windowsazure/hh690944.aspx), o con otras técnicas para hospedar aplicaciones Java en Azure, si no está usando Eclipse.

Creación de un formulario web para hacer una llamada
----------------------------------------------------

El siguiente código muestra cómo crear un formulario web para recuperar datos de usuario para hacer una llamada. Para este ejemplo, se creó un nuevo proyecto web dinámico, llamado **TwilioCloud**, y se agregó **callform.jsp** como archivo JSP.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Formulario para llamada automatizada</title>
    </head>
    <body>
     <p>Rellene los campos y haga clic en <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>A:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>De:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Mensaje de la llamada:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

Creación del código para realizar la llamada
--------------------------------------------

El siguiente código, que se llama cuando el usuario completa el formulario mostrado por callform.jsp, crea el mensaje de llamada y genera la llamada. Para los fines de este ejemplo, el archivo JSP se llama **makecall.jsp** y se agregó al proyecto **TwilioCloud**. (Use la cuenta de Twilio y el token de autenticación en lugar de los valores de marcador de posición asignados a **accountSID** y **authToken** en el código a continuación).

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Usar el SID de su cuenta y su token de autenticación en vez
         // de los marcadores de posición que se muestran aquí.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";
     
         // Crear una instancia de una instancia del cliente de Twilio.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Recuperar la cuenta, que se usa después para recuperar CallFactory.
         Account account = client.getAccount();

         // Mostrar el extremo del cliente. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");
     
         // Mostrar la versión de API.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Recuperar los valores escritos por el usuario.
         String callTo = request.getParameter("callTo");  
         // El Id. de la persona que llama saliente, que se usa para el parámetro From,
         // debe haberse comprobado previamente con Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");
     
         // Reemplazar espacios en el texto del usuario por '%20', 
         // para que el texto sea adecuado para una dirección URL.
         userText = userText.replace(" ", "%20");
     
         // Crear una dirección URL usando el mensaje de Twilio y el texto escrito por el usuario.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;
     
         // Mostrar la dirección URL del mensaje.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Colocar los valores de llamada From, To y URL en un mapa hash. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);
     
         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Además de hacer la llamada, makecall.jsp muestra el extremo de Twilio, la versión de API y el estado de la llamada. Un ejemplo es la siguiente captura de pantalla:

![Respuesta de llamada de Azure con Twilio y Java](./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg)

Ejecución de la aplicación
--------------------------

A continuación se presentan los pasos generales para ejecutar su aplicación; los detalles de estos pasos se pueden encontrar en [Creación de una aplicación Hello World con el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)](http://msdn.microsoft.com/en-us/library/windowsazure/hh690944.aspx).

1.  Exporte su TwilioCloud WAR a la carpeta de Azure **approot**.
2.  Modifique **startup.cmd** para descomprimir el TwilioCloud WAR.
3.  Compile la aplicación para el emulador de proceso.
4.  Comience su implementación en el emulador de proceso.
5.  Abra un explorador y ejecute **http://localhost:8080/TwilioCloud/callform.jsp**.
6.  Escriba valores en el formulario, haga clic en **Make this call** y, a continuación, vea los resultados en makecall.jsp.

Cuando esté listo para implementar en Azure, recompile para su implementación en la nube, implemente en Azure y ejecute http://*your\_hosted\_name*.cloudapp.net/TwilioCloud/callform.jsp en el explorador (reemplace su valor por *your\_hosted\_name*).

Pasos siguientes
----------------

Este código se ha proporcionado para mostrar la funcionalidad básica usando Twilio en Java en Azure. Antes de realizar la implementación en el entorno de producción de Azure, es posible que desee agregar un mayor control de errores u otras características. Por ejemplo:

-   En lugar de usar un formulario web, puede usar blobs de almacenamiento de Azure o una base de datos SQL para almacenar los números de teléfono y el texto de llamada. Para obtener información sobre el uso de blobs de almacenamiento de Azure en Java, consulte [Uso del servicio de almacenamiento de blobs desde Java](http://www.windowsazure.com/en-us/develop/java/how-to-guides/blob-storage/). Para obtener información sobre el uso de bases de datos SQL en Java, consulte [Uso de bases de datos SQL en Java](http://msdn.microsoft.com/en-us/library/windowsazure/hh749029.aspx).
-   Podría usar **RoleEnvironment.getConfigurationSettings** para recuperar el Id. de la cuenta de Twilio y el token de autenticación de los ajustes de configuración de su implementación, en vez de codificar de forma rígida los valores en makecall.jsp. Para obtener información sobre la clase **RoleEnvironment**, consulte [Uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP](http://msdn.microsoft.com/en-us/library/windowsazure/hh690948.aspx) y la documentación del paquete de tiempo de ejecución del servicio de Azure en <http://dl.windowsazure.com/javadoc>.
-   El código makecall.jsp asigna una URL proporcionada por Twilio, <http://twimlets.com/message>, a la variable **Url**. Esta URL proporciona una respuesta del Lenguaje de marcado de Twilio (TwiML) que informa a Twilio acerca de cómo proceder con la llamada. Por ejemplo, la TwiML que se devuelve puede contener un verbo **&lt;Say\>** que se traduce en el texto que se está hablado con el destinatario de la llamada. En lugar de usar la dirección URL proporcionada por Twilio, podría construir su propio servicio para responder a la solicitud de Twilio; para obtener más información, consulte [Uso de Twilio para capacidades de voz y SMS en Java](../partner-twilio-java-how-to-use-voice-sms). Se puede encontrar más información sobre TwiML en <http://www.twilio.com/docs/api/twiml>, y más información sobre **&lt;Say\>** y otros verbos de Twilio en <http://www.twilio.com/docs/api/twiml/say>.
-   Lea las directrices de seguridad de Twilio en [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).

Para obtener información adicional acerca de Twilio, consulte [https://www.twilio.com/docs](http://www.twilio.com/docs).

Otras referencias
-----------------

-   [Uso de Twilio para capacidades de voz y SMS en Java](../partner-twilio-java-how-to-use-voice-sms)
-   [Incorporación de un certificado al almacén de certificados CA de Java](../java-add-certificate-ca-store)

