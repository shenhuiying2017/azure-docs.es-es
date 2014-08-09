<properties linkid="" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="elioda" solutions="" manager="" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

Introducción a los Centros de notificaciones
============================================

[C\# para Tienda Windows](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet "C# para Tienda Windows")[Windows Phone](/es-es/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/es-es/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/es-es/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/es-es/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin.Android. En este tutorial, creará una aplicación Xamarin.Android que recibirá notificaciones de inserción con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs](http://go.microsoft.com/fwlink/p/?LinkId=331329).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Habilitación del servicio de mensajería en la nube de Google](#register)
2.  [Configuración de su Centro de notificaciones](#configure-hub)
3.  [Conexión de su aplicación al Centro de notificaciones](#connecting-app)
4.  [Ejecución de su aplicación con el emulador](#run-app)
5.  [Envío de notificaciones desde su back-end](#send)

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Este tutorial requiere lo siguiente:

-   Xamarin.Android
-   Cuenta de Google activa

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones Android.

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F).

<a name="register"></a>Habilitación del servicio de mensajería en la nube de GoogleHabilitación del servicio de mensajería en la nube de Google
------------------------------------------------------------------------------------------------------------------------

**Nota:**

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Diríjase al sitio web [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **Create project...**.

  ![][1]   

    **Nota:**

    Si ya tiene un proyecto existente, se le dirigirá a la página **Deshboard** después de iniciar sesión. Para crear un nuevo proyecto desde el panel, expanda **API Project**, haga clic en **Create...** bajo **Other projects** y, a continuación, escriba un nombre de proyecto y haga clic en **Create project**.

2.  Haga clic en **Overview** en la columna de la izquierda y anote el número de proyecto en la sección **Dashboard**.

    Más adelante en este tutorial, configurará este valor como la variable PROJECT\_ID en el cliente.

3.  En la página [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), haga clic en **Services** y, a continuación, haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** y aceptar los términos del servicio.

4.  Haga clic en **API Access** y, a continuación, en **Create new Server key...**

  ![][2]

5.  En **Configure Server Key for API Project**, haga clic en **Create**.

  ![][3]

6.  Anote el valor de **API key**.

  ![][4] 

A continuación, utilizará este valor de clave de API para permitir que su centro de notificaciones se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

<a name="configure-hub">Configuración de su Centro de notificacionesConfiguración de su Centro de notificaciones
----------------------------------------------------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y, a continuación, haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

  ![][7]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

  ![][8]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en **Configure** en la parte superior.

  ![][9]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

  ![][10]

6.  Haga clic en la pestaña **Configure**, escriba el valor de **API Key** obtenido de GCM en la sección anterior y, a continuación, haga clic en **Save**.

  ![][11]

7.  Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Connection Information**. Anote las dos cadenas de conexión.

  ![][12]

Su centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones de inserción.

<a name="connecting-app"></a>Conexión de su aplicaciónConexión de su aplicación al Centro de notificaciones
------------------------------------------------------------------------------

### Crear un nuevo proyecto

1.  En Xamarin Studio (o Visual Studio), cree un nuevo proyecto Android (File, New, Solution, Android Application).

  ![][13]   
  ![][14]

2.  Abra las propiedades del proyecto; para ello, haga clic con el botón secundario en su nuevo proyecto, en la vista Solution, y seleccione **Options**. Seleccione el elemento **Android Application** en la sección **Build**.

  ![][15]

3.  Configure **Minimum Android version** como API Level 8.

4.  Configure **Target Android version** como la versión de API a la que se gustaría dirigirse (debe ser API Level 8 o superior).

5.  Asegúrese de que la primera letra de su **nombre del paquete** sea minúscula.

    **Nota:**

    La primera letra del nombre del paquete debe ser minúscula. De lo contrario, recibirá errores de manifiesto de la aplicación al registrar **BroadcastReceiver** y **IntentFilters** para las notificaciones de inserción siguientes.

### Incorporación de PushSharp a su proyecto

1.  Ahora debemos agregar **PushSharp** como referencia en nuestro proyecto. Para ello, debemos compilar la última versión de PushSharp y agregar la DLL compilada como referencia a nuestro proyecto Xamarin.Android.

2.  Visite la [página Github PushSharp](https://github.com/Redth/PushSharp) (en inglés) y descargue la última versión. Una vez extraída la recopilación de archivos, diríjase a la siguiente carpeta del proyecto de ejemplo:

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. y abra el siguiente archivo de proyecto en Xamarin Studio (o Visual Studio):

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Compile el ejemplo cliente PushSharp MonoForAndroid en modo **Release**.

4.  Cree una carpeta **\_external** en su carpeta del proyecto Xamarin.Android.

5.  Copie el siguiente archivo del ejemplo del cliente PushSharp MonoForAndroid en la carpeta **\_external** recién creada en su carpeta del proyecto Xamarin.Android:

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Abra su proyecto Xamarin.Android en Xamarin Studio (o Visual Studio).

7.  Haga clic con el botón secundario en la carpeta **References** del proyecto y seleccione **Edit References...**

8.  Vaya a la pestaña **.Net Assembly**, diríjase a la carpeta **\_external** de su proyecto, seleccione el elemento **PushSharp.Client.MonoForAndroid.dll** que compilamos anteriormente y haga clic en **Add**. Haga clic en OK para cerrar el cuadro de diálogo.

### Agregar Xamarin.NotificationHub a su proyecto

1.  Ahora debemos agregar bibliotecas compatibles con la clase NotificationHub. Visite la [página Github Xamarin.NotificationHub](https://github.com/SaschaDittmann/Xamarin.NotificationHub) (en inglés), y descargue y compile la carpeta de origen.

2.  Copie la biblioteca compilada **ByteSmith.WindowsAzure.Messaging.Android.dll** en su carpeta **\_external**, en la carpeta de su proyecto Xamarin.Android.

3.  Abra su proyecto Xamarin.Android en Xamarin Studio (o Visual Studio).

4.  Haga clic con el botón secundario en la carpeta **References** del proyecto y seleccione **Edit References...**

5.  Vaya a la pestaña **.Net Assembly**, diríjase a la carpeta **\_external** de su proyecto, seleccione el elemento **ByteSmith.WindowsAzure.Messaging.Android.dll** que compilamos anteriormente y haga clic en **Add**. Haga clic en OK para cerrar el cuadro de diálogo.

### Configurar los Centros de notificaciones en su proyecto

1.  Cree una clase **Constants.cs** y defina los siguientes valores constantes (sustituyendo los marcadores por valores):

         public const string SenderID = "<GoogleProjectNumber>"; // Número de proyecto Google API

         // Cadena de conexión y ruta del centro específicos de las aplicaciones de Azure
         public const string ConnectionString = "<Azure connection string>"";
         public const string NotificationHubPath = "<hub path>";

2.  Agregue las siguientes instrucciones using a **MainActivity.cs**:

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

3.  Cree el método siguiente en la clase **MainActivity**:

         private void RegisterWithGCM()
         {
             // Comprobar que todo se ha configurado correctamente
             PushClient.CheckDevice(this);
             PushClient.CheckManifest(this);

             // Registrar notificaciones de inserción
             System.Diagnostics.Debug.WriteLine("Registering...");
             PushClient.Register(this, Constants.SenderID);
         }

4.  Cree una nueva clase **MyBroadcastReceiver**.

    **Nota:**

    Le guiaremos por los pasos para crear un elemento **BroadcastReceiver** partiendo de cero. Sin embargo, una alternativa rápida para crear manualmente el elemento **MyBroadcastReceiver.cs** siguiente es consultar el archivo **PushService.cs** que se encuentra en el proyecto Xamarin.Android del ejemplo PushSharp en GitHub. También se puede comenzar duplicando el elemento **PushService.cs** y cambiando los nombres de las clases.

5.  Agregue las siguientes instrucciones using a **MyBroadcastReceiver.cs**:

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

6.  Agregue las siguientes solicitudes de permisos entre las instrucciones **using** y la declaración **namespace**:

         [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

         //GET_ACCOUNTS solo es necesario para las versiones de Android 4.0.3 e inferiores
         [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
         [assembly: UsesPermission(Name = "android.permission.INTERNET")]
         [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  En **MyBroadcastReceiver.cs**, cambie la clase **MyBroadcastReceiver** para que coincida con lo siguiente:

         [BroadcastReceiver(Permission=GCMConstants.PERMISSION\_GCM\_INTENTS)]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
         public class MyBroadcastReceiver : PushHandlerBroadcastReceiverBase<PushHandlerService>
         {
             public static string[] SENDER_IDS = new string[] { Constants.SenderID };

             public const string TAG = "MyBroadcastReceiver-GCM";
         }

8.  Agregue otra clase en **MyBroadcastReceiver.cs** llamada **PushHandlerService**, que se obtiene de **PushHandlerServiceBase**. Asegúrese de usar la directiva **Service** en la clase:

        [Service] // Se debe utilizar la clase pública de etiqueta de servicio 
        PushHandlerService: PushHandlerServiceBase 
		{
	        public static string RegistrationID { get; private set; }
	        private NotificationHub Hub { get; set; }

	        public PushHandlerService() : base(Constants.SenderID) 
            {
	            Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); 
	        }
		}

9.  **PushHandlerServiceBase** implementa los métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** y **OnError()**. Nuestra clase de implementación **PushHandlerService** debe reemplazar estos métodos, los cuáles se activarán en respuesta a la interacción con el centro de notificaciones.

10. Reemplace el método **OnRegistered()** en **PushHandlerService** por el siguiente código:

         protected override async void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;

             createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

             Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
             try
             {
                 await Hub.UnregisterAllAsync(registrationId);
             }
             catch (Exception ex)
             {
                 Debug.WriteLine(ex.Message);
                 Debugger.Break();
             }

             var tags = new List<string>() { "falcons" }; // create tags if you want

             try
             {
                 var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
             }
             catch (Exception ex)
             {
                 Debug.WriteLine(ex.Message); 
                 Debugger.Break();
             }
         }

    **Nota:**

    En el código **OnRegistered()** anterior, debe tener en cuenta la posibilidad de especificar etiquetas para registrar canales de mensajería específicos.

11. Reemplace el método **OnMessage** en **PushHandlerService** por el siguiente código:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

12. Agregue el siguiente método **createNotification** a **PushHandlerService** para enviar notificaciones a los usuarios como se ha indicado anteriormente:

        void createNotification(string title, string desc)
        {
            //Crear notificaciones
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Crear un intento para mostrar ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Crear la notificación
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //El comando Auto Cancel borrará la notificación cuando la pulse el usuario
            notification.Flags = NotificationFlags.AutoCancel;

            //Configurar la información de la notificación
            //Utilizamos la intención pendiente, pasando nuestro uiIntent, al cual se llamará
            //al pulsar la notificación.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Mostrar la notificación
            notificationManager.Notify(1, notification);
        }

13. Reemplace los miembros abstractos **OnUnRegistered()**, **OnRecoverableError()** y **OnError()** para que su código se compile.

<a name="run-app"></a>Ejecución de su aplicaciónEjecución de su aplicación en el emulador
-------------------------------------------------------------------

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1.  En **Tools**, haga clic en **Open Android Emulator Manager**, seleccione su dispositivo y, a continuación, haga clic en **Edit**.

  ![][18]

2.  Seleccione **Google APIs** en **Target** y, a continuación, haga clic en **OK**.

  ![][19]

3.  En la barra de herramientas de la parte superior, haga clic en **Run** y, a continuación, seleccione su aplicación. De este modo se arranca el emulador y se ejecuta la aplicación.

4.  La aplicación recupera el valor de *registrationId* de GCM y se registra con el Centro de notificaciones.

    **Nota:**

    Para recibir notificaciones de inserción, debe configurar una cuenta de Google en su dispositivo virtual Android (en el emulador, diríjase a **Settings** y haga clic en **Add Account**). Además, asegúrese de que el emulador esté conectado a Internet.

<a name="send"></a>Envío de notificacionesEnvío de notificaciones desde su back-end
----------------------------------------------------------------

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la [interfaz REST](http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx). En este tutorial enviará notificaciones con una aplicación de consola .NET y con un servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1.  Cree una aplicación de consola nueva de Visual C\#:

  ![][20]

2.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet](http://nuget.org/packages/WindowsAzure.ServiceBus/). En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba:

         Install-Package WindowsAzure.ServiceBus

    y presione Entrar.

3.  Abra el archivo Program.cs y agregue la siguiente instrucción using:

         using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método.

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection data-morhtml="true" string with full access>", "<hub data-morhtml="true" name>");
			await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
		}
        

5.  Posteriormente, agregue las siguientes líneas al método Main:

          SendNotificationAsync();
          Console.ReadLine();

6.  Presione la tecla F5 para ejecutar la aplicación. Debería recibir una notificación del sistema.

  ![][21]

Para enviar una notificación usando un servicio móvil, siga la [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service) y, a continuación:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y seleccione su servicio móvil.

2.  Seleccione la pestaña **Programador** en la parte superior.

  ![][22]

3.  Cree un nuevo trabajo programado, inserte un nombre y, a continuación, seleccione **On demand**.

  ![][23]

4.  Cuando se cree el trabajo, haga clic en el nombre del trabajo. Luego, haga clic en la pestaña **Script** de la barra superior.

5.  Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Save**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
		  function (error) 
		  {
            if (!error) {
                console.warn("Notification successful");
             }
             else
             {
               console.warn("Notification failed" + error);
             }
           }
         );

6.  Haga clic en **Run Once** en la barra inferior. Debería recibir una notificación del sistema.

<a name="next-steps"> </a>Pasos siguientes
----------------

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios](/es-es/manage/services/notification-hubs/notify-users-aspnet), mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora](/es-es/manage/services/notification-hubs/breaking-news-dotnet). Para obtener más información acerca del uso de los Centros de notificaciones, consulte la [orientación sobre los Centros de notificaciones](http://msdn.microsoft.com/es-es/library/jj927170.aspx) y los [procedimientos de los Centros de notificaciones para Android](http://msdn.microsoft.com/es-es/library/dn282661.aspx) (en inglés).

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/es-es/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
[PushSharp Github page]: https://github.com/Redth/PushSharp
[Xamarin.NotificationHub Github page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
