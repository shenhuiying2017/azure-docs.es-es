---
title: Envío de notificaciones push a aplicaciones Xamarin.Android mediante Azure Notification Hubs | Microsoft Docs
description: En este tutorial, obtenga información acerca de cómo usar Azure Notification Hubs para enviar notificaciones push a una aplicación Xamarin Android.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>Tutorial: Envío de notificaciones push a aplicaciones de Xamarin.Android mediante Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Información general
Este tutorial muestra cómo puede usar Azure Notification Hubs para enviar notificaciones push a una aplicación Xamarin.Android. Cree una aplicación de Xamarin.Android en blanco que reciba notificaciones push mediante Firebase Cloud Messaging (FCM). Use el Centro de notificaciones para difundir notificaciones push a todos los dispositivos que ejecutan la aplicación. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][GitHub].

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Crear un proyecto de Firebase y habilitar Firebase Cloud Messaging
> * Crear un Centro de notificaciones
> * Crear una aplicación de Xamarin.Android y conectarla al Centro de notificaciones
> * Enviar notificaciones de prueba desde Azure Portal

## <a name="prerequisites"></a>requisitos previos

- **Suscripción de Azure**. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- [Visual Studio con Xamarin] en Windows o [Visual Studio para Mac] en OS X.
- Cuenta de Google activa

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Crear un proyecto de Firebase y habilitar Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Creación de un centro de notificaciones
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>Configuración de los valores de GCM para el Centro de notificaciones

1. Seleccione **Google (GCM)** en la sección **NOTIFICATION SETTINGS** (CONFIGURACIÓN DE NOTIFICACIONES). 
2. Escriba el valor de **Legacy server key** (Clave de servidor heredada) que anotó en Google Firebase Console. 
3. Seleccione **Guardar** en la barra de herramientas. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

El centro de notificaciones ya está configurado para funcionar con FCM y dispone de las cadenas de conexión para registrar la aplicación para que reciba notificaciones y para enviar notificaciones push.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Creación de una aplicación de Xamarin.Android y su conexión al Centro de notificaciones

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Creación de un proyecto de Visual Studio y adición de paquetes NuGet
1. En Visual Studio, seleccione **Archivo**, **Nuevo** y, finalmente, **Proyecto**. 
   
      ![Visual Studio: creación de un nuevo proyecto de Android](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. En la ventana del **Explorador de soluciones**, expanda **Propiedades**y haga clic en **AndroidManifest.xml**. Actualice el nombre del paquete para que coincida con el nombre del paquete que especificó al agregar Firebase Cloud Messaging al proyecto en Google Firebase Console. 

      ![Nombre del paquete en GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. Haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes NuGet...** 
4. Seleccione la pestaña **Examinar**. Busque **Xamarin.GooglePlayServices.Base**. Seleccione **Xamarin.GooglePlayServices.Base** en la lista de resultados. Luego, seleccione **Instalar**. 

      ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. En la ventana **Administrador de paquetes NuGet**, busque **Xamarin.Firebase.Messaging**. Seleccione **Xamarin.Firebase.Messaging** en la lista de resultados. Luego, seleccione **Instalar**. 
6. Ahora, busque **Xamarin.Azure.NotificationHubs.Android**. Seleccione **Xamarin.Azure.NotificationHubs.Android** en la lista de resultados. Luego, seleccione **Instalar**. 

### <a name="add-the-google-services-json-file"></a>Adición del archivo JSON de Google Services

1. Copie el archivo **google-services.json** que ha descargado de Google Firebase Console a la carpeta del proyecto.
2. Agregue **google-services.json** al proyecto.
3. Seleccione **google services.json** en la ventana del **Explorador de soluciones**.
4. En el panel **Propiedades**, seleccione **GoogleServicesJson** en Acción de compilación. Si no ve **GoogleServicesJson**, cierre Visual Studio, vuelva a iniciarlo, vuelva a abrir el proyecto y vuelva a intentarlo. 

      ![Acción de compilación GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configuración de los centros de notificaciones en su proyecto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registro en Firebase Cloud Messaging

Abra el archivo **AndroidManifest.xml** e inserte los siguientes elementos `<receiver>` en el elemento `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Recopile la siguiente información para el centro de notificaciones y la aplicación Android:
   
   * **Cadena de conexión de escucha**: en el panel de [Azure Portal], elija **Ver cadenas de conexión**. Copie la cadena de conexión *DefaultListenSharedAccessSignature* para este valor.
   * **Nombre del centro**: es el nombre del centro en [Azure Portal]. Por ejemplo, *mynotificationhub2*.
     
2. Haga clic con el botón derecho en el **proyecto** en la ventana del **Explorador de soluciones**, seleccione **Agregar** y, luego, **Clase**. 
4. Cree una clase **Constants.cs** para el proyecto Xamarin y defina los siguientes valores de constante en la clase. Reemplace los marcadores de posición por sus valores.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. Agregue las siguientes instrucciones a **MainActivity.cs**:
   
    ```csharp
        using Android.Util;
    ```
4. Agregue una variable de instancia a la clase **MainActivity.cs** que se usará para mostrar un cuadro de diálogo de alerta cuando se esté ejecutando la aplicación:
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. Agregue el código siguiente a `OnCreate` en **MainActivity.cs** después de `base.OnCreate(savedInstanceState)`:

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. Cree una clase nueva, **MyFirebaseIIDService**, tal como creó la clase **Constants**. 
8. Agregue las siguientes instrucciones using a la clase **MyFirebaseIIDService.cs**:
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. En **MyFirebaseIIDService.cs**, agregue la siguiente declaración de **clase** y haga que su clase se herede de **FirebaseInstanceIdService**:
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. En **MyFirebaseIIDService.cs**, agregue el código siguiente:
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. Cree otra nueva clase para su proyecto y asígnele el nombre **MyFirebaseMessagingService**.
12. Agregue las siguientes instrucciones using a la clase **MyFirebaseMessaging.cs**.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. Agregue lo siguiente encima de su declaración de clase y haga que su clase se herede de **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. Agregue el código siguiente a **MyFirebaseMessagingService.cs**:
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. **Compile** el proyecto. 
16. **Ejecute** la aplicación en su dispositivo o en el emulador cargado.

## <a name="send-test-notification-from-the-azure-portal"></a>Envío de notificaciones de prueba desde Azure Portal
Puede probar de recibir notificaciones en la aplicación con la opción *Envío de prueba* en [Azure Portal]. Envía una notificación push de prueba al dispositivo.

![Azure Portal: envío de prueba](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Las notificaciones push se envían normalmente en un servicio back-end como Mobile Services o ASP.NET mediante una biblioteca compatible. Si no hay disponible ninguna biblioteca para su back-end, también puede usar la API de REST directamente para enviar mensajes de notificación.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial se envían las notificaciones de difusión a todos los dispositivos Android registrados con el back-end. Para aprender a enviar notificaciones push a dispositivos Android específicos, pase al siguiente tutorial: 

> [!div class="nextstepaction"]
>[Envío de notificaciones push a dispositivos específicos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio con Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
