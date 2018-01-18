---
title: "Adición de notificaciones push a una aplicación de Xamarin.Forms | Microsoft Docs"
description: Aprenda a usar los servicios de Azure para enviar notificaciones push multiplataforma a sus aplicaciones de Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: a9c7c5dbbc50ccf8c5383be28e96dfb82af48559
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
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

1. En el proyecto **Droid**, haga clic con el botón derecho en **Referencias > Administrar paquetes NuGet...**.
1. En la ventana del Administrador de paquetes NuGet, busque el paquete **Xamarin.Firebase.Messaging** y agréguelo al proyecto.
1. En las propiedades del proyecto **Droid**, configure la aplicación para la compilación con la versión Android 7.0 o superior.
1. Agregue el archivo **google services.json**, que se descargó de la consola de Firebase, a la raíz del proyecto **Droid** y establezca su acción de compilación en **GoogleServicesJson**. Para más información, consulte [Add the Google Services JSON File](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File) (Incorporación del archivo JSON de Google Services).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registro en Firebase Cloud Messaging

1. Abra el archivo **AndroidManifest.xml** e inserte los siguientes elementos `<receiver>` en el elemento `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementación del servicio de identificación de instancia de Firebase

1. Agregue una nueva clase al proyecto **Droid** denominada `FirebaseRegistrationService` y asegúrese de que las siguientes instrucciones `using` están presentes en la parte superior del archivo:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Reemplace la clase `FirebaseRegistrationService` vacía por el siguiente código:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class FirebaseRegistrationService : FirebaseInstanceIdService
        {
            const string TAG = "FirebaseRegistrationService";

            public override void OnTokenRefresh()
            {
                var refreshedToken = FirebaseInstanceId.Instance.Token;
                Log.Debug(TAG, "Refreshed token: " + refreshedToken);
                SendRegistrationTokenToAzureNotificationHub(refreshedToken);
            }

            void SendRegistrationTokenToAzureNotificationHub(string token)
            {
                // Update notification hub registration
                Task.Run(async () =>
                {
                    await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
                });
            }
        }

    La clase `FirebaseRegistrationService` es responsable de generar tokens de seguridad que autorizan el acceso de la aplicación a FCM. El método `OnTokenRefresh` se invoca cuando la aplicación recibe un token de registro de FCM. El método recupera el token de la propiedad `FirebaseInstanceId.Instance.Token`, que FCM actualiza de forma asincrónica. El método `OnTokenRefresh` se invoca con poca frecuencia, porque el token solo se actualiza cuando la aplicación se instala o desinstala, el usuario elimina los datos de la aplicación, la aplicación borra el identificador de instancia o peligra la seguridad del token. Además, el servicio de identificación de instancia de FCM solicitará que la aplicación actualice el token de manera periódica, normalmente cada 6 meses.

    El método `OnTokenRefresh` también invoca el método `SendRegistrationTokenToAzureNotificationHub`, que se usa para asociar el token de registro del usuario con el centro de notificaciones de Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registro con el centro de notificaciones de Azure

1. Agregue una nueva clase al proyecto **Droid** denominada `AzureNotificationHubService` y asegúrese de que las siguientes instrucciones `using` están presentes en la parte superior del archivo:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Reemplace la clase `AzureNotificationHubService` vacía por el siguiente código:

        public class AzureNotificationHubService
        {
            const string TAG = "AzureNotificationHubService";

            public static async Task RegisterAsync(Push push, string token)
            {
                try
                {
                    const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                    JObject templates = new JObject();
                    templates["genericMessage"] = new JObject
                    {
                        {"body", templateBody}
                    };

                    await push.RegisterAsync(token, templates);
                    Log.Info("Push Installation Id: ", push.InstallationId.ToString());
                }
                catch (Exception ex)
                {
                    Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
                }
            }
        }

    El método `RegisterAsync` crea una plantilla de mensaje de notificación simple como JSON y registros para recibir notificaciones de plantilla desde el centro de notificaciones con el token de registro de Firebase. Esto garantiza que las notificaciones enviadas desde el centro de notificaciones de Azure se destinan al dispositivo que representa el token de registro.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visualización del contenido de una notificación push

1. Agregue una nueva clase al proyecto **Droid** denominada `FirebaseNotificationService` y asegúrese de que las siguientes instrucciones `using` están presentes en la parte superior del archivo:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Util;
        using Firebase.Messaging;

1. Reemplace la clase `FirebaseNotificationService` vacía por el siguiente código:

        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class FirebaseNotificationService : FirebaseMessagingService
        {
            const string TAG = "FirebaseNotificationService";

            public override void OnMessageReceived(RemoteMessage message)
            {
                Log.Debug(TAG, "From: " + message.From);

                // Pull message body out of the template
                var messageBody = message.Data["message"];
                if (string.IsNullOrWhiteSpace(messageBody))
                    return;

                Log.Debug(TAG, "Notification message body: " + messageBody);
                SendNotification(messageBody);
            }

            void SendNotification(string messageBody)
            {
                var intent = new Intent(this, typeof(MainActivity));
                intent.AddFlags(ActivityFlags.ClearTop);
                var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

                var notificationBuilder = new Notification.Builder(this)
                    .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                    .SetContentTitle("New Todo Item")
                    .SetContentText(messageBody)
                    .SetContentIntent(pendingIntent)
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                    .SetAutoCancel(true);

                var notificationManager = NotificationManager.FromContext(this);
                notificationManager.Notify(0, notificationBuilder.Build());
            }
        }

    El método `OnMessageReceived`, que se invoca cuando una aplicación recibe una notificación de FCM, extrae el contenido del mensaje y llama al método `SendNotification`. Este método convierte el contenido del mensaje en una notificación local que se inicia mientras se ejecuta la aplicación, con la notificación que aparece en el área de notificación.

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

## <a name="next-steps"></a>pasos siguientes
Puede obtener más información sobre las notificaciones de inserción:

* [Sending Push Notifications from Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/) (Envío de notificaciones push desde Azure Mobile Apps)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Remote Notifications with Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/) (Notificaciones remotas con Firebase Cloud Messaging)
* [Diagnosticar problemas de notificaciones push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  : existen varias razones para que las notificaciones se pierdan o no lleguen a los dispositivos. En este tema se muestra cómo analizar y descubrir la causa principal de los errores de notificación de inserción.

También puede continuar con uno de los siguientes tutoriales:

* [Adición de la autenticación a la aplicación Xamarin.Android ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.
* [Habilitación de la sincronización sin conexión para su aplicación](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Aprenda a agregar compatibilidad sin conexión para la aplicación mediante un back-end de Mobile Apps. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no haya conexión de red.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
