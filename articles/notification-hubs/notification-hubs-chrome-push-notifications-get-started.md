---
title: "Envío de notificaciones push a Chrome con los Azure Notification Hubs | Microsoft Docs"
description: "Aprenda a usar los Centros de notificaciones de Azure para enviar notificaciones push a una aplicación de Chrome."
services: notification-hubs
keywords: "notificaciones push móviles,notificaciones push,notificación push,notificaciones push android"
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 107c001f1b02874adfdc53856f18e6bfcbcb0cf4
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Envío de notificaciones push a Chrome con los Centros de notificaciones de Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

En este tema se muestra cómo usar los Centros de notificaciones de Azure para enviar notificaciones push a una aplicación de Chrome que se mostrará en el contexto del explorador Google Chrome. En este tutorial, creará una aplicación de Chrome que recibirá notificaciones push mediante el [Servicio de mensajería en la nube de Google (GCM)](https://developers.google.com/cloud-messaging/). 

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).
> 
> 

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Habilitación del servicio de mensajería en la nube de Google](#register)
* [Configuración de su Centro de notificaciones](#configure-hub)
* [Conexión de la aplicación Chrome con el centro de notificaciones](#connect-app)
* [Envío de notificaciones push a la aplicación de Chrome](#send)
* [Funcionalidades y funciones adicionales](#next-steps)

> [!NOTE]
> Las notificaciones push de las aplicaciones de Chrome no son notificaciones en el explorador genéricas, son específicas del modelo de extensibilidad del explorador (consulte [Información general sobre las aplicaciones de Chrome] para más información). Además del explorador de escritorio, las aplicaciones de Chrome se ejecutan en dispositivos móviles (iOS y Android) mediante Apache Cordova. Vea [Aplicaciones de Chrome en dispositivos móviles] para obtener más información.
> 
> 

La configuración de GCM y de Centros de notificaciones de Azure es idéntica a la configuración para Android ya que el [Servicio de mensajería en la nube de Google para Chrome] está en desuso y el mismo GCM ahora admite dispositivos Android e instancias de Chrome.

## <a id="register"></a>Habilitación del servicio de mensajería en la nube de Google
1. Diríjase al sitio web de la [consola en la nube de Google] , inicie sesión con las credenciales de su cuenta de Google y luego, haga clic en botón **Crear proyecto**. Proporcione un **nombre de proyecto** adecuado y, luego, haga clic en el botón **Crear**.
   
       ![Google Cloud Console - Create Project][1]
2. Tome nota del **número del proyecto** en la página de **proyectos** para el proyecto que acaba de crear. Úselo como **id. de remitente de GCM** en la aplicación Chrome para registrarse con GCM.
   
       ![Google Cloud Console - Project Number][2]
3. En el panel de la izquierda, haga clic en **API y autenticación**, y luego desplácese hacia abajo y haga clic en el botón de alternancia para habilitar **Servicio de mensajería en la nube de Google para Android**. No es necesario habilitar el **servicio de mensajería en la nube de Google para Chrome**.
   
       ![Google Cloud Console - Server Key][3]
4. En el panel izquierdo, haga clic en **Credenciales** > **Crear nueva clave** > **Clave de servidor** > **Crear**.
   
       ![Google Cloud Console - Credentials][4]
5. Anote el valor de la **clave de API**del servidor. La configurará después en el centro de notificaciones para que pueda enviar notificaciones push a GCM.
   
       ![Google Cloud Console - API Key][5]

## <a id="configure-hub"></a>Configuración de su Centro de notificaciones
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6.   En la hoja **Configuración**, seleccione **Servicios de notificaciones** y, luego, **Google (GCM)**. Escriba la clave de API y guárdela.

&emsp;&emsp;![Centros de notificaciones de Azure: Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>Conexión de la aplicación Chrome con el centro de notificaciones
El centro de notificaciones está configurado para trabajar con GCM y el usuario dispone de las cadenas de conexión necesarias para registrar su aplicación para que reciba y envíe notificaciones push. LK

### <a name="create-a-new-chrome-app"></a>Creación de una nueva aplicación Chrome
El siguiente ejemplo se basa en el [ejemplo de GCM de la aplicación de Chrome] y usa la forma recomendada para crear una aplicación de Chrome. Destacaremos los pasos relacionados con Centros de notificaciones de Azure. 

> [!NOTE]
> Se recomienda que descargue el origen de esta aplicación Chrome del [ejemplo del centro de notificaciones de la aplicación Chrome].
> 
> 

La aplicación Chrome se crea con JavaScript y puede usar su editor de texto preferido para su creación. A continuación se muestra cuál será el aspecto de esta aplicación Chrome.

![Aplicación de Google Chrome][15]

1. Cree una carpeta y asígnele el nombre `ChromePushApp`. Por supuesto, el nombre es arbitrario; si el nombre es diferente, asegúrese de sustituir la ruta de acceso en los segmentos de código necesarios.
2. Descargue la [biblioteca crypto-js] en la carpeta que creó en el segundo paso. Esta carpeta de biblioteca contendrá dos subcarpetas: `components` y `rollups`.
3. Cree un archivo `manifest.json` . Todas las aplicaciones de Chrome están respaldadas por un archivo de manifiesto que contiene los metadatos de la aplicación y, lo más importante, todos los permisos concedidos a la aplicación cuando el usuario la instala.
   
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
   
    Observe el elemento `permissions` , que especifica que esta aplicación de Chrome podrá recibir notificaciones push de GCM. También debe especificar el URI de Centros de notificaciones de Azure donde la aplicación Chrome realizará una llamada REST para registrarse.
    Nuestra aplicación de ejemplo también usa un archivo de icono, `gcm_128.png`, que encontrará en el código fuente reutilizado del ejemplo original de GCM. Puede sustituirlo por cualquier imagen que cumpla los [criterios de icono](https://developer.chrome.com/apps/manifest/icons).
4. Cree un archivo llamado `background.js` con el código siguiente:
   
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
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    Este es el archivo que muestra el HTML de la ventana de la aplicación de Chrome (**register.html**) y también define el controlador **messageReceived** para controlar la notificación push entrante.
5. Cree un archivo llamado `register.html` , que define la interfaz de usuario de la aplicación de Chrome. 
   
   > [!NOTE]
   > En este ejemplo se usa **CryptoJS v3.1.2**. Si descargó otra versión de la biblioteca, asegúrese de reemplazar correctamente la versión en la ruta de acceso `src` .
   > 
   > 
   
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
6. Cree un archivo llamado `register.js` con el código siguiente. Este archivo especifica el script subyacente a `register.html`. Las aplicaciones Chrome no permiten la ejecución insertada, por lo que es necesario crear un script de copia de seguridad independiente para la interfaz de usuario.
   
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
   
          // Prevent register button from being clicked again before the registration finishes.
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
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
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
   
    El script anterior tiene los siguientes parámetros clave:
   
   * **window.onload** define los eventos de clic de botón de los dos botones de la interfaz de usuario. Uno se registra con GCM y el otro usa el identificador de registro que se devolvió después del registro con GCM para registrarse con Centros de notificaciones de Azure.
   * **updateLog** es la función que nos permite controlar las funcionalidades de registro simples.
   * **registerWithGCM** es el primer controlador de clic de botón que hace que la llamada de `chrome.gcm.register` a GCM registre la instancia actual de la aplicación de Chrome.
   * **registerCallback** es la función de devolución de llamada que se invoca cuando se devuelve la llamada de registro de GCM anterior.
   * **registerWithNH** es el segundo controlador de clic de botón, que se registra con los Centros de notificaciones. Obtiene los valores de `hubName` y `connectionString` (que el usuario ha especificado) y elabora la llamada a la API de REST de registro en los Centros de notificaciones.
   * **splitConnectionString** y **generateSaSToken** son los objetos auxiliares que representan la implementación de Javascript del proceso de creación de un token SaS, que debe usarse en todas las llamadas de la API de REST. Para más información, vea [Conceptos comunes](http://msdn.microsoft.com/library/dn495627.aspx).
   * **sendNHRegistrationRequest** es la función que realiza una llamada HTTP REST a los Centros de notificaciones de Azure.
   * **registrationPayload** define la carga XML del registro. Para obtener más información, consulte [Crear registro]. Actualizamos el identificador de registro en ella con lo que hemos recibido de GCM.
   * **client** es una instancia de **XMLHttpRequest** que usamos para realizar la solicitud HTTP POST. Tenga en cuenta que actualizamos el encabezado `Authorization` con `sasToken`. La finalización correcta de esta llamada registrará esta instancia de la aplicación Chrome con Centros de notificaciones de Azure.

La estructura general de las carpetas de este proyecto debe ser similar a esta: ![Aplicación de Google Chrome: estructura de carpetas][21]

### <a name="set-up-and-test-your-chrome-app"></a>Configuración y prueba de la aplicación Chrome
1. Abra el explorador Chrome. Abra las **extensiones de Chrome** y habilite el **modo de desarrollador**.
   
       ![Google Chrome - Enable Developer Mode][16]
2. Haga clic en **Cargar extensión descomprimida** y vaya a la carpeta donde creó los archivos. Opcionalmente, también puede usar la **herramienta para desarrolladores de aplicaciones y extensiones Chrome**. Esta herramienta es una aplicación Chrome en sí misma (se instala desde el almacén web de Chrome) y proporciona las capacidades de depuración avanzadas para el desarrollo de aplicaciones Chrome.
   
       ![Google Chrome - Load Unpacked Extension][17]
3. Si la aplicación Chrome se crea sin errores, verá que aparece.
   
       ![Google Chrome - Chrome App Display][18]
4. Escriba el **número de proyecto** que obtuvo anteriormente de la **consola en la nube de Google**, como el identificador del remitente, y haga clic en **egistrar con GCM**. Debe ver el mensaje **Registration with GCM succeeded**
   
       ![Google Chrome - Chrome App Customization][19]
5. Escriba su **nombre del centro de notificaciones** y el valor de **DefaultListenSharedAccessSignature** obtenido anteriormente del Portal y haga clic en **Registrarse en el Centro de notificaciones de Azure**. Debe ver el mensaje **El registro en el centro de notificaciones se realizó correctamente!** y los detalles de la respuesta de registro, que contienen el identificador de registro de Centros de notificaciones de Azure.
   
       ![Google Chrome - Specify Notification Hub Details][20]  

## <a name="send"></a>Envío de notificaciones a la aplicación Chrome
Para las pruebas, enviaremos notificaciones push de Chrome mediante la aplicación de consola de .NET. 

> [!NOTE]
> Puede enviar notificaciones push con los Centros de notificaciones desde cualquier back-end a través de la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz de REST</a>. Consulte el [portal de documentación](https://azure.microsoft.com/documentation/services/notification-hubs/) para obtener más ejemplos multiplataforma.
> 
> 

1. En Visual Studio, en el menú **Archivole**, seleccione **Nuevo** y, luego, **Proyecto**. En **Visual C#**, haga clic en **Windows** y **Aplicación de consola** y, luego, haga clic en **Aceptar**.  Esto crea un nuevo proyecto de aplicación de consola.
2. En el menú **Herramientas**, haga clic en **Administrador de paquetes de biblioteca** y, luego, en **Consola del Administrador de paquetes**. Esto muestra la Consola del Administrador de paquetes.
3. En la ventana de la consola, ejecute el siguiente comando:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. Abra `Program.cs` y agregue la siguiente instrucción `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. En la clase `Program` , agregue el método siguiente:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > Asegúrese de usar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de conexión de acceso **Listen** no concede permisos para enviar notificaciones push.
   > 
   > 
6. Agregue las siguientes llamadas en el método `Main` :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Asegúrese de que se está ejecutando Chrome y ejecute la aplicación de consola.
8. Debería ver el siguiente cuadro emergente de notificación en el escritorio.
   
       ![Google Chrome - Notification][13]
9. También puede ver todas las notificaciones mediante la ventana de notificaciones de Chrome en la barra de tareas (en Windows) cuando Chrome se está ejecutando.
   
       ![Google Chrome - Notifications List][14]

> [!NOTE]
> No es necesario tener abierta o en ejecución la aplicación Chrome en el explorador (aunque el propio explorador Chrome se debe estar ejecutando). También obtendrá una vista consolidada de todas las notificaciones en la ventana de notificaciones de Chrome.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre Centros de notificaciones en [Introducción a los centros de notificaciones].

Para dirigirse a usuarios específicos, consulte el tutorial [Los Centros de notificaciones de Azure notifican a los usuarios con back-end de .NET] . 

Si desea segmentar los usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora] .

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
[ejemplo del centro de notificaciones de la aplicación Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[consola en la nube de Google]: http://cloud.google.com/console
[Introducción a los centros de notificaciones]: notification-hubs-push-notification-overview.md
[Información general sobre las aplicaciones de Chrome]: https://developer.chrome.com/apps/about_apps
[ejemplo de GCM de la aplicación de Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Aplicaciones de Chrome en dispositivos móviles]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Crear registro]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Servicio de mensajería en la nube de Google para Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Los Centros de notificaciones de Azure notifican a los usuarios con back-end de .NET]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
