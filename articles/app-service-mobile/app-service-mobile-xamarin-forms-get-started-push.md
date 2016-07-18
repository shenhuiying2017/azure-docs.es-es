<properties
	pageTitle="Incorporación de notificaciones push a la aplicación de Xamarin.Forms | Microsoft Azure"
	description="Aprenda a usar los servicios de Azure para enviar notificaciones push multiplataforma a sus aplicaciones de Xamarin.Forms."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/20/2016"
	ms.author="wesmc"/>

# Incorporación de notificaciones push a la aplicación de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Información general

En este tutorial, se muestra cómo usar los servicios de Azure para enviar notificaciones push a aplicaciones de Xamarin.Forms que se ejecutan en las diversas plataformas de dispositivos nativos, Android, iOS y Windows. Las notificaciones push se envían desde un back-end de Aplicaciones móviles de Azure mediante Centros de notificaciones de Azure. Se usan registros de plantilla para poder enviar el mismo mensaje a dispositivos que se ejecutan en todas las plataformas mediante los diversos servicios de notificaciones push (PNS). Para más información acerca del envío de notificaciones push multiplataforma, consulte la documentación de [Centros de notificaciones de Azure](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

Se agregan notificaciones push a todos los proyectos que admite la aplicación de Xamarin.Forms. Cada vez que se inserta un registro en el back-end, se envía una notificación push.

##Requisitos previos

Para obtener el mejor resultado con este tutorial, se recomienda completar primero el tutorial [Creación de una aplicación Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Después de completar este tutorial, tendrá un proyecto de Xamarin.Forms que es una aplicación TodoList multiplataforma.

Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de notificaciones push al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para enviar notificaciones push a dispositivos iOS, se requiere [pertenecer al programa para desarrolladores de Apple](https://developer.apple.com/programs/ios/). Además, debe usar un dispositivo iOS físico porque el [simulador de iOS no admite notificaciones push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="create-hub"></a>Creación de un centro de notificaciones

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Actualización del proyecto de servidor para enviar notificaciones push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Opcional) Configuración y ejecución del proyecto de Android

Complete esta sección para habilitar las notificaciones push para el proyecto Droid de Xamarin.Forms para Android.


###Habilitación del servicio de mensajería en la nube de Google (GCM)

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###Configuración del back-end de aplicación móvil para enviar solicitudes push mediante GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Incorporación de notificaciones push al proyecto de Android

Con el back-end configurado para usar el Servicio de mensajería en la nube de Google (GCM), podemos agregar los componentes y el código al cliente que permite que la aplicación se registre con GCM, registrarnos para notificaciones push con Centros de notificaciones de Azure a través del back-end de aplicación móvil y recibir notificaciones.

1. En el proyecto **Droid**, haga clic con el botón derecho en la carpeta **Componentes**, haga clic en **Get More Components...** (Obtener más componentes), busque el componente **Servicio de mensajería en la nube de Google** y agréguelo al proyecto. Este componente es compatible con las notificaciones push para un proyecto de Xamarin para Android.


2. Abra el archivo de proyecto MainActivity.cs y agregue la siguiente instrucción using al principio del archivo:

		using Gcm.Client;

3. Agregue el código siguiente al método **OnCreate** después de la llamada a **LoadApplication**:

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Agregue un nuevo método auxiliar **CreateAndShowDialog**, como sigue:

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Agregue el siguiente código a la clase **MainActivity**:

		// Create a new instance field for this activity.
		static MainActivity instance = null;

		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

	Esto expone la instancia actual de **MainActivity** para que podamos ejecutarlo en el subproceso de interfaz de usuario principal.

6. Inicialice la variable `instance` al principio del método **OnCreate** como sigue.

		// Set the current instance of MainActivity.
		instance = this;

2. Agregue un nuevo archivo de clase al proyecto **Droid** denominado `GcmService.cs`, y asegúrese de que las siguientes instrucciones **using** están presentes en la parte superior del archivo:

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. Agregue las siguientes solicitudes de permiso al principio del archivo, después de las instrucciones **using** y antes de la declaración **namespace**.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Agregue la siguiente definición de clase al espacio de nombres.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]Reemplace **<PROJECT\_NUMBER>** por el número de proyecto que anotó antes.

11. Reemplace la clase vacía **GcmService** por el código siguiente, que usa el nuevo receptor de difusión:

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Agregue a la clase **GcmService** el código siguiente, que reemplaza el controlador de eventos **OnRegistered** e implementa un método **Register**.

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                	{"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

		Note that this code uses the `messageParam` parameter in the template registration. 

13. Agregue el siguiente código que implementa **OnMessage**:

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

		    var msg = new StringBuilder();

		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }

		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();

		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }

		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }

		    createNotification("Unknown message details", msg.ToString());
		}

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

	Esto controla las notificaciones entrantes y las envía al administrador de notificaciones para que las muestre.

14. Además, **GcmServiceBase** exige que implemente los métodos de controlador **OnUnRegistered** y **OnError**, que se puede hacer de la siguiente manera:

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

Ahora, está listo para probar las notificaciones push en la aplicación que se ejecuta en el emulador o un dispositivo Android.

###Prueba de las notificaciones push en la aplicación de Android

Los dos primeros pasos son necesarios solo al realizar pruebas en un emulador.

1. Asegúrese de que va a implementar o depurar en un dispositivo virtual que tenga las API de Google como destino, como se muestra a continuación en el administrador de dispositivo virtual Android (AVD).

2. Agregue una cuenta de Google a los dispositivos Android haciendo clic en **Aplicaciones** > **Configuración** > **Agregar cuenta** y siga los avisos para usar Agregar una cuenta de Google existente al dispositivo o crear una nueva.

1. En Visual Studio o Xamarin Studio, haga clic con el botón derecho en el proyecto **Droid** y haga clic en **Establecer como proyecto de inicio**.

2. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación en el dispositivo Android o el emulador.

3. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

4. Compruebe que se recibe una notificación cuando se agrega un artículo.


##(Opcional) Configuración y ejecución del proyecto de iOS

Esta sección trata de la ejecución del proyecto de iOS de Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Configuración del Centro de notificaciones para APNS

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/). Haga clic en **Examinar** > **Aplicaciones móviles** > su aplicación móvil > **Configuración** > **Insertar** > **Apple (APNS)** > **Cargar certificado**. Cargue el archivo del certificado de inserción. p12 que exportó anteriormente. No olvide seleccionar **Espacio aislado** si creó un certificado de inserción de desarrollo para desarrollo y pruebas. De lo contrario, elija **Producción**. El servicio está configurado ahora para trabajar con las notificaciones push en iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	A continuación realizará la configuración del proyecto de iOS en Xamarin Studio o Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Incorporación de notificaciones push a la aplicación iOS

1. En el proyecto **iOS**, abra AppDelegate.cs, agregue la siguiente instrucción **using** en la parte superior del archivo de código.

        using Newtonsoft.Json.Linq;

4. En la clase **AppDelegate**, agregue una invalidación para el evento **RegisteredForRemoteNotifications** con el fin de registrar las notificaciones:

        public override void RegisteredForRemoteNotifications(UIApplication application, 
			NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. En **AppDelegate**, agregue también la invalidación siguiente para el controlador de eventos **DidReceivedRemoteNotification**.

        public override void DidReceiveRemoteNotification(UIApplication application, 
			NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

	Este método controla las notificaciones entrantes mientras se ejecuta la aplicación.

2. En la clase **AppDelegate**, agregue el siguiente código al método **FinishedLaunching**:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

	Tras esto, se habilita la compatibilidad con las notificaciones remotas y el registro de inserción de solicitudes.

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

####Prueba de las notificaciones push en su aplicación de iOS

1. Haga clic con el botón derecho en el proyecto de iOS y haga clic en **Establecer como proyecto de inicio**.

2. Haga clic en el botón **Ejecutar** o presione la tecla **F5** en Visual Studio para compilar el proyecto e iniciar la aplicación en un dispositivo iOS. Luego, haga clic en **Aceptar** para aceptar las notificaciones push.

	> [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

3. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).

4. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.


##(Opcional) Configuración y ejecución de proyectos de Windows

Esta sección está dedicada a la ejecución de los proyectos WinApp y WinPhone81 de Xamarin.Forms para dispositivos Windows. Estos pasos también son compatibles con los proyectos de Plataforma universal de Windows (UWP). Puede omitir esta sección si no está trabajando con dispositivos Windows.


####Registro de la aplicación de Windows para notificaciones push con WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configuración del Centro de notificaciones para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Incorporación de notificaciones push a la aplicación de Windows

1. En Visual Studio, abra **App.xaml.cs** en un proyecto de Windows y agregue las siguientes instrucciones **using**.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	Reemplace `<your_TodoItemManager_portable_class_namespace>` por el espacio de nombres del proyecto portable que contiene la clase `TodoItemManager`.
 

2. En App.xaml.cs, agregue el siguiente método **InitNotificationsAsync**:

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	Este método obtiene el canal de notificación push y registra una plantilla para recibir notificaciones de plantilla desde el Centro de notificaciones. Se entregará a este cliente una notificación de plantilla que admita *messageParam*.

3. En App.xaml.cs, actualice la definición del método de controlador de eventos **OnLaunched** con la adición del modificador `async` y agregue la siguiente línea de código al final del método:

        await InitNotificationsAsync();

	Esto garantiza que se creará o actualizará el registro de notificaciones push cada vez que se inicie la aplicación. Es importante hacer esto para garantizar que el canal de inserción WNS siempre esté activo.

4. En el Explorador de soluciones de Visual Studio, abra el archivo **Package.appxmanifest** y establezca **Capacidad de aviso** en **Sí** en **Notificaciones**.

5. Compile la aplicación y compruebe que no haya errores. Ahora debe registrar la aplicación cliente para las notificaciones de plantilla desde el back-end de aplicación móvil. Repita esta sección para cada proyecto de Windows de la solución.


####Prueba de las notificaciones push en su aplicación de Windows

1. En Visual Studio, haga clic con el botón derecho en un proyecto de Windows y haga clic en **Establecer como proyecto de inicio**.

2. Presione el botón **Run** para compilar el proyecto e iniciar la aplicación.

3. En la aplicación, escriba un nombre para un nuevo todoitem y haga clic en el icono del signo más (**+**) para agregarlo.

4. Compruebe que se recibe una notificación cuando se agrega el artículo.

##Pasos siguientes

Más información acerca de las notificaciones push:

* [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags): las etiquetas permiten dirigirse a clientes segmentados con inserciones. Aprenda a agregar etiquetas a la instalación de un dispositivo.

* [Diagnosticar problemas de notificaciones push](../notification-hubs/notification-hubs-push-notification-fixer.md): existen varias razones para que las notificaciones se pierdan o no lleguen a los dispositivos. En este tema se muestra cómo analizar y descubrir la causa principal de los errores de notificación de inserción.

También podría continuar con uno de los siguientes tutoriales:

* [Agregar autenticación a la aplicación](app-service-mobile-xamarin-forms-get-started-users.md): aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Enable offline sync for your app](app-service-mobile-xamarin-forms-get-started-offline-data.md) (Habilitación de la sincronización sin conexión para su aplicación): aprenda a agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0706_2016-->