<properties
	pageTitle="Introducción a los Centros de notificaciones para aplicaciones Xamarin.Android"
	description="Obtenga información acerca de cómo usar los centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin Android."
	authors="ysxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/14/2015"
	ms.author="yuaxu"/>

# Introducción a los Centros de notificaciones

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Información general

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin.Android. En este tutorial, creará una aplicación Xamarin.Android que recibirá notificaciones de inserción con el servicio de mensajería en la nube de Google \(GCM\). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][GitHub].

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones.

##Requisitos previos

Este tutorial requiere lo siguiente:

+ [Xamarin.Android]
+ Cuenta de Google activa
+ [Componente de Servicios móviles de Azure]
+ [Componente de mensajería de Azure]
+ [Componente del Cliente de mensajería en la nube de Google]

Completar este tutorial es un requisito previo para todos los demás tutoriales de Centros de notificaciones para aplicaciones Xamarin.Android.

> [AZURE.IMPORTANT]Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a name="configure-hub"></a>Configuración del Centro de notificaciones

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a name="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

### Creación de un nuevo proyecto

1. En Xamarin Studio \(o Visual Studio\), haga clic en **File** \(Archivo\) y **New** \(Nuevo\) y, a continuación, haga clic en **Android Application** \(Aplicación de Android\) en el cuadro de diálogo **New Solution** \(Nueva solución\) y finalmente haga clic en **OK** \(Aceptar\).

   ![][14]

	Esto crea un nuevo proyecto de Android.

2. Abra las propiedades del proyecto. Para ello, haga clic con el botón derecho en el nuevo proyecto en la vista Solution \(Solución\) y elija **Options** \(Opciones\). Seleccione el elemento **Android Application** \(Aplicación Android\) en la sección **Build** \(Compilación\).

   ![][15]

3. Establezca el valor de **Minimum Android version** \(Versión mínima de Android\) en API level 8.

4. Establezca **Target Android version** \(Versión Android de destino\) en la versión de API a la que desea dirigirse \(debe ser API level 8 o superior\).

5. Asegúrese de que la primera letra de **Package name** \(Nombre del paquete\) sea minúscula.

	> [AZURE.IMPORTANT]La primera letra del nombre del paquete debe ser minúscula. De lo contrario, recibirá errores del manifiesto de aplicación al registrar los elementos **BroadcastReceiver** e **IntentFilter** para las notificaciones de inserción que se muestran a continuación.

### Incorporación de los componentes necesarios para el proyecto

El Cliente de mensajería en la nube de Google disponible en el Almacén de componentes Xamarin simplifica el proceso de compatibilidad con las notificaciones de inserción en Xamarin.Android.

1. En la aplicación Xamarin.Android, haga clic con el botón derecho en la carpeta Components \(Componentes\) y elija **Get More Components...** \(Obtener más componentes\).

2. Busque el componente **Azure Mobile Services** \(Servicios móviles de Azure\) y agréguelo al proyecto.

3. Busque el componente **Azure Messaging** \(Mensajería de Azure\) y agréguelo al proyecto.

4. Busque el componente**Google Cloud Messaging Client** \(Cliente de mensajería en la nube de Google\) y agréguelo al proyecto.


### Configuración de los Centros de notificaciones en su proyecto

1. Cree una clase **Constants.cs** y defina los siguientes valores de constante \(sustituyendo los marcadores de posición por valores\):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Agregue las siguientes instrucciones using a **MainActivity.cs**:

		using Microsoft.WindowsAzure.MobileServices;
		using Gcm.Client;

3. Cree el método siguiente en la clase **MainActivity**:

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Cree una nueva clase **MyBroadcastReceiver**.

	> [AZURE.NOTE]Más adelante, se explicará cómo crear un elemento **BroadcastReceiver** desde el principio. Sin embargo, una alternativa rápida para crear manualmente un elemento **MyBroadcastReceiver.cs** es consultar el archivo **GcmService.cs**, que se encuentra en el proyecto Xamarin.Android de ejemplo en GitHub. Duplicar **GcmService.cs** y cambiar los nombres de las clases también puede ser un buen punto de partida.

5. Agregue las siguientes instrucciones using a **MyBroadcastReceiver.cs** \(en referencia al componente y al ensamblado agregados anteriormente\):

		using WindowsAzure.Messaging;
		using Gcm.Client;

5. Agregue las siguientes solicitudes de permisos entre las instrucciones **using** y la declaración **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. En **MyBroadcastReceiver.cs**, cambie la clase** MyBroadcastReceiver** para que coincida con lo siguiente:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Agregue otra clase en **MyBroadcastReceiver.cs** llamada **PushHandlerService** que deriva de **GcmServiceBase**. Asegúrese de aplicar el atributo **Service** a la clase:

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
    	{ Log.Info\(MyBroadcastReceiver.TAG, "PushHandlerService\(\) constructor"\); } }


8. **GcmServiceBase** implementa los métodos **OnRegistered\(\)**, **OnUnRegistered\(\)**, **OnMessage\(\)**, **OnRecoverableError\(\)** y **OnError\(\)**. Nuestra clase de implementación **PushHandlerService** debe reemplazar estos métodos, los cuales se activarán en respuesta a la interacción con el centro de notificaciones.

9. Reemplace el método **OnRegistered\(\)** en **PushHandlerService** por el código siguiente:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub (Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = Hub.RegisterNative (registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	> [AZURE.NOTE]En el código **OnRegistered\(\)** anterior, tenga en cuenta la posibilidad de especificar etiquetas para registrar canales de mensajería específicos.

10. Reemplace el método **OnMessage** en **PushHandlerService** por el código siguiente:

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

11. Agregue el siguiente método **createNotification** a **PushHandlerService** para enviar notificaciones a los usuarios como se ha indicado anteriormente:

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. Reemplace los miembros abstractos **OnUnRegistered\(\)**, **OnRecoverableError\(\)** y **OnError\(\)** para que el código se compile.


##<a name="run-app"></a>Ejecución de la aplicación en el emulador

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android \(AVD\) compatible con las API de Google.

	> [AZURE.IMPORTANT] In order to receive push notifications, you must set up a Google account on your Android Virtual Device (in the emulator, navigate to **Settings** and click **Add Account**). Also, make sure that the emulator is connected to the Internet.

1. En **Tools** \(Herramientas\), haga clic en **Open Android Emulator Manager** \(Abrir Administrador de emulador Android\), seleccione su dispositivo y, a continuación, haga clic en **Edit** \(Editar\).

   ![][18]

2. Seleccione **Google APIs** \(API de Google\) en **Target** \(Destino\) y, a continuación, haga clic en **OK** \(Aceptar\).

   ![][19]

3. En la barra de herramientas de la parte superior, haga clic en **Run** \(Ejecutar\) y, a continuación, seleccione la aplicación. Esto inicia el emulador y ejecuta la aplicación.

  La aplicación recupera el valor *registrationId* de GCM y se registra con el Centro de notificaciones.

<a name="send"></a>Envío de notificaciones desde el back-end

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial enviará notificaciones con una aplicación de consola .NET y con un servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1. Cree una aplicación de consola nueva de Visual C\#:

   ![][20]

2. Agregue una referencia al SDK de Bus de servicio de Azure con el paquete de <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">NuGet WindowsAzure.ServiceBus </a>. En el menú principal de Visual Studio, haga clic en **Herramientas**, **Administrador de paquetes de biblioteca** y finalmente en **Consola del administrador de paquetes**. Posteriormente, en la ventana de la consola, escriba:

        Install-Package WindowsAzure.ServiceBus

    y presione Entrar.

2. Abra el archivo Program.cs y agregue la siguiente instrucción using:

        using Microsoft.ServiceBus.Notifications;

3. En la clase `Program`, agregue el método siguiente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. Posteriormente, agregue las siguientes líneas al método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Presione la tecla F5 para ejecutar la aplicación. Debería recibir una notificación del sistema.

   ![][21]

Para enviar una notificación mediante un servicio móvil, siga la [Introducción a los Servicios móviles] y, a continuación:

1. Inicie sesión en el [Portal de administración de Azure] y seleccione su servicio móvil.

2. Seleccione la pestaña **Programador** en la parte superior.

   ![][22]

3. Cree un nuevo trabajo programado, inserte un nombre y seleccione **A petición**.

   ![][23]

4. Cuando se cree el trabajo, haga clic en el nombre del trabajo. A continuación, haga clic en la pestaña **Script** en la barra superior.

5. Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Guardar**.

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

6. Haga clic en **Ejecutar una vez** en la barra inferior. Debería recibir una notificación del sistema.

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Notificación a los usuarios con los Centros de notificaciones de Azure], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información acerca del uso de los Centros de notificaciones, consulte [Orientación sobre los Centros de notificaciones] y [Procedimientos de los Centros de notificaciones para Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

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
[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portal de administración de Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Notificación a los usuarios con los Centros de notificaciones de Azure]: /manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Componente del Cliente de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
[Componente de mensajería de Azure]: http://components.xamarin.com/view/azure-messaging
<!--HONumber=52-->
