---
title: "Introducción a los Notification Hubs para las aplicaciones Xamarin.Android | Microsoft Docs"
description: "En este tutorial, obtenga información acerca de cómo usar Azure Notification Hubs para enviar notificaciones push a una aplicación Xamarin Android."
author: jwhitedev
manager: kpiteira
editor: 
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 4cb3aaa3d4e577e45f01f245d3898c033092f5a3
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-notification-hubs-for-xamarinandroid-apps"></a>Introducción a Notification Hubs para las aplicaciones Xamarin.Android
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Información general
Este tutorial muestra cómo puede usar Azure Notification Hubs para enviar notificaciones push a una aplicación Xamarin.Android. Creará una aplicación de Xamarin.Android en blanco que recibirá notificaciones push mediante Firebase Cloud Messaging (FCM). Cuando haya finalizado, podrá usar el centro de notificaciones para difundir notificaciones push a todos los dispositivos que ejecutan su aplicación. El código acabado está disponible en el ejemplo de la [aplicación NotificationHubs][GitHub].

En este tutorial se demuestra el escenario de difusión sencillo con Notification Hubs.

## <a name="before-you-begin"></a>Antes de empezar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

El código completo de este tutorial se puede encontrar en GitHub [aquí][GitHub].

## <a name="prerequisites"></a>requisitos previos
Este tutorial requiere lo siguiente:

* [Visual Studio con Xamarin] en Windows o [Visual Studio para Mac] en OS X.
* Cuenta de Google activa

Completar este tutorial es un requisito previo para todos los demás tutoriales de Notification Hubs para aplicaciones Xamarin.Android.

> [!IMPORTANT]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).
> 
> 

## <a name="enable-firebase-cloud-messaging"></a>Habilitación de Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-your-notification-hub"></a>Configuración de su Centro de notificaciones
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li><p>Haga clic en la pestaña <b>Configurar</b> en la parte superior, escriba el valor <b>Clave de API</b> que obtuvo en la sección anterior y, a continuación, seleccione <b>Guardar</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

El centro de notificaciones ya está configurado para funcionar con FCM y dispone de las cadenas de conexión para registrar la aplicación para que reciba notificaciones y para enviar notificaciones push.

## <a name="connect-your-app-to-the-notification-hub"></a>Conexión de la aplicación al Centro de notificaciones
En primer lugar, cree un nuevo proyecto. 

1. En Visual Studio, elija **Nueva solución** > **Aplicación Android** y, a continuación, seleccione **Siguiente**.
   
      ![Visual Studio: creación de un nuevo proyecto de Android][22]

2. Escriba su **Nombre de la aplicación** e **Identificador**. Elija las opciones de **Plataformas de destino** que quiera admitir y luego elija **Siguiente** y **Crear**.
   
      ![Visual Studio: configuración de aplicaciones de Android][23]

    Esto crea un nuevo proyecto de Android.

3. Abra las propiedades del proyecto. Para ello, haga clic con el botón derecho en el nuevo proyecto en la vista Solución y elija **Opciones**. Seleccione el elemento **Android Application** (Aplicación Android) en la sección **Build** (Compilación).
   
    Asegúrese de que la primera letra de **Package name** (Nombre del paquete) sea minúscula.
   
   > [!IMPORTANT]
   > La primera letra del nombre del paquete debe ser minúscula. De lo contrario, recibirá errores de manifiesto de la aplicación al registrar la aplicación para las notificaciones push siguientes.
   > 
   > 
   
      ![Visual Studio: opciones de proyecto de Android][24]
4. De forma opcional, establezca el valor de **Minimum Android version** (Versión mínima de Android) en otro nivel de API.
5. De manera opcional, establezca **Versión Android de destino** en otra versión de API a la que quiera dirigirse (debe ser el nivel 8 de API o superior).
6. Haga clic en **Aceptar** y cierre el cuadro de diálogo Opciones del proyecto.

### <a name="add-the-required-packages-to-your-project"></a>Adición de los paquetes necesarios al proyecto

1. Haga clic con el botón derecho en el proyecto y elija **Agregar** > **Agregar paquetes NuGet**.
2. Busque **Xamarin.Azure.NotificationHubs.Android** y agréguelo al proyecto.
3. Busque **Xamarin.Firebase.Messaging** y agréguelo al proyecto.

### <a name="set-up-notification-hubs-in-your-project"></a>Configuración de los centros de notificaciones en su proyecto
1. Recopile la siguiente información para el centro de notificaciones y la aplicación Android:
   
   * **Cadena de conexión de escucha**: en el panel de [Azure Portal], elija **Ver cadenas de conexión**. Copie la cadena de conexión *DefaultListenSharedAccessSignature* para este valor.
   * **Nombre del centro**: este es el nombre del centro en [Azure Portal]. Por ejemplo, *mynotificationhub2*.
     
2. Cree una clase **Constants.cs** para el proyecto Xamarin y defina los siguientes valores de constante en la clase. Reemplace los marcadores de posición por sus valores.
    
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

6. Haga clic con el botón derecho en el proyecto y agregue el archivo `google-services.json` que descargó del proyecto de Firebase anteriormente. Haga clic con el botón derecho en el archivo agregado y establezca la acción de compilación en `GoogleServicesJson`.

    ![Visual Studio: configuración de google-services.json][25]

7. Cree una nueva clase, **MyFirebaseIIDService**.

8. Agregue las siguientes instrucciones using a la clase **MyFirebaseIIDService.cs**:
   
    ```csharp
        using System;
        using Android.App;
        using Firebase.Iid;
        using Android.Util;
        using WindowsAzure.Messaging;
        using System.Collections.Generic;
    ```

9. En **MyFirebaseIIDService.cs**, agregue lo siguiente encima de la declaración de **clase** y haga que su clase se herede de **FirebaseInstanceIdService**:
   
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
        using System;
        using System.Linq;
        using Android;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Firebase.Messaging;
    ```

13. Agregue lo siguiente encima de su declaración de clase y haga que su clase se herede de **FirebaseMessagingService**:
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseIIDService : FirebaseMessagingService
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

15. Ejecute la aplicación en su dispositivo o en el emulador cargado.

## <a name="send-notifications-from-the-portal"></a>Envío de notificaciones desde el portal
Puede probar de recibir notificaciones en la aplicación con la opción *Envío de prueba* en [Azure Portal]. Se enviará una notificación push de prueba al dispositivo.

![Azure Portal: envío de prueba][30]

Las notificaciones push se envían normalmente en un servicio back-end como Mobile Services o ASP.NET mediante una biblioteca compatible. También puede usar la API de REST directamente para enviar mensajes de notificación si no hay disponible ninguna biblioteca para su back-end.

A continuación, presentamos una lista de algunos otros tutoriales que podría interesarle revisar para enviar notificaciones:

* ASP.NET: consulte [Notificación a los usuarios con Azure Notification Hubs].
* SDK de Java para Azure Notification Hubs: consulte [Uso de Notification Hubs desde Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificaciones desde Java. Esto se probó en Eclipse para el desarrollo de Android.
* PHP: consulte [Uso de Notification Hubs desde PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>pasos siguientes
En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Notificación a los usuarios con Azure Notification Hubs]. Si desea segmentar a sus usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto]. Para más información acerca del uso de Notification Hubs, consulte [Introducción a los Notification Hubs] y [Procedimiento: Azure Notification Hubs (aplicaciones Android)].

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
[Visual Studio con Xamarin]: https://docs.microsoft.com/en-us/visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Introducción a los Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimiento: Azure Notification Hubs (aplicaciones Android)]: http://msdn.microsoft.com/library/dn282661.aspx

[Notificación a los usuarios con Azure Notification Hubs]: /manage/services/notification-hubs/notify-users-aspnet
[Uso de Centros de notificaciones para enviar noticias de último minuto]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
