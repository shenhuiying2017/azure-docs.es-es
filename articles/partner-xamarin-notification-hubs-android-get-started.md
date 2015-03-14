<properties 
	pageTitle="Introducción a los Centros de notificaciones para aplicaciones Xamarin.Android" 
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin Android. 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin.Android. 
En este tutorial, creará una aplicación Xamarin.Android que recibirá notificaciones de inserción con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones. El código terminado está disponible en el ejemplo de la [aplicación NotificationHubs][GitHub].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Habilitación del servicio de mensajería en la nube de Google]
2. [Configuración del Centro de notificaciones]
3. [Conexión de la aplicación al Centro de notificaciones]
4. [Ejecución de la aplicación con el emulador]
5. [Envío de notificaciones desde el back-end]

En este tutorial se demuestra el escenario de difusión sencillo con los Centros de notificaciones. Este tutorial requiere lo siguiente:

+ [Xamarin.Android]
+ Cuenta de Google activa
+ [Componente de Servicios móviles de Azure]
+ [Componente del Servicio de mensajería en la nube de Google]

Completar este tutorial es un requisito previo para todos los demás tutoriales de Centros de notificaciones para aplicaciones Xamarin.Android. 

> [AZURE.IMPORTANT] Para completar este tutorial, debe tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F"%20target="_blank).

<h2><a name="register"></a>Habilitación del servicio de mensajería en la nube de Google</h2>

<p></p>

> [AZURE.IMPORTANT] Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302"%20target="_blank). 

1. Navegue al sitio web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a>, inicie sesión con las credenciales de la cuenta de Google y, a continuación, haga clic en **Create project...** (Crear proyecto...).

   	![][1]   
	
	> [AZURE.NOTE] Si ya tiene un proyecto, se le dirigirá a la página **Dashboard** (Panel) después de iniciar sesión. Para crear un nuevo proyecto desde el panel, expanda **API Project** (Proyecto de API), haga clic en **Create...** (Crear...) bajo **Other projects** (Otros proyectos), escriba un nombre de proyecto y haga clic en **Create project** (Crear proyecto).

2. Haga clic en **Overview** (Información general) en la columna de la izquierda y anote el número de proyecto en la sección **Dashboard** (Panel). 

	Más adelante en este tutorial, configurará este valor como la variable PROJECT_ID en el cliente.

3. En la página <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a>, haga clic en **Services** (Servicios) y, a continuación, haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** (Servicio de mensajería en la nube de Google para Android) y acepte los términos del servicio. 

4. Haga clic en **API Access** (Acceso de API) y, a continuación, en **Create new Server key...** (Crear nueva clave de servidor...). 

   	![][2]

5. En **Configure Server Key for API Project** (Configurar clave de servidor para proyecto de API), haga clic en **Create** (Crear).

   	![][3]

6. Anote el valor de **API key** (Clave de API).

   	![][4] 

A continuación, utilizará este valor de clave de API para permitir que su Centro de notificaciones se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

<h2><a name="configure-hub"></a>Configuración del Centro de notificaciones</h2>

1. Inicie sesión en el [Portal de administración de Azure] y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

   	![][7]

3. Escriba un nombre para el Centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear una nueva base de datos central de notificaciones**.

   	![][8]

4. Haga clic en el espacio de nombres que acaba de crear (por lo general ***nombre del Centro de notificaciones*-ns**) y, a continuación, haga clic en **Configurar** en la parte superior.

   	![][9]

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el Centro de notificaciones que acaba de crear.

   	![][10]

6. Haga clic en la pestaña **Configurar** que aparece en la parte superior, escriba el valor **Clave de API** que obtuvo en la sección anterior y, a continuación, haga clic en **Guardar**.

   	![][11]

7. Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Información de conexión**. Anote las dos cadenas de conexión.

   	![][12]

Su Centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones de inserción.

<h2><a name="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones</h2>

### Crear un nuevo proyecto

1. En Xamarin Studio (o Visual Studio), cree un nuevo proyecto Android (File, New, Solution, Android Application).

   	![][13]   
   	![][14]

2. Abra las propiedades del proyecto. Para ello, haga clic con el botón derecho en el nuevo proyecto en la vista Solution (Solución) y elija **Options** (Opciones). Seleccione el elemento **Android Application** (Aplicación Android) en la sección **Build** (Compilación).

   	![][15]

3. Establezca el valor de **Minimum Android version** (Versión mínima de Android) en API level 8.

4. Establezca **Target Android version** (Versión Android de destino) en la versión de API a la que desea dirigirse (debe ser API level 8 o superior).

5. Asegúrese de que la primera letra de **Package name** (Nombre del paquete) sea minúscula.

	> [AZURE.IMPORTANT] La primera letra del nombre del paquete debe ser minúscula. De lo contrario, recibirá errores del manifiesto de aplicación al registrar los elementos **BroadcastReceiver** e **IntentFilter** para las notificaciones de inserción que se muestran a continuación.

### Agregue el Cliente de mensajería en la nube de Google a su proyecto.

El Cliente de mensajería en la nube de Google disponible en el Almacén de componentes Xamarin simplifica el proceso de compatibilidad con las notificaciones de inserción en Xamarin.Android.

1. En la aplicación Xamarin.Android, haga clic con el botón derecho en la carpeta Components (Componentes) y elija **Get More Components...** (Obtener más componentes).

2. Busque el componente **Google Cloud Messaging Client** (Cliente del Servicio de mensajería en la nube de Google).

3. Agregue el componente a la aplicación de Xamarin.Android. Las referencias de ensamblados se agregan de forma automática.

### Agregar Xamarin.NotificationHub a su proyecto

Este ensamblado proporciona una forma sencilla de registrarse con Centros de notificaciones de Azure. Puede descargarse siguiendo las instrucciones de abajo y también se puede encontrar en la [descarga de ejemplo][GitHub].

1. Visite la [página Github Xamarin.NotificationHub] y descargue y compile la carpeta de origen.

2. Cree una carpeta **_external** en la carpeta del proyecto de Xamarin.Android y, a continuación, copie el archivo **ByteSmith.WindowsAzure.Messaging.Android.dll** compilado en esta.

3. Abra su proyecto Xamarin.Android en Xamarin Studio (o Visual Studio).

4. Haga clic con el botón derecho en la carpeta **References** (Referencias) del proyecto y elija **Edit References...** (Editar referencias).

5. Vaya a la pestaña **.Net Assembly** (Ensamblado .Net), desplácese a la carpeta **_external** del proyecto, seleccione el elemento **ByteSmith.WindowsAzure.Messaging.Android.dll** que compilamos anteriormente y haga clic en **Add** (Agregar). Haga clic en OK para cerrar el cuadro de diálogo. 

### Configurar los Centros de notificaciones en su proyecto

1. Cree una clase **Constants.cs** y defina los siguientes valores de constante (sustituyendo los marcadores de posición por valores):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Agregue las siguientes instrucciones using a **MainActivity.cs**:

		using ByteSmith.WindowsAzure.Messaging;
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

	> [AZURE.NOTE] Más adelante, se explicará cómo crear un elemento **BroadcastReceiver** desde el principio. Sin embargo, una alternativa rápida para crear manualmente un elemento **MyBroadcastReceiver.cs** es consultar el archivo **GcmService.cs**, que se encuentra en el proyecto Xamarin.Android de ejemplo en GitHub. Duplicar **GcmService.cs** y cambiar los nombres de las clases también puede ser un buen punto de partida.

5. Agregue las siguientes instrucciones using a **MyBroadcastReceiver.cs** (en referencia al componente y al ensamblado agregados anteriormente):

		using ByteSmith.WindowsAzure.Messaging;
		using Gcm.Client;

5. Agregue las siguientes solicitudes de permisos entre las instrucciones **using** y la declaración **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. En **MyBroadcastReceiver.cs**, cambie la clase **MyBroadcastReceiver** para que coincida con lo siguiente:

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }
        
7. Agregue otra clase en **MyBroadcastReceiver.cs** llamada **PushHandlerService** que se deriva de **PushHandlerServiceBase**. Asegúrese de usar la directiva **Service** en la clase:

    	[Service] //Must use the service tag
    	public class GcmService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public GcmService() : base(Constants.SenderID) 
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
        	}
    	}


8. **GcmServiceBase** implementa los métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** y **OnError()**. Nuestra clase de implementación **GcmService** debe reemplazar estos métodos, los cuales se activarán en respuesta a la interacción con el Centro de notificaciones.

9. Reemplace el método **OnRegistered()** en **PushHandlerService** por el código siguiente:

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

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

	> [AZURE.NOTE] En el código **OnRegistered()** anterior, tenga en cuenta la posibilidad de especificar etiquetas para registrar canales de mensajería específicos.
    
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

11. Add the following **createNotification** method to **PushHandlerService** for notifying users as used above:

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
        
12. Reemplace los miembros abstractos **OnUnRegistered()**, **OnRecoverableError()** y **OnError()** para que el código se compile.


<h2><a name="run-app"></a>Ejecución de la aplicación en el emulador</h2>

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1. En **Tools** (Herramientas), haga clic en **Open Android Emulator Manager** (Abrir Administrador de emulador Android), seleccione su dispositivo y, a continuación, haga clic en **Edit** (Editar).

   	![][18]

2. Seleccione **Google APIs** (APIs de Google) en **Target** (Destino) y, a continuación, haga clic en **OK** (Aceptar).

   	![][19]

3. En la barra de herramientas de la parte superior, haga clic en **Run** (Ejecutar) y, a continuación, seleccione la aplicación. De este modo se arranca el emulador y se ejecuta la aplicación.

4. La aplicación recupera el valor de *registrationId* de GCM y se registra en el Centro de notificaciones.

	> [AZURE.IMPORTANT] Para recibir notificaciones de inserción, debe configurar una cuenta de Google en el dispositivo virtual de Android (en el emulador, navegue a **Settings** (Configuración) y haga clic en **Add Account** [Agregar cuenta]). Además, asegúrese de que el emulador esté conectado a Internet.

<h2><a name="send"></a>Envío de notificaciones desde el back-end</h2>

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial enviará notificaciones con una aplicación de consola .NET y con un servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1. Cree una aplicación de consola nueva de Visual C#: 

   	![][20]

2. Agregue una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus NuGet</a>. En el menú principal de Visual Studio, haga clic en **Herramientas**, **Administrador de paquetes de biblioteca** y finalmente en **Consola del administrador de paquetes**. Posteriormente, en la ventana de la consola, escriba:

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

4. Cuando se cree el trabajo, haga clic en el nombre del trabajo. A continuación,haga clic en la pestaña **Script** en la barra superior.

5. Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por el nombre del Centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Save**.

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

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de los Centros de notificaciones para insertar notificaciones para los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información acerca del uso de los Centros de notificaciones, consulte [Orientación sobre los Centros de notificaciones] y [Procedimientos de los Centros de notificaciones para Android].

<!-- Anchors. -->
[Habilitación del servicio de mensajería en la nube de Google]: #register
[Configuración del Centro de notificaciones]: #configure-hub
[Conexión de la aplicación al Centro de notificaciones]: #connecting-app
[Ejecución de la aplicación con el emulador]: #run-app
[Envío de notificaciones desde el back-end]: #send
[Pasos siguientes]:#next-steps

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
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Información general acerca de los Centros de notificaciones ]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Uso de los Centros de notificaciones para insertar notificaciones para los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
[Página de componentes GCMClient]: http://components.xamarin.com/view/GCMClient
[Página Github Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Componente del Servicio de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/

<!--HONumber=45--> 
