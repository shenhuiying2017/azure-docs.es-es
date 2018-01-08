---
title: "Introducción a Azure Notification Hubs para aplicaciones Android y Firebase Cloud Messaging | Microsoft Docs"
description: "En este tutorial aprenderá a usar Azure Notification Hubs y Firebase Cloud Messaging para enviar notificaciones push a dispositivos Android."
services: notification-hubs
documentationcenter: android
keywords: "notificaciones push,notificación push,notificación push android, fcm, firebase cloud messaging"
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 2cac554be145c3bb9ec2c71ef893bba947104a2d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-android-apps-and-firebase-cloud-messaging"></a>Introducción a Azure Notification Hubs para aplicaciones Android y Firebase Cloud Messaging
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Información general
> [!IMPORTANT]
> En este artículo se muestran las notificaciones push con Firebase Cloud Messaging (FCM) de Google. Si aún utiliza Google Cloud Messaging (GCM), consulte [Envío de notificaciones push a Android con Azure Notification Hubs](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

En este tutorial se muestra cómo usar Azure Notification Hubs y Firebase Cloud Messaging para enviar notificaciones push a una aplicación de Android. En este tutorial, creará una aplicación de Android en blanco que recibirá notificaciones push mediante Firebase Cloud Messaging (FCM).

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

El código completo de este tutorial se puede descargar de GitHub, [aquí](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

## <a name="prerequisites"></a>requisitos previos
> [!IMPORTANT]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).
> 
> 

* Además de la cuenta de Azure activa mencionada anteriormente, este tutorial requiere la versión más reciente de [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3, o una versión superior, para Firebase Cloud Messaging.
* Para Firebase Cloud Messaging, se requiere la revisión 27 del repositorio de Google, o una revisión posterior.
* Google Play Services 9.0.2, o una versión superior, para Firebase Cloud Messaging.
* La realización de este tutorial es un requisito previo para todos los tutoriales de Notification Hubs para aplicaciones Android.

## <a name="create-a-new-android-studio-project"></a>Creación de un nuevo proyecto de Android Studio
1. En Android Studio, inicie un nuevo proyecto de Android Studio.
   
    ![Android Studio: nuevo proyecto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Elija el factor de forma **Teléfono y tableta** y el **SDK mínimo** que desea admitir. A continuación, haga clic en **Siguiente**.
   
    ![Android Studio: flujo de trabajo de creación de proyecto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Elija **Empty Activity** (Actividad vacía) para la actividad principal, haga clic en **Next** (Siguiente) y, luego, en **Finish** (Finalizar).

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Creación de un proyecto que admita Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-new-notification-hub"></a>Configuración de un centro de notificaciones nuevo
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. En **Notification Services**, seleccione **Google (GCM)**. Escriba la clave de servidor de FCM que copió anteriormente de la [consola de Firebase](https://firebase.google.com/console/) y haga clic en **Save**(Guardar).

&emsp;&emsp;![Centros de notificaciones de Azure: Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

El Centro de notificaciones está configurado para funcionar con Firebase Cloud Messaging y el usuario dispone de las cadenas de conexión necesarias para registrar su aplicación para que reciba y envíe notificaciones push.

## <a id="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones
### <a name="add-google-play-services-to-the-project"></a>Incorporación de los servicios de Google Play al proyecto
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Incorporación de bibliotecas de Azure Notification Hubs
1. En el archivo `Build.Gradle` de la **aplicación**, agregue las siguientes líneas en la sección **dependencies**.
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Agregue el repositorio siguiente después de la sección de **dependencias** .
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="updating-the-androidmanifestxml"></a>Actualización de AndroidManifest.xml.
1. Para admitir FCM, es preciso implementar un servicio de escucha Instance ID en el código, que se usa para [obtener tokens de registro](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) con la [API FirebaseInstanceId de Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). En este tutorial, el nombre de la clase es `MyInstanceIDService`. 
   
    Agregue la siguiente definición de servicio al archivo AndroidManifest.xml, en la etiqueta `<application>` . 
   
    ```xml
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
    ```

2. Una vez que haya recibido el token de registro de FCM de la API FirebaseInstanceId, lo usará para el [registro en el Centro de notificaciones de Azure](notification-hubs-push-notification-registration-management.md). Este registro se admite en segundo plano con un servicio `IntentService` denominado `RegistrationIntentService`. Este servicio también será responsable de actualizar su token de registro de FCM.
   
    Agregue la siguiente definición de servicio al archivo AndroidManifest.xml, en la etiqueta `<application>` . 
   
    ```xml
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
    ```

3. También debe definir un receptor para recibir las notificaciones. Agregue la siguiente definición de receptor al archivo AndroidManifest.xml, en la etiqueta `<application>` . Reemplace el marcador de posición `<your package>` por el nombre real del paquete, que se muestra en la parte superior del archivo `AndroidManifest.xml`.

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. Agregue los siguientes permisos relacionados con FCM necesarios debajo de la etiqueta `</application>`. Asegúrese de reemplazar `<your package>` por el nombre del paquete que se muestra en la parte superior del archivo `AndroidManifest.xml`.
   
    Para más información acerca de estos permisos, consulte [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Configurar una aplicación cliente de GCM para Android) y [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Migrar una aplicación cliente de GCM para Android a Firebase Cloud Messaging).
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Incorporación de código
1. En la Vista de proyecto, expanda **app** > **src** > **main** > **java**. Haga clic con el botón derecho en la carpeta del paquete en **java**, haga clic en **Nuevo** y, a continuación, haga clic en **Clase Java**. Agregue una clase nueva llamada `NotificationSettings`. 
   
    ![Android Studio: nueva clase Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Asegúrese de actualizar estos tres marcadores de posición en el código siguiente para la clase `NotificationSettings`:
   
   * **SenderId**: el id. de remitente que obtuvo anteriormente en la pestaña **Mensajería en la nube** de la configuración del proyecto en la [consola de Firebase](https://firebase.google.com/console/).
   * **HubListenConnectionString**: la cadena de conexión **DefaultListenAccessSignature** del centro. Para copiar dicha cadena de conexión, haga clic en **Directivas de acceso** en su centro en [Azure Portal].
   * **HubName**: use el nombre del Centro de notificaciones que aparece en la hoja del centro en [Azure Portal].
     
     `NotificationSettings` :
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. Use los pasos anteriores para agregar otra clase nueva denominada `MyInstanceIDService`. Se trata de su implementación de servicio de escucha de Instance ID.
   
    El código de esta clase llama a la clase `IntentService` para [actualizar el token de FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en segundo plano.
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. Agregue otra clase nueva, llamada `RegistrationIntentService`, al proyecto. Es la implementación de la clase `IntentService` que controla la [actualización del token de FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) y el [registro en el Centro de notificaciones](notification-hubs-push-notification-registration-management.md).
   
    Use el siguiente código para esta clase.
   
    ```java
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class RegistrationIntentService extends IntentService {
   
            private static final String TAG = "RegIntentService";
   
            private NotificationHub hub;
   
            public RegistrationIntentService() {
                super(TAG);
            }
   
            @Override
            protected void onHandleIntent(Intent intent) {
   
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
   
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
   
                    // Storing the registration ID that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
    ```

2. En la clase `MainActivity`, agregue las siguientes instrucciones `import` encima de la declaración de la clase.
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;
    ```

3. Agregue los siguientes miembros privados en la parte superior de la clase. Se usan para [comprobar la disponibilidad de Google Play Services, tal como recomienda Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. En la clase `MainActivity` , agregue el método siguiente a la disponibilidad de Google Play Services. 
   
    ```java
        /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }
    ```

5. En la clase `MainActivity`, agregue el siguiente código, que comprueba Google Play Services antes de llamar a `IntentService` para obtener el token de registro de FCM y realizar el registro en el Centro de notificaciones.
   
    ```java
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
    ```

6. En el método `OnCreate` de la clase `MainActivity`, agregue el código siguiente para iniciar el proceso de registro cuando se crea la actividad.
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. Para comprobar el estado de la aplicación y el del informe de la aplicación, agregue estos métodos adicionales a la clase `MainActivity`.
   
    ```java
        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
   
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
   
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
   
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
   
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }
    ```

8. El método `ToastNotify` usa el control *"Hello World"* `TextView` para informar del estado y de las notificaciones de forma persistente en la aplicación. En el archivo activity_main.xml, agregue el siguiente identificador para ese control.
   
    ```java
       android:id="@+id/text_hello"
    ```

9. A continuación, agregará una subclase para el receptor que definió en el archivo AndroidManifest.xml. Agregue otra clase nueva, llamada `MyHandler`, al proyecto.
10. Agregue las siguientes instrucciones de importación en la parte superior de `MyHandler.java`:
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. Agregue el siguiente código para la clase `MyHandler` para convertirla en una subclase de `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Este código invalida el método `OnReceive`, por lo que el controlador informa de las notificaciones que se reciben. El controlador también envía la notificación push al administrador de notificaciones de Android mediante el método `sendNotification()` . El método `sendNotification()` se debe ejecutar cuando se reciba una notificación y la aplicación no se esté ejecutando.
    
    ```java
        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
    
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
    
            private void sendNotification(String msg) {
    
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
    
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. En la barra de menús de Android Studio, haga clic en **Build** (Compilar)  > **Rebuild Project** (Recompilar proyecto) para asegurarse de que no hay errores en el código.
13. Ejecute la aplicación en el dispositivo y compruebe que se registra correctamente con el Centro de notificaciones. 
    
    > [!NOTE]
    > El registro puede producir un error la primera vez que se realiza el inicio hasta que se llama al método `onTokenRefresh()` del servicio de identificación de la instancia. La actualización debe iniciar un registro correcto en el Centro de notificaciones.
    > 
    > 

## <a name="sending-push-notifications"></a>Envío de notificaciones push
Para probar la recepción de notificaciones push en la aplicación, envíelas a través de [Azure Portal] (busque la sección **Solución del problemas** en el centro, como se muestra a continuación).

![Azure Notification Hubs: envío de prueba](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="testing-your-app"></a>Prueba de la aplicación
#### <a name="push-notifications-in-the-emulator"></a>Notificaciones push en el emulador
Si desea probar notificaciones push en un emulador, asegúrese de que la imagen del emulador admita el nivel de API de Google que elija para la aplicación. Si la imagen no es compatible con las API de Google nativas, aparecerá la excepción **SERVICE\_NOT\_AVAILABLE**.

Además, asegúrese de haber agregado su cuenta de Google al emulador en ejecución en **Configuración** > **Cuentas**. De lo contrario, sus intentos de registrarse con GCM podrían generar la excepción **AUTHENTICATION\_FAILED**.

#### <a name="running-the-application"></a>Ejecución de la aplicación
1. Ejecute la aplicación y observe que el Id. de registro se informa para un registro correcto.
   
    ![Prueba en Android: registro de canal](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Escriba un mensaje de notificación que se enviará a todos los dispositivos Android registrados con el centro.
   
    ![Prueba en Android - envío de un mensaje](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Presione **Enviar notificación**. Los dispositivos en los que se ejecuta la aplicación muestran una instancia de `AlertDialog` con el mensaje de notificación push. Los dispositivos en que la aplicación no esté en ejecución, pero que anteriormente se han registrado para recibir notificaciones push, reciben una notificación en el administrador de notificaciones de Android. Para verlas, deslice el dedo hacia abajo desde la esquina superior izquierda.
   
    ![Prueba en Android - notificaciones](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>pasos siguientes
Se recomienda seguir el tutorial [Notificación a los usuarios con Azure Notification Hubs] como paso siguiente. Se muestra cómo enviar notificaciones desde un back-end de ASP.NET mediante etiquetas para dirigirse a usuarios específicos.


Si desea segmentar los usuarios por grupos de interés, consulte el tutorial [Uso de Notification Hubs para enviar noticias de última hora] .

Para más información sobre Notification Hubs, consulte [Introducción a Notification Hubs].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Introducción a Notification Hubs]: notification-hubs-push-notification-overview.md
[Notificación a los usuarios con Azure Notification Hubs]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Uso de Notification Hubs para enviar noticias de última hora]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
