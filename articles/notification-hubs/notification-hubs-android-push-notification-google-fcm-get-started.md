<properties
	pageTitle="Envío de notificaciones push a Android con los Centros de notificaciones de Azure y Firebase Cloud Messaging | Microsoft Azure"
	description="En este tutorial aprenderá a usar los Centros de notificaciones de Azure y Firebase Cloud Messaging para enviar notificaciones push a dispositivos Android."
	services="notification-hubs"
	documentationCenter="android"
	keywords="notificaciones push,notificación push,notificación push android, fcm, firebase cloud messaging"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="wesmc"/>

# Envío de notificaciones push a Android con los Centros de notificaciones de Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Información general

> [AZURE.IMPORTANT] En este tema se muestran las notificaciones push con Firebase Cloud Messaging (FCM) de Google. Si aún utiliza el Servicio de mensajería en la nube (GCM) de Google, consulte [Envío de notificaciones push a Android con los Centros de notificaciones de Azure](notification-hubs-android-push-notification-google-gcm-get-started.md).

En este tutorial se muestra cómo usar Centros de notificaciones de Azure y Firebase Cloud Messaging para enviar notificaciones push a una aplicación de Android. Creará una aplicación de Android en blanco que reciba notificaciones push mediante Firebase Cloud Messaging (FCM).



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

El código completo de este tutorial se puede descargar de GitHub [aquí](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##Requisitos previos

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- Además de la cuenta de Azure activa mencionada anteriormente, este tutorial requiere la versión más reciente de [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android 2.3, o una versión superior, para Firebase Cloud Messaging.
- Para Firebase Cloud Messaging, se requiere la revisión 27 del repositorio de Google, o una revisión posterior.
- Google Play Services 9.0.2, o una versión superior, para Firebase Cloud Messaging.
- La realización de este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones Android.


##Creación de un nuevo proyecto de Android Studio

1. En Android Studio, inicie un nuevo proyecto de Android Studio.

   	![Android Studio: nuevo proyecto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Elija el factor de forma **Teléfono y tableta** y el **SDK mínimo** que desea admitir. A continuación, haga clic en **Siguiente**.

   	![Android Studio: flujo de trabajo de creación de proyecto](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Elija **Empty Activity** (Actividad vacía) para la actividad principal, haga clic en **Next** (Siguiente) y, luego, en **Finish** (Finalizar).


##Creación de un proyecto que admita Firebase Cloud Messaging

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##Configuración de un centro de notificaciones nuevo

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. En la hoja **Settings** (Configuración) del Centro de notificaciones, seleccione **Notification Services** (Servicios de notificaciones) y, luego, **Google (GCM)**. Escriba la clave de servidor de FCM que copió anteriormente de la [consola de Firebase](https://firebase.google.com/console/) y haga clic en **Save** (Guardar).

&emsp;&emsp;![Centros de notificaciones de Azure: Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

El Centro de notificaciones está configurado para funcionar con Firebase Cloud Messagin y el usuario dispone de las cadenas de conexión necesarias para registrar su aplicación para que reciba y envíe notificaciones push.

##<a id="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

###Incorporación de los servicios de Google Play al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Incorporación de bibliotecas de Centros de notificaciones de Azure


1. En el archivo `Build.Gradle` de la **aplicación**, agregue las siguientes líneas en la sección **dependencies**.

		compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
		compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Agregue el repositorio siguiente después de la sección de **dependencias**.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### Actualización de AndroidManifest.xml.


1. Para admitir FCM, es preciso implementar un servicio de escucha Instance ID en el código, que se usa para [obtener tokens de registro](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) con la [API FirebaseInstanceId de Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). En este tutorial, asignaremos el nombre `MyInstanceIDService` a la clase.
 
	Agregue la siguiente definición de servicio al archivo AndroidManifest.xml, en la etiqueta `<application>`.

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Una vez que hayamos recibido el token de registro de FCM de la API FirebaseInstanceId, los usaremos para el [registro en el Centro de notificaciones de Azure](notification-hubs-push-notification-registration-management.md). Este registro se admite en segundo plano con un servicio `IntentService` llamado `RegistrationIntentService`. Este servicio también será responsable de actualizar el token de registro de FCM.
 
	Agregue la siguiente definición de servicio al archivo AndroidManifest.xml, en la etiqueta `<application>`.

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. También definiremos un receptor para recibir las notificaciones. Agregue la siguiente definición de receptor al archivo AndroidManifest.xml, en la etiqueta `<application>`. Reemplace el marcador de posición `<your package>` por el nombre real del paquete, que se muestra en la parte superior del archivo `AndroidManifest.xml`.

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. Agregue los siguientes permisos relacionados con FCM necesarios debajo de la etiqueta `</application>`. Asegúrese de reemplazar `<your package>` por el nombre del paquete que se muestra en la parte superior del archivo `AndroidManifest.xml`.

	Para más información acerca de estos permisos, consulte [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/android/client#manifest) (Configurar una aplicación cliente de GCM para Android) y [Migrate a GCM Client App for Android to Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm) (Migrar una aplicación cliente de GCM para Android a Firebase Cloud Messaging).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### Incorporación de código


1. En la Vista de proyecto, expanda **app** > **src** > **main** > **java**. Haga clic con el botón derecho en la carpeta del paquete en **java**, haga clic en **Nuevo** y, a continuación, haga clic en **Clase Java**. Agregue una clase nueva llamada `NotificationSettings`.

	![Android Studio: nueva clase Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

	Asegúrese de actualizar estos tres marcadores de posición en el código siguiente para la clase `NotificationSettings`:
	* **SenderId**: el Id. de remitente que obtuvo anteriormente en la pestaña **Cloud Messaging** (Mensajería en la nube) de la configuración del proyecto en la [consola de Firebase](https://firebase.google.com/console/).
	* **HubListenConnectionString**: la cadena de conexión **DefaultListenAccessSignature** del centro. Para copiar dicha cadena de conexión, haga clic en **Directivas de acceso** en la hoja **Configuración** de su centro en el [Portal de Azure].
	* **HubName**: use el nombre del Centro de notificaciones que aparece en la hoja del centro en el [Portal de Azure].

	Código de `NotificationSettings`:

		public class NotificationSettings {
		    public static String SenderId = "<Your project number>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
		}

2. Use los pasos anteriores para agregar otra clase nueva llamada `MyInstanceIDService`. Se trata de nuestra implementación de servicio de escucha de Instance ID.

	El código de esta clase llamará a la clase `IntentService` para [actualizar el token de FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) en segundo plano.

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


3. Agregue otra clase nueva, llamada `RegistrationIntentService`, al proyecto. Será la implementación de la clase `IntentService` que controlará la [actualización del token de FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) y el [registro en el Centro de notificaciones](notification-hubs-push-notification-registration-management.md).

	Use el siguiente código para esta clase.

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
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
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
		                // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
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


		
4. En la clase `MainActivity`, agregue las siguientes instrucciones `import` encima de la declaración de la clase.

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.content.Intent;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. Agregue los siguientes miembros privados en la parte superior de la clase. Los usaremos para [comprobar la disponibilidad de Google Play Services, tal como recomienda Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

	    public static MainActivity mainActivity;
    	public static Boolean isVisible = false;	
	    private static final String TAG = "MainActivity";
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. En la clase `MainActivity`, agregue el método siguiente a la disponibilidad de Google Play Services.

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


7. En la clase `MainActivity`, agregue el siguiente código, que comprobará Google Play Services antes de llamar a `IntentService` para obtener el token de registro de FCM y realizar el registro en el Centro de notificaciones.

	    public void registerWithNotificationHubs()
	    {
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with FCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. En el método `OnCreate` de la clase `MainActivity`, agregue el código siguiente para iniciar el proceso de registro cuando se crea la actividad.

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. Agregue estos métodos adicionales a `MainActivity` para comprobar el estado de la aplicación y el del informe de la aplicación.

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


10. El método `ToastNotify` usa el control *"Hello World"* `TextView` para informar del estado y de las notificaciones de forma persistente en la aplicación. En el archivo activity\_main.xml, agregue el siguiente identificador para ese control.

        android:id="@+id/text_hello"

11. A continuación, agregaremos una subclase para el receptor que definimos en el archivo AndroidManifest.xml. Agregue otra clase nueva, llamada `MyHandler`, al proyecto.

12. Agregue las siguientes instrucciones de importación en la parte superior de `MyHandler.java`:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.media.RingtoneManager;
		import android.net.Uri;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Agregue el siguiente código para la clase `MyHandler` para convertirla en una subclase de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

	Este código invalida el método `OnReceive`, por lo que el controlador informará de las notificaciones que se reciban. El controlador también envía la notificación push al administrador de notificaciones de Android mediante el método `sendNotification()`. El método `sendNotification()` se debe ejecutar cuando se reciba una notificación y la aplicación no se esté ejecutando.

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


14. En la barra de menús de Android Studio, haga clic en **Build** (Compilar) -> **Rebuild Project** (Recompilar proyecto) para asegurarse de que no hay errores en el código.
15. Ejecute la aplicación en el dispositivo y compruebe que se registra correctamente con el Centro de notificaciones.

	> [AZURE.NOTE] El registro puede producir un error la primera vez que se realiza el inicio hasta que se llama al método `onTokenRefresh()` del servicio de identificación de la instancia. La actualización debe iniciar un registro correcto en el Centro de notificaciones.

##Envío de notificaciones push

Para probar la recepción de notificaciones push en la aplicación, envíelas a través del [Portal de Azure] \(busque la sección **Solución del problemas** en la hoja del centro, como se muestra a continuación).

![Centros de notificaciones de Azure: envío de prueba](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (Opcional) Envío de notificaciones push directamente desde la aplicación

>[AZURE.IMPORTANT] Este ejemplo sobre el envío de notificaciones desde la aplicación cliente se ha diseñado exclusivamente con fines informativos. `DefaultFullSharedAccessSignature` deberá estar presente en la aplicación cliente, lo que supone un riesgo para el Centro de notificaciones, ya que un usuario podría obtener acceso para enviar notificaciones no autorizadas a los clientes.

Lo habitual es enviar notificaciones mediante un servidor back-end. En algunos casos, puede que quiera enviar notificaciones push directamente desde la aplicación cliente. En esta sección se explica cómo enviar notificaciones desde el cliente mediante la [API de REST del Centro de notificaciones de Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. En la vista de proyecto de Android Studio, expanda **App** > **src** > **main** > **res** > **layout**. Abra el archivo de diseño `activity_main.xml` y haga clic en la pestaña **Texto** para actualizar el contenido de texto del archivo. Actualícelo con el siguiente código, que agrega nuevos controles `Button` y `EditText` para enviar mensajes de notificación push al Centro de notificaciones. Agregue este código al final, inmediatamente antes de `</RelativeLayout>`.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. En la Vista de proyecto de Android Studio, expanda **App** > **src** > **main** > **res** > **values**. Abra el archivo `strings.xml` y agregue los valores de cadena a los que hacen referencia los nuevos controles `Button` y `EditText`. Agréguelos al final del archivo, justo antes de `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. En el archivo `NotificationSetting.java`, agregue la siguiente configuración a la clase `NotificationSettings`.

	Actualice `HubFullAccess` con la cadena de conexión **DefaultFullSharedAccessSignature** de su centro. Esta cadena de conexión se puede copiar desde el [Portal de Azure]. Para ello, en la hoja **Configuración** del Centro de notificaciones, haga clic en **Directivas de acceso**.

		public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. En el archivo `MainActivity.java`, agregue las siguientes instrucciones `import` antes de la clase `MainActivity`.

		import java.io.BufferedOutputStream;
		import java.io.BufferedReader;
		import java.io.InputStreamReader;
		import java.io.OutputStream;
		import java.net.HttpURLConnection;
		import java.net.URL;
		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

6. En el archivo `MainActivity.java`, agregue los siguientes miembros al principio de la clase `MainActivity`.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;

6. Debe crear un token de firma de acceso a software (SaS) para autenticar una solicitud POST para enviar mensajes a su centro de notificaciones. Para ello, analice los datos de clave de la cadena de conexión y, luego, cree el token SaS como se menciona en la referencia de API de REST de [Conceptos comunes](http://msdn.microsoft.com/library/azure/dn495627.aspx). El código siguiente es un ejemplo de implementación.

	En `MainActivity.java`, agregue el método siguiente a la clase `MainActivity` para analizar la cadena de conexión.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
	     * to parse the connection string so a SaS authentication token can be
	     * constructed.
	     *
	     * @param connectionString This must be the DefaultFullSharedAccess connection
	     *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }


7. En `MainActivity.java`, agregue el método siguiente a la clase `MainActivity` para crear un token de autenticación de SaS.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
	     * construct a SaS token from the access key to authenticate a request.
	     *
	     * @param uri The unencoded resource URI string for this operation. The resource
	     *            URI is the full URI of the Service Bus resource to which access is
	     *            claimed. For example,
	     *            "http://<namespace>.servicebus.windows.net/<hubName>"
	     */
	    private String generateSasToken(String uri) {
	
	        String targetUri;
	        String token = null;
	        try {
	            targetUri = URLEncoder
	                    .encode(uri.toString().toLowerCase(), "UTF-8")
	                    .toLowerCase();
	
	            long expiresOnDate = System.currentTimeMillis();
	            int expiresInMins = 60; // 1 hour
	            expiresOnDate += expiresInMins * 60 * 1000;
	            long expires = expiresOnDate / 1000;
	            String toSign = targetUri + "\n" + expires;
	
	            // Get an hmac_sha1 key from the raw key bytes
	            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
	            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
	
	            // Get an hmac_sha1 Mac instance and initialize with the signing key
	            Mac mac = Mac.getInstance("HmacSHA256");
	            mac.init(signingKey);
	
	            // Compute the hmac on input data bytes
	            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
	
	            // Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
	            String signature = URLEncoder.encode(
	                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
	
	            // Construct authorization string
	            token = "SharedAccessSignature sr=" + targetUri + "&sig="
	                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
	        } catch (Exception e) {
	            if (isVisible) {
	                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
	            }
	        }
	
	        return token;
	    }



8. En `MainActivity.java`, agregue el método siguiente a la clase `MainActivity` para controlar si se hace clic en el botón **Enviar notificación** y enviar el mensaje de notificación push al centro mediante la API de REST integrada.

	    /**
	     * Send Notification button click handler. This method parses the
	     * DefaultFullSharedAccess connection string and generates a SaS token. The
	     * token is added to the Authorization header on the POST request to the
	     * notification hub. The text in the editTextNotificationMessage control
	     * is added as the JSON body for the request to add a GCM message to the hub.
	     *
	     * @param v
	     */
	    public void sendNotificationButtonOnClick(View v) {
	        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
	        final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
	
	        new Thread()
	        {
	            public void run()
	            {
	                try
	                {
	                    // Based on reference documentation...
	                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
	                    ParseConnectionString(NotificationSettings.HubFullAccess);
	                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
	                            "/messages/?api-version=2015-01");
	
	                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	
	                    try {
	                        // POST request
	                        urlConnection.setDoOutput(true);
	
	                        // Authenticate the POST request with the SaS token
	                        urlConnection.setRequestProperty("Authorization", 
								generateSasToken(url.toString()));
	
	                        // Notification format should be GCM
	                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
	
	                        // Include any tags
	                        // Example below targets 3 specific tags
	                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
	                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
							//		"tag1 || tag2 || tag3");
	
	                        // Send notification message
	                        urlConnection.setFixedLengthStreamingMode(json.length());
	                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
	                        bodyStream.write(json.getBytes());
	                        bodyStream.close();
	
	                        // Get reponse
	                        urlConnection.connect();
	                        int responseCode = urlConnection.getResponseCode();
	                        if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
	                        }
	                    } finally {
	                        urlConnection.disconnect();
	                    }
	                }
	                catch(Exception e)
	                {
	                    if (isVisible) {
	                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
	                    }
	                }
	            }
	        }.start();
	    }



##Prueba de la aplicación

####Notificaciones push en el emulador

Si desea probar notificaciones push en un emulador, asegúrese de que la imagen del emulador admita el nivel de API de Google que elija para la aplicación. Si la imagen no es compatible con las API de Google nativas, aparecerá la excepción **SERVICE\_NOT\_AVAILABLE**.

Compruebe, además, que ha agregado la cuenta de Google al emulador en ejecución en **Configuración** > **Cuentas**. De lo contrario, sus intentos de registrarse con GCM podrían generar la excepción **AUTHENTICATION\_FAILED**.

####Ejecución de la aplicación

1. Ejecute la aplicación y observe que el Id. de registro se informa para un registro correcto.

   	![Prueba en Android: registro de canal](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Escriba un mensaje de notificación que se enviará a todos los dispositivos Android registrados con el centro.

   	![Prueba en Android - envío de un mensaje](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Presione **Enviar notificación**. En los dispositivos en los que se ejecute la aplicación, aparecerá una instancia de `AlertDialog` con el mensaje de notificación push. Los dispositivos en que la aplicación no esté en ejecución, pero que anteriormente se hayan registrado para recibir notificaciones push, recibirán una notificación en el administrador de notificaciones de Android. Para verlas, deslice el dedo hacia abajo desde la esquina superior izquierda.

   	![Prueba en Android - notificaciones](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##Pasos siguientes

Se recomienda seguir el tutorial [Notificación a los usuarios con los Centros de notificaciones de Azure] como paso siguiente. Le mostrará cómo enviar notificaciones desde un back-end de ASP.NET mediante etiquetas para dirigirse a usuarios específicos.

Si desea segmentar los usuarios por grupos de interés, consulte el tutorial [Uso de Notification Hubs para enviar noticias de última hora].

Para más información sobre los Centros de notificaciones, consulte [Centros de notificaciones de Azure].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Centros de notificaciones de Azure]: notification-hubs-push-notification-overview.md
[Notificación a los usuarios con los Centros de notificaciones de Azure]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Uso de Notification Hubs para enviar noticias de última hora]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0907_2016-->