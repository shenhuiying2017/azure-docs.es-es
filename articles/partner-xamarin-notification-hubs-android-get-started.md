<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

En este tema se muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Xamarin.Android.
En este tutorial, creará una aplicación Xamarin.Android que recibirá notificaciones de inserción con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][aplicación NotificationHubs].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Habilitación del servicio de mensajería en la nube de Google][Habilitación del servicio de mensajería en la nube de Google]
2.  [Configuración de su Centro de notificaciones][Configuración de su Centro de notificaciones]
3.  [Conexión de su aplicación al Centro de notificaciones][Conexión de su aplicación al Centro de notificaciones]
4.  [Ejecución de su aplicación con el emulador][Ejecución de su aplicación con el emulador]
5.  [Envío de notificaciones desde su back-end][Envío de notificaciones desde su back-end]

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Este tutorial requiere lo siguiente:

-   [Xamarin.Android][1]
-   Cuenta de Google activa
-   [Componente de Servicios móviles de Azure][Componente de Servicios móviles de Azure]
-   [Componente del Servicio de mensajería en la nube de Google][Componente del Servicio de mensajería en la nube de Google]

Completar este tutorial es un requisito previo para todos los demás tutoriales de centros de notificaciones para aplicaciones Xamarin.Android.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <a name="register"></a><span class="short-header">Habilitación del servicio de mensajería en la nube de Google</span>Habilitación del servicio de mensajería en la nube de Google

<div class="dev-callout"><b>Nota:</b>
<p>Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una direcci&oacute;n de correo electr&oacute;nico verificada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Diríjase al sitio web [Google apis][Google apis], inicie sesión con las credenciales de su cuenta de Google y, a continuación, haga clic en **Create project...**.

    ![][]

    <div class="dev-callout"><b>Nota:</b>
<p>Si ya tiene un proyecto existente, se le dirigir&aacute; a la p&aacute;gina <strong>Deshboard</strong> despu&eacute;s de iniciar sesi&oacute;n. Para crear un nuevo proyecto desde el panel, expanda <strong>API Project</strong>, haga clic en <strong>Create...</strong> bajo <strong>Other projects</strong> y, a continuaci&oacute;n, escriba un nombre de proyecto y haga clic en <strong>Create project</strong>.</p>
</div>

2.  Haga clic en **Overview** en la columna de la izquierda y anote el número de proyecto en la sección **Dashboard**.

    Más adelante en este tutorial, configurará este valor como la variable PROJECT\_ID en el cliente.

3.  En la página [Google apis][Google apis], haga clic en **Services** y, a continuación, haga clic en el botón de alternancia para habilitar **Google Cloud Messaging for Android** y aceptar los términos del servicio.

4.  Haga clic en **API Access** y, a continuación, en **Create new Server key...**

    ![][2]

5.  En **Configure Server Key for API Project**, haga clic en **Create**.

    ![][3]

6.  Anote el valor de **API key**.

    ![][4]

A continuación, utilizará este valor de clave de API para permitir que su centro de notificaciones se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

## <a name="configure-hub"></a><span class="short-header">Configuración de su Centro de notificaciones</span>Configuración de su Centro de notificaciones

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y, a continuación, haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

    ![][5]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

    ![][6]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en **Configure** en la parte superior.

    ![][7]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

    ![][8]

6.  Haga clic en la pestaña **Configure**, escriba el valor de **API Key** obtenido de GCM en la sección anterior y, a continuación, haga clic en **Save**.

    ![][9]

7.  Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Connection Information**. Anote las dos cadenas de conexión.

    ![][10]

Su centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones de inserción.

## <a name="connecting-app"></a><span class="short-header">Conexión de su aplicación</span>Conexión de su aplicación al Centro de notificaciones

### Crear un nuevo proyecto

1.  En Xamarin Studio (o Visual Studio), cree un nuevo proyecto Android (File, New, Solution, Android Application).

    ![][11]

    ![][12]

2.  Abra las propiedades del proyecto; para ello, haga clic con el botón secundario en su nuevo proyecto, en la vista Solution, y seleccione **Options**. Seleccione el elemento **Android Application** en la sección **Build**.

    ![][13]

3.  Configure **Minimum Android version** como API Level 8.

4.  Configure **Target Android version** como la versión de API a la que se gustaría dirigirse (debe ser API Level 8 o superior).

5.  Asegúrese de que la primera letra de su **nombre del paquete** sea minúscula.

    <div class="dev-callout"><b>Nota:</b>
<p>La primera letra del nombre del paquete debe ser min&uacute;scula. De lo contrario, recibir&aacute; errores de manifiesto de la aplicaci&oacute;n al registrar **BroadcastReceiver** y **IntentFilters** para las notificaciones de inserci&oacute;n siguientes.</p>
</div>

### Agregue el Cliente de mensajería en la nube de Google a su proyecto.

El Cliente de mensajería en la nube de Google disponible en el Almacén de componentes Xamarin simplifica el proceso de compatibilidad con las notificaciones de inserción en Xamarin.Android.

1.  En la aplicación Xamarin.Android, haga clic con el botón secundario en la carpeta Components y elija **Get More Components**.

2.  Busque el componente **Google Cloud Messaging Client**.

3.  Agregue el componente a la aplicación de Xamarin.Android. Las referencias de ensamblados se agregan de forma automática.

### Agregar Xamarin.NotificationHub a su proyecto

Este ensamblado proporciona una forma sencilla de registrarse con Centros de notificaciones de Azure. Puede descargarse siguiendo las instrucciones de abajo y también se puede encontrar en la [descarga de ejemplo][aplicación NotificationHubs].

1.  Visite la [página Github Xamarin.NotificationHub][página Github Xamarin.NotificationHub] (en inglés), y descargue y compile la carpeta de origen.

2.  Cree una carpeta \*\*\_external\*\* en la carpeta de su proyecto de Xamarin.Android y, a continuación, copie el archivo **ByteSmith.WindowsAzure.Messaging.Android.dll** compilado allí.

3.  Abra su proyecto Xamarin.Android en Xamarin Studio (o Visual Studio).

4.  Haga clic con el botón secundario en la carpeta **References** del proyecto y seleccione **Edit References...**

5.  Vaya a la pestaña **.Net Assembly**, diríjase a la carpeta \*\*\_external\*\* de su proyecto, seleccione el elemento **ByteSmith.WindowsAzure.Messaging.Android.dll** que compilamos anteriormente y haga clic en **Add**. Haga clic en OK para cerrar el cuadro de diálogo.

### Configurar los Centros de notificaciones en su proyecto

1.  Cree una clase **Constants.cs** y defina los siguientes valores constantes (sustituyendo los marcadores por valores):

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  Agregue las siguientes instrucciones using a **MainActivity.cs**:

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  Cree el método siguiente en la clase **MainActivity**:

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  Cree una nueva clase **MyBroadcastReceiver**.

    <div class="dev-callout"><b>Nota:</b>
<p>Se explicar&aacute; c&oacute;mo crear un **BroadcastReceiver** desde cero m&aacute;s adelante. Sin embargo, una alternativa r&aacute;pida para crear manualmente un **MyBroadcastReceiver.cs** es consultar el archivo **GcmService.cs** que se encuentra en el proyecto Xamarin.Android de ejemplo en GitHub. Tambi&eacute;n se puede comenzar duplicando **GcmService.cs** y cambiando los nombres de las clases.</p>
</div>

5.  Agregue las siguientes instrucciones using a **MyBroadcastReceiver.cs** (en referencia al componente y al ensamblado que se agregaron antes):

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  Agregue las siguientes solicitudes de permisos entre las instrucciones **using** y la declaración **namespace**:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  En **MyBroadcastReceiver.cs**, cambie la clase **MyBroadcastReceiver** para que coincida con lo siguiente:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  Agregue otra clase en **MyBroadcastReceiver.cs** llamada **PushHandlerService**, que se obtiene de **PushHandlerServiceBase**. Asegúrese de usar la directiva **Service** en la clase:

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

9.  **GcmServiceBase** implementa los métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** y **OnError()**. Nuestra clase de implementación **GcmService** debe reemplazar estos métodos, los cuales se activarán en respuesta a la interacción con el centro de notificaciones.

10. Reemplace el método **OnRegistered()** en **PushHandlerService** por el siguiente código:

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

    <div class="dev-callout"><b>Nota:</b>
<p>En el c&oacute;digo **OnRegistered()** anterior, debe tener en cuenta la posibilidad de especificar etiquetas para registrar canales de mensajer&iacute;a espec&iacute;ficos.</p>
</div>

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

13. Reemplace los miembros abstractos **OnUnRegistered()**, **OnRecoverableError()** y **OnError()** para que su código se compile.

## <a name="run-app"></a><span class="short-header">Ejecución de su aplicación</span>Ejecución de su aplicación en el emulador

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1.  En **Tools**, haga clic en **Open Android Emulator Manager**, seleccione su dispositivo y, a continuación, haga clic en **Edit**.

    ![][14]

2.  Seleccione **Google APIs** en **Target** y, a continuación, haga clic en **OK**.

    ![][15]

3.  En la barra de herramientas de la parte superior, haga clic en **Run** y, a continuación, seleccione su aplicación. De este modo se arranca el emulador y se ejecuta la aplicación.

4.  La aplicación recupera el valor de *registrationId* de GCM y se registra con el Centro de notificaciones.

    <div class="dev-callout"><b>Nota:</b>
<p>Para recibir notificaciones de inserci&oacute;n, debe configurar una cuenta de Google en su dispositivo virtual Android (en el emulador, dir&iacute;jase a **Settings** y haga clic en **Add Account**). Adem&aacute;s, aseg&uacute;rese de que el emulador est&eacute; conectado a Internet.</p>
</div>

## <a name="send"></a><span class="short-header">Envío de notificaciones</span>Envío de notificaciones desde su back-end

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz REST][interfaz REST]. En este tutorial enviará notificaciones con una aplicación de consola .NET y con un servicio móvil mediante un script de Node.

Para enviar notificaciones mediante una aplicación .NET:

1.  Cree una aplicación de consola nueva de Visual C#:

    ![][16]

2.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet][paquete WindowsAzure.ServiceBus NuGet]. En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba:

        Install-Package WindowsAzure.ServiceBus

    y presione Entrar.

3.  Abra el archivo Program.cs y agregue la siguiente instrucción using:

        using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  Posteriormente, agregue las siguientes líneas al método Main:

         SendNotificationAsync();
         Console.ReadLine();

6.  Presione la tecla F5 para ejecutar la aplicación. Debería recibir una notificación del sistema.

    ![][17]

Para enviar una notificación usando un servicio móvil, siga la [Introducción a los Servicios móviles][Introducción a los Servicios móviles] y, a continuación:

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y seleccione su servicio móvil.

2.  Seleccione la pestaña **Programador** en la parte superior.

    ![][18]

3.  Cree un nuevo trabajo programado, inserte un nombre y, a continuación, seleccione **On demand**.

    ![][19]

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

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios][Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora]. Para obtener más información acerca del uso de los Centros de notificaciones, consulte la [orientación sobre los Centros de notificaciones][orientación sobre los Centros de notificaciones] y los [procedimientos de los Centros de notificaciones para Android][procedimientos de los Centros de notificaciones para Android] (en inglés).

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Windows Universal]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /es-es/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /es-es/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /es-es/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /es-es/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [aplicación NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Habilitación del servicio de mensajería en la nube de Google]: #register
  [Configuración de su Centro de notificaciones]: #configure-hub
  [Conexión de su aplicación al Centro de notificaciones]: #connecting-app
  [Ejecución de su aplicación con el emulador]: #run-app
  [Envío de notificaciones desde su back-end]: #send
  [1]: http://xamarin.com/download/
  [Componente de Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [Componente del Servicio de mensajería en la nube de Google]: http://components.xamarin.com/view/GCMClient/
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [página Github Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [interfaz REST]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [paquete WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  [Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
  [orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
  [procedimientos de los Centros de notificaciones para Android]: http://msdn.microsoft.com/es-es/library/dn282661.aspx
