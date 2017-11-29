---
title: "Adición de notificaciones push a una aplicación de Xamarin.Forms | Microsoft Docs"
description: Aprenda a usar los servicios de Azure para enviar notificaciones push multiplataforma a sus aplicaciones de Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Incorporación de notificaciones push a la aplicación de Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregará las notificaciones de inserción a todos los proyectos resultantes del [inicio rápido de Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Esto significa que se envía una notificación de inserción a todos los clientes multiplataforma cada vez que se inserta un registro.

Si no usa el proyecto de servidor de inicio rápido descargado, necesitará el paquete de extensión de la notificación de inserción. Para más información, vea [Trabajar con el SDK de servidor de back-end de .NET para Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Requisitos previos
Para iOS, necesitará una [suscripción de Apple Developer Program](https://developer.apple.com/programs/ios/) y un dispositivo de iOS físico. El [simulador de iOS no admite notificaciones de inserción](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurar un Centro de notificaciones
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Actualización del proyecto de servidor para enviar notificaciones push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configuración y ejecución del proyecto de Android (opcional)
Complete esta sección para habilitar las notificaciones push para el proyecto Droid de Xamarin.Forms para Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Habilitar la mensajería en la nube Firebase (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configuración del back-end de Mobile Apps para enviar solicitudes push mediante FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Incorporación de notificaciones push al proyecto de Android
Con el back-end configurado con FCM, puede agregar componentes y códigos al cliente para registrar con FCM. También puede registrarse para notificaciones de inserción con Azure Notification Hubs a través del back-end de Mobile Apps y recibir notificaciones.

1. En el proyecto **Droid**, haga clic con el botón derecho en la carpeta **Components** (Componentes) y haga clic en **Get More Components...** (Obtener más componentes...). Después, busque el componente **Google Cloud Messaging Client** (Cliente de Google Cloud Messaging) y agréguelo al proyecto. Este componente es compatible con las notificaciones push para un proyecto de Xamarin para Android.
2. Abra el archivo de proyecto MainActivity.cs y agregue la siguiente instrucción al principio del archivo:

        using Gcm.Client;
3. Agregue el código siguiente al método **OnCreate** después de la llamada a **LoadApplication**:

        try
        {
            // Check to ensure everything's set up right
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
4. Agregue un nuevo método auxiliar **CreateAndShowDialog** , como sigue:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. Agregue el siguiente código a la clase **MainActivity** :

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

    Esto expone la instancia actual de **MainActivity** para que podamos realizar la ejecución en el subproceso de IU principal.
6. Inicialice la variable `instance` al principio del método **OnCreate** de la siguiente manera.

        // Set the current instance of MainActivity.
        instance = this;
7. Agregue un nuevo archivo de clase al proyecto **Droid**denominado `GcmService.cs`, y asegúrese de que las siguientes instrucciones **using** están presentes en la parte superior del archivo:

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
8. Agregue las siguientes solicitudes de permiso al principio del archivo, después de las instrucciones **using** y antes de la declaración **namespace**.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Agregue la siguiente definición de clase al espacio de nombres.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Reemplace **<PROJECT_NUMBER>** por el número de proyecto que anotó antes.    
   >
   >
10. Reemplace la clase vacía **GcmService** por el código siguiente, que usa el nuevo receptor de difusión:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Agregue el siguiente código a la clase **GcmService**. Esto invalida el controlador de eventos **OnRegistered** e implementa un método **registrar**.

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
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

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

    Tenga en cuenta que este código usa el parámetro `messageParam` en el registro de plantilla.
12. Agregue el siguiente código que implementa **OnMessage**:

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
13. Además, **GcmServiceBase** exige que implemente los métodos de controlador **OnUnRegistered** y **OnError**, que se puede hacer de la siguiente manera:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Ahora, está listo para probar las notificaciones push en la aplicación que se ejecuta en el emulador o un dispositivo Android.

### <a name="test-push-notifications-in-your-android-app"></a>Prueba de las notificaciones push en la aplicación de Android
Los dos primeros pasos son necesarios solo cuando se realizan pruebas en un emulador.

1. Asegúrese de que va a implementar o depurar en un dispositivo virtual que tenga las API de Google como destino, como se muestra a continuación en el administrador de dispositivo virtual Android.
2. Agregue una cuenta de Google al dispositivo Android haciendo clic en **Aplicaciones** > **Configuración** > **Agregar cuenta**. Luego siga las indicaciones para agregar una cuenta de Google existente al dispositivo o para crear una nueva.
3. En Visual Studio o Xamarin Studio, haga clic con el botón derecho en el proyecto **Droid** y haga clic en **Establecer como proyecto de inicio**.
4. Haga clic en **Ejecutar** para compilar el proyecto e iniciar la aplicación en el dispositivo Android o el emulador.
5. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).
6. Compruebe que se recibe una notificación cuando se agrega un artículo.

## <a name="configure-and-run-the-ios-project-optional"></a>Configuración y ejecución del proyecto de iOS (opcional)
Esta sección trata de la ejecución del proyecto de iOS de Xamarin para dispositivos iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configuración del Centro de notificaciones para APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

A continuación, realizará la configuración del proyecto de iOS en Xamarin Studio o Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Incorporación de notificaciones push a la aplicación iOS
1. En el proyecto **iOS**, abra AppDelegate.cs y agregue la siguiente instrucción en la parte superior del archivo de código.

        using Newtonsoft.Json.Linq;
2. En la clase **AppDelegate**, agregue una invalidación para el evento **RegisteredForRemoteNotifications** con el fin de registrar las notificaciones:

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. En **AppDelegate**, agregue también la invalidación siguiente para el controlador de eventos **DidReceiveRemoteNotification**:

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
4. En la clase **AppDelegate**, agregue el siguiente código al método **FinishedLaunching**:

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

#### <a name="test-push-notifications-in-your-ios-app"></a>Prueba de las notificaciones push en su aplicación de iOS
1. Haga clic con el botón derecho en el proyecto de iOS y luego haga clic en **Establecer como proyecto de inicio**.
2. Pulse el botón **Ejecutar** o presione **F5** en Visual Studio para compilar el proyecto e iniciar la aplicación en un dispositivo iOS. Luego, haga clic en **Aceptar** para aceptar las notificaciones de inserción.

   > [!NOTE]
   > Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.
   >
   >
3. En la aplicación, escriba una tarea y luego haga clic en el icono de signo de suma (**+**).
4. Compruebe que se ha recibido la notificación y luego haga clic en **Aceptar** para descartarla.

## <a name="configure-and-run-windows-projects-optional"></a>Configuración y ejecución de proyectos de Windows (opcional)
Esta sección está dedicada a la ejecución de los proyectos WinApp y WinPhone81 de Xamarin.Forms para dispositivos Windows. Estos pasos también son compatibles con los proyectos de Plataforma universal de Windows (UWP). Puede omitir esta sección si no está trabajando con dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registro de la aplicación de Windows para notificaciones de inserción en el Servicio de notificaciones de Windows (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configuración del Centro de notificaciones para WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Incorporación de notificaciones push a la aplicación de Windows
1. En Visual Studio, abra **App.xaml.cs** en un proyecto de Windows y agregue las siguiente instrucciones.

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
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

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

    Este método obtiene el canal de notificaciones de inserción y registra una plantilla para recibir notificaciones de plantilla desde el centro de notificaciones. Se entregará a este cliente una notificación de plantilla que admita *messageParam* .
3. En App.xaml.cs, actualice la definición de método del controlador de eventos **OnLaunched** agregando el modificador `async`. Luego, agregue la siguiente línea de código al final del método:

        await InitNotificationsAsync();

    Esto garantiza que se creará o actualizará el registro de notificaciones de inserción cada vez que se inicie la aplicación. Es importante hacer esto para garantizar que el canal de inserción WNS siempre esté activo.  
4. En el Explorador de soluciones de Visual Studio, abra el archivo **Package.appxmanifest** y establezca **Capacidad de aviso** en **Sí** en **Notificaciones**.
5. Compile la aplicación y compruebe que no haya errores. La aplicación cliente ahora debe registrarse para las notificaciones de plantilla desde el back-end de Mobile Apps. Repita esta sección para cada proyecto de Windows de la solución.

#### <a name="test-push-notifications-in-your-windows-app"></a>Prueba de las notificaciones push en su aplicación de Windows
1. En Visual Studio, haga clic con el botón derecho en un proyecto de Windows y luego haga clic en **Establecer como proyecto de inicio**.
2. Presione el botón **Run** para compilar el proyecto e iniciar la aplicación.
3. En la aplicación, escriba un nombre para un nuevo todoitem y haga clic en el icono del signo más (**+**) para agregarlo.
4. Compruebe que se recibe una notificación cuando se agrega el artículo.

## <a name="next-steps"></a>Pasos siguientes
Puede obtener más información sobre las notificaciones de inserción:

* [Diagnosticar problemas de notificaciones push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  : existen varias razones para que las notificaciones se pierdan o no lleguen a los dispositivos. En este tema se muestra cómo analizar y descubrir la causa principal de los errores de notificación de inserción.

También puede continuar con uno de los siguientes tutoriales:

* [Adición de la autenticación a la aplicación Xamarin.Android ](app-service-mobile-xamarin-forms-get-started-users.md)  
  : aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.
* [Habilitación de la sincronización sin conexión para su aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Aprenda a agregar compatibilidad sin conexión para la aplicación mediante un back-end de Mobile Apps. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no haya conexión de red.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
