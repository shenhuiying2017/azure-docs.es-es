<properties 
	pageTitle="Introducción a los Centros de notificaciones de Azure" 
	description="Aprenda a usar los Centros de notificaciones de Azure para las notificaciones de inserción a los usuarios." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-chrome" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="03/15/2015" 
	ms.author="piyushjo"/>
	
# Introducción a los Centros de notificaciones

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Chrome.

Una de las ventajas principales de utilizar las notificaciones de la aplicación Chrome es que aparezcan las notificaciones en el contexto del explorador Google Chrome y no es necesario que se ejecute la aplicación de Chrome ni abrirla en el explorador (aunque el propio explorador Chrome debe estar ejecutándose). También obtendrá una vista consolidada de todas las notificaciones en la ventana de notificaciones de Chrome. 

>[AZURE.NOTE] Esto no es una notificación de inserción genérica en el explorador y es específica de las aplicaciones Chrome; consulte [Información general de aplicaciones de Chrome] para obtener más información. Las aplicaciones Chrome se conocían anteriormente como "Aplicaciones empaquetadas" y son diferentes de las "Aplicaciones hospedadas" más sencillas. Consulte [Aplicaciones Web instalables] para ver la diferencia. También puede ejecutar las aplicaciones Chrome en dispositivos móviles (Android y iOS) con Apache Cordova. Consulte [Aplicaciones Chrome en dispositivos móviles] para obtener más información. 

En este tutorial, puede crear una aplicación Chrome que reciba notificaciones de inserción mediante el servicio de mensajería en la nube de Google (GCM). Cuando haya terminado, podrá difundir notificaciones de inserción a todos los usuarios de Chrome que hayan instalado esta aplicación Chrome. 

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Habilitación del servicio de mensajería en la nube de Google (GCM)](#register)
* [Configuración del Centro de notificaciones](#configure-hub)
* [Conexión de la aplicación Chrome al Centro de notificaciones](#connect-app)
* [Envío de notificaciones a la aplicación Chrome](#send)
* [Pasos siguientes](#next-steps)

En este tutorial se demuestra el escenario de difusión sencillo con Centros de notificaciones. La configuración de GCM y del Centro de notificaciones de Azure es idéntica a la configuración para Android ya que [Mensajería en la nube de Google para Chrome] ha quedado desusada y el mismo GCM ahora admite dispositivos Android e instancias de Chrome. 

Asegúrese de seguirlo junto con los tutoriales de los pasos siguientes para saber cómo usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. 

>[AZURE.NOTE] para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google (GCM)

1. Diríjase al sitio web de la [consola en la nube de Google], inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en botón **CREAR PROYECTO**. Proporcione un **Nombre del proyecto** adecuado y haga clic en el botón **Crear**. 

   	![][1]

2. Tome nota del **número del proyecto** en la página Proyectos del proyecto que acaba de crear. Utilícelo como el **Id. de remitente de GCM** en la aplicación Chrome para registrarse con GCM.

   	![][2]

3. En el panel de la izquierda, haga clic en **APIs & auth** (API y autenticación), a continuación desplácese hacia abajo y haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** (Mensajería en la nube de Google para Android). No es necesario habilitar el *Servicio de mensajería en la nube de Google para Chrome*.  También es posible que el nombre cambie a *Servicio de mensajería en la nube de Google* en el futuro. 

   	![][3]

4. En el panel izquierdo, haga clic en **Credentials** (Credenciales) -> **Create New Key** (Crear nueva clave) -> **Server Key** (Clave de servidor) -> **Create** (Crear).

   	![][4]

5. Anote el valor de la **clave de API** del servidor. Lo configurará después en el Centro de notificaciones para que pueda enviar notificaciones de inserción a GCM. 

   	![][5]

##<a id="configure-hub"></a>Configuración del Centro de notificaciones

1. Inicie sesión en el **[Portal de administración de Azure]** y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones** -> **Bus de servicio** -> **Centro de notificaciones** -> **Creación rápida**. Escriba un nombre para su Centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear un nuevo Centro de notificaciones**.

   	![][6]

4. Vaya al Centro de notificaciones que acaba de crear. Haga clic en el espacio de nombres que contiene el centro de notificaciones (normalmente ***nombre del concentrador de notificación*-ns**).   

   	![][7]

5. Haga clic en la ficha **Centros de notificaciones** en la parte superior.

   	![][8]

6. Ahora haga clic en la pestaña **Configurar** en la parte superior. 

   	![][9]

7. En la pestaña **Configurar**, desplácese hacia abajo hasta la **configuración de mensajería en la nube de google**, escriba el valor de la **clave de API** obtenido en la sección anterior y, a continuación, haga clic en **Guardar**.

   	![][10]

8. Haga clic en la pestaña **Panel** en la parte superior y luego haga clic en **Información de conexión** en la parte inferior. 

   	![][11]
 
9. Tome nota de **DefaultListenSharedAccessSignature** (que será necesario en la aplicación Chrome para registrarse con el Centro de notificaciones) y de **DefaultFullSharedAccessSignature** (que necesitará para enviar notificaciones) 

   	![][12]

El Centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión necesarias para una configuración adicional.

##<a id="connect-app"></a>Conexión de la aplicación Chrome al Centro de notificaciones

###Creación de una nueva aplicación Chrome
El siguiente ejemplo se basa en la [muestra de GCM de la aplicación Chrome] y usa la forma recomendada para crear una aplicación Chrome. En las secciones siguientes, nos centraremos en los pasos relacionados del Centro de notificaciones de Azure. Se recomienda que descargue el origen de esta aplicación Chrome del [ejemplo del Centro de notificaciones de la aplicación Chrome].

La aplicación Chrome se crea con JavaScript y puede utilizar su editor de texto preferido para su creación. 

1. A continuación se muestra cuál será el aspecto de esta aplicación Chrome. 

   	![][15]

2. Cree una carpeta y asígnele el nombre de **ChromePushApp**. Puede darle el nombre que desee. 

3. Descargue *la biblioteca cryto-js* desde [crypto-js library] en esta carpeta.  Esta carpeta de biblioteca contendrá dos subcarpetas - *components* y *rollups*. 

4. Cree un archivo manifest.json. Todas las aplicaciones Chrome están respaldadas por un archivo de manifiesto que describe los metadatos de la aplicación y, en particular, los permisos disponibles para la aplicación.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}
	
	Observe el elemento *permisos* que especifica que esta aplicación de Chrome puede recibir notificaciones de inserción de GCM. También debe especificar el URI del Centro de notificaciones de Azure donde la aplicación Chrome realizará una llamada REST para registrarse.
	Utiliza un gcm_128.png del archivo de icono que encontrará en el origen reutilizado de la muestra original de GCM. Puede utilizar cualquier imagen que desee. 
 
5. Cree un archivo llamado background.js con el código siguiente:

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}
		
		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.
		
		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);
		
		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}
		
		var registerWindowCreated = false;
		
		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {
		
		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}
		
		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);
		
		// Set up listeners to trigger the first time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Este es el archivo que muestra el html de la ventana de la aplicación de Chrome (*register.html*) y también define el controlador *messageReceived* para controlar la notificación de inserción entrante. 

6. Cree un archivo llamado *register.html* que define la interfaz de usuario de la aplicación de Chrome.  Observe que este ejemplo usa *CryptoJS v3.1.2*. Si descargó cualquier otra versión, corrija la ruta de acceso de src del script. 

		<html>
		
		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>
		
		<body>
		
		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>
		
		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/> 
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea> 
		
		<br/>
		
		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
		
		<br/>
		<br/>
		
		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea> 
		
		</body>
		
		</html>

7. Cree un archivo llamado *register.js* con el código siguiente. Este archivo especifica el script detrás de *register.html*. Las aplicaciones Chrome no permiten la ejecución insertada, por lo que es necesario crear un script de copia de seguridad independiente para la interfaz de usuario. 

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
		
		window.onload = function() { 
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH; 
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs."); 
		} 
		
		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }
		
		  document.getElementById("console").innerHTML = currentStatus  + status;
		}
		
		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);
		
		  // Prevent register button from being clicked again before the registration finishes
		  document.getElementById("registerWithGCM").disabled = true;
		}
		
		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;
		
		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }
		
		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;
		
		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}
		
		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}
		
		// From http://msdn.microsoft.com/library/dn495627.aspx 
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";
		
		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });
		
		  originalUri = endpoint + hubName;
		}
		
		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration
		
		  // Set expiration in seconds
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // using CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // construct authorization string
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}
		
		function sendNHRegistrationRequest()
		{
		  var registrationPayload = 
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" + 
		      "<content type="application/xml">" + 
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";
		
		  // Update the payload with the registration id obtained earlier
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
		
		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();
		
		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };
		
		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }
		
		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");
		
		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	El script anterior tiene los beneficios siguientes:
	- *window.onload* define los eventos de clic del botón de los dos botones en la interfaz de usuario: uno que se registra con GCM y el otro que utiliza el identificador de registro devuelto después de registrarse con GCM para registrarse con los Centros de notificaciones de Azure.
	- La función *updateLog* define una función de registro simple.
	- *registerWithGCM* es el primer controlador de clic de botón que hace que la llamada de *chrome.gcm.register* a GCM registre esta instancia de la aplicación de Chrome. 
	- *registerCallback* es la función de devolución de llamada que se invoca cuando se devuelve la llamada de registro GCM anterior.
	- *registerWithNH* es el identificador de clic del segundo botón, que se registra con los Centros de notificaciones.  Obtiene los valores *hubName* y *connectionString* que el usuario especificó y llama a la API de REST de registro de los centros de notificación artesanal. 
	- *splitConnectionString* y *generateSaSToken* constituyen la implementación de Javascript de la creación de un token SaS que debe enviarse en todas las llamadas de API de REST. Obtener más información sobre esto aquí:http://msdn.microsoft.com/library/dn495627.aspx 
	- *sendNHRegistrationRequest* es la función que realiza una llamada a REST de HTTP.
	- *registrationPayload* define la carga de xml de registro. Obtenga más información aquí: [Creación de una API de NH REST de registro]. Actualizamos el identificador de registro en ella con lo que hemos recibido de GCM. 
	- *client* es una instancia de *XMLHttpRequest* que se utiliza para realizar la solicitud POST de HTTP.  Tenga en cuenta que el encabezado *Authorization* se actualiza con el sasToken. La finalización correcta de esta llamada registrará esta instancia de la aplicación Chrome con Centros de notificaciones de Azure. 
	

8. Debería ver la siguiente vista de la carpeta al final:
   	![][21]

###Configuración y prueba de la aplicación Chrome

1. Abra el explorador Chrome. Abra las **Extensiones de Chrome** y active **Modo de desarrollador**. 

   	![][16]

2. Haga clic en **Cargar extensión descomprimida** y desplácese hasta la carpeta donde ha creado los archivos. Opcionalmente, también puede utilizar la **Herramienta para desarrolladores de aplicaciones y extensiones de Chrome** que es en sí misma una aplicación Chrome (que tendrá que instalar desde la tienda web de Chrome) y proporciona capacidades de depuración avanzadas para el desarrollo de aplicaciones Chrome. 

   	![][17]

3. Si crea la aplicación Chrome sin errores, verá aparecer la aplicación. 

   	![][18]

4. Escriba el **número de proyecto** que obtuvo anteriormente de la **consola en la nube de Google**, como el identificador del remitente y haga clic en **Registrar con GCM**. Debe ver un mensaje *Registro con GCM realizado correctamente.*

   	![][19]

5. Escriba su **nombre del Centro de notificaciones** y la **DefaultListenSharedAccessSignature** obtenida anteriormente desde el Portal de administración de Azure y haga clic en **Registrarse con el Centro de notificaciones de Azure**. Debe ver un mensaje*Registro del centro de notificaciones correcto* y los detalles de la respuesta de registro que contiene el identificador de registro de los centros de notificaciones de Azure. 

   	![][20]  

##<a name="send"></a>Envío de notificaciones a la aplicación Chrome

En este tutorial enviará notificaciones con la aplicación de consola de .NET aunque puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end mediante la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. 

Para ver un ejemplo de cómo enviar notificaciones desde un back-end de los Servicios móviles de Azure integrado en Centros de notificaciones, consulte **Introducción a las notificaciones de inserción en Servicios móviles** ([back-end .NET](mobile-services-javascript-backend-android-get-started-push.md) | [Back-end de JavaScript](mobile-services-javascript-backend-android-get-started-push.md)).  
Para obtener un ejemplo de cómo enviar notificaciones con API de REST, consulte **Uso de los Centros de notificaciones desde Java/PHP/Python** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego **Proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**.  Esto crea un nuevo proyecto de aplicación de consola.

2. En el menú **Tools**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**. Esto muestra la Consola del Administrador de paquetes.

3. En la ventana de la consola, ejecute el siguiente comando:

        Install-Package WindowsAzure.ServiceBus
    
   	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus de NuGet</a>. 

4. Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.ServiceBus.Notifications;

5. En la clase **Program**, agregue el siguiente método.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Asegúrese de reemplazar el marcador de posición "hub name" por el nombre del Centro de notificaciones que aparece en el portal en la pestaña **Bases de datos centrales de notificaciones**. Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del Centro de notificaciones". 

	>[AZURE.NOTE] Asegúrese de usar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

5. Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Asegúrese de que el explorador Chrome esté abierto. La aplicación Chrome no necesita abrirse para esto. Debería ver el siguiente cuadro emergente de notificación en el escritorio. 

   	![][13]

7. También puede ver todas las notificaciones mediante la ventana de notificaciones de Chrome, a la que se puede acceder desde la barra de tareas (en Windows) cuando Chrome se está ejecutando. 

   	![][14]

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difundirá notificaciones a la aplicación Chrome. 
Obtenga más información sobre los Centros de notificaciones en la [Información general de los Centros de notificaciones].
Para dirigirse a usuarios específicos, consulte el tutorial [Notificación a los usuarios con los Centros de notificaciones de Azure], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Noticias de última hora de los Centros de notificaciones de Azure]. 

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[ejemplo del Centro de notificaciones de la aplicación Chrome]: http://google.com
[Consola de la nube de Google]: http://cloud.google.com/console
[consola en la nube de Google]: http://cloud.google.com/console
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Información general de los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Información general de aplicaciones de Chrome]: https://developer.chrome.com/apps/about_apps
[Ejemplo de GCM de aplicación Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[muestra de GCM de la aplicación Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Aplicaciones Web instalables]: https://developers.google.com/chrome/apps/docs/
[Aplicaciones Chrome en dispositivos móviles]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Creación del registro de la API de NH REST]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[Creación de una API de NH REST de registro]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js library]: http://code.google.com/p/crypto-js/
[GCM con aplicaciones Chrome]: https://developer.chrome.com/apps/cloudMessaging
[Mensajería en la nube de Google para Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificación a los usuarios con los Centros de notificaciones de Azure]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Noticias de última hora de los Centros de notificaciones de Azure]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!--HONumber=49-->