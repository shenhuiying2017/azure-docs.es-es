<properties 
	pageTitle="Introducción a los Centros de notificaciones de Azure" 
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure para las notificaciones de inserción." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="wesmc"/>

# Introducción a los Centros de notificaciones

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Información general

Este tema muestra cómo puede usar Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación de Android. En este tutorial, puede crear una aplicación de Android en blanco que reciba notificaciones de inserción mediante el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones.

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para saber cómo usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos.


##Requisitos previos

Este tutorial requiere lo siguiente:

+ Android Studio, que puede descargar <a href="http://go.microsoft.com/fwlink/?LinkId=389797">aquí</a>
+ para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F).


Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones Android.


##Creación de un proyecto que admite Servicio de mensajería en la nube de Google

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]


##Configuración de un centro de notificaciones nuevo

[AZURE.INCLUDE [notification-hubs-android-configure-push](../../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

###Creación de un nuevo proyecto de Android

1. En Android Studio, inicie un nuevo proyecto de Android Studio.

   	![][13]

2. Elija el factor de forma **Teléfono y tableta** y el **SDK mínimo** que desea admitir. A continuación, haga clic en **Siguiente**.

   	![][14]

3. Elija **Actividad en blanco** como la actividad principal. Haga clic en **Siguiente** y, a continuación, en **Finalizar**.

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../../includes/mobile-services-add-Google-play-services.md)]

###Incorporación de código

1. Descargue el SDK para Android de Centros de notificaciones desde <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">aquí</a>. Extraiga el archivo .zip y copie **notificationhubs\\notification-hubs-0.4.jar** y **notifications\\notifications-1.0.1.jar** en el directorio **app\\libs** del proyecto. Para hacerlo, arrastre los archivos directamente a la carpeta **libs** en la ventana Vista del proyecto de Android Studio. Actualice la carpeta libs.



	La documentación de referencia de estos dos paquetes se encuentran en los vínculos siguientes: * [com.microsoft.windowsazure.messaging](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/messaging/package-summary.html) * [com.microsoft.windowsazure.notifications](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/notifications/package-summary.html)


    > [AZURE.NOTE]Los números que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.

2. A continuación, configurará la aplicación para obtener un identificador de registro desde GCM y lo usará para registrar la instancia de la aplicación en el centro de notificaciones.

	En el archivo AndroidManifest.xml, agregue los siguientes permisos debajo de la etiqueta `</application>`. Asegúrese de reemplazar `<your package>` por el nombre del paquete que aparece en la parte superior del archivo AndroidManifest.xml (`com.example.testnotificationhubs` en este ejemplo).

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. En la clase **MainActivity**, agregue las siguientes instrucciones `import` sobre la declaración de clase.

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


4. Agregue los siguientes miembros privados en la parte superior de la clase.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";


	Asegúrese de actualizar los tres marcadores de posición: * **SENDER_ID**: defina `SENDER_ID` en el número de proyecto que obtuvo anteriormente desde el proyecto que creó en la [Consola de la nube de Google](http://cloud.google.com/console). * **HubListenConnectionString**: defina `HubListenConnectionString` en la cadena de conexión **DefaultListenAccessSignature** correspondiente a su centro. Puede copiar esa cadena de conexión con un clic en **Ver cadena de conexión** en la pestaña **Panel** del centro en el [Portal de administración de Azure]. * **HubName**: el nombre del centro de notificaciones que aparece en la parte superior de la página en Azure correspondiente a su centro (**no** la dirección URL completa). Por ejemplo, `"myhub"`.



5. En el método **OnCreate** de la clase **MainActivity**, agregue el código siguiente para realizar el registro sobre la creación de la actividad.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. En **MainActivity.java**, agregue el código siguiente para el método **registerWithNotificationHubs()**. Este método informa el éxito después del registro con el Servicio de mensajería en la nube de Google y el Centro de notificaciones:

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " + 
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}

		
		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when theres an exception or message to report.
		  * 
		  * @param title   Title for the AlertDialog box. 
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE, 
						(CharSequence) "OK", 
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

7. Puesto que Android no muestra las notificaciones, debe escribir su propio receptor. En **AndroidManifest.xml**, agregue el siguiente elemento dentro del elemento `<application>`.

	> [AZURE.NOTE]Reemplace el marcador de posición por el nombre del paquete.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


8. En la vista de proyecto, expanda **app** -> **src** -> **main** -> **java**. Haga clic con el botón derecho en la carpeta del paquete en **java**, haga clic en **Nuevo** y, a continuación, haga clic en **Clase Java**.

	![][6]

9. En el campo **Nombre** del nuevo tipo de clase **MyHandler**, haga clic en **Aceptar**.
	

10. Agregue las siguientes instrucciones import en la parte superior de **MyHandler.java**:

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

11. Actualice la declaración de clase de la siguiente manera para crear `MyHandler` una subclase de `com.microsoft.windowsazure.notifications.NotificationsHandler`, tal como se muestra a continuación.

		public class MyHandler extends NotificationsHandler {


12. Agregue el siguiente código para la clase `MyHandler`.

	Este código reemplaza el método `OnReceive`, por lo que el controlador generará una ventana emergente con `AlertDialog` para mostrar las notificaciones recibidas. El controlador también envía la notificación al administrador de notificaciones de Android con el método `sendNotification()`.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
        	mainActivity.DialogNotify("Received Notification",nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}
	
13. En la barra de menús de Android Studio, haga clic en **Compilar** -> **Recompilar proyecto** para asegurarse de que no se detectan errores.

##Envío de notificaciones



Para probar la recepción de notificaciones en su aplicación, envíe notificaciones en el Portal de Azure usando la pestaña de depuración en el centro de notificaciones, tal como se muestra en la pantalla que aparece a continuación.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

![][31]

1. En la vista de proyecto de Android Studio, expanda **App**->**src**->**main**->**res**->**layout**. Abra el archivo de diseño **activity_main.xml** y haga clic en la pestaña **Texto** para actualizar los contenidos de texto del archivo. Actualícelo con el siguiente código, el que agrega un nuevo control `Button` y `EditText` para enviar mensajes de notificación al centro de notificaciones. Agregue este código en la parte inferior, justo antes de `</RelativeLayout>`.

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

2. En la vista de proyecto de Android Studio, expanda **App** -> **src** -> **main** -> **res** -> **values**. Abra el archivo **strings.xml** y agregue los valores de cadena a los que los nuevos controles `Button` y `EditText` hacen referencia. Agréguelos en la parte inferior del archivo, justo antes de `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. En el archivo **MainActivity.java**, agregue las siguientes instrucciones `import` sobre la clase `MainActivity`.

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;
		
		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


3. En el archivo **MainActivity.java**, agregue los siguientes miembros en la parte superior de la clase `MainActivity`.

	Escriba el nombre del centro en `HubName`, no el espacio de nombres. Por ejemplo, "myhub". Además, escriba la cadena de conexión **DefaultFullSharedAccessSignature**. Esta cadena de conexión se puede copiar desde el [Portal de administración de Azure] mediante un clic en **Ver cadena de conexión** en la pestaña **Panel** para el centro de notificaciones.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. La actividad contiene el nombre del centro y la cadena de conexión de acceso compartido correspondiente al centro. Debe crear un token de firma de acceso a software (SaS) para autenticar una solicitud POST para enviar mensajes a su centro de notificaciones. Para hacerlo, analice los datos de clave desde la cadena de conexión y, a continuación, cree el token SAS como se menciona en la referencia API de REST de [Conceptos comunes](http://msdn.microsoft.com/library/azure/dn495627.aspx).

	En **MainActivity.java**, agregue el método siguiente a la clase `MainActivity` para analizar la cadena de conexión.

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

5. En **MainActivity.java**, agregue el método siguiente a la clase `MainActivity` para crear un token de autenticación SaS.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         * 
         * @param uri The un-encoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example, 
         *            "http://<namespace>.servicebus.windows.net/<hubName>" 
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
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
	            // Apache commons codec.
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }
    
            return null;
        }


6. En **MainActivity.java**, agregue el método siguiente a la clase `MainActivity` para controlar el botón **Enviar notificación** y envíe el mensaje de notificación al centro mediante la API de REST.

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
                        HttpClient client = new DefaultHttpClient();
    
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);
    
                        // Authenticate the POST request with the SaS token.
                        post.setHeader("Authorization", generateSasToken(url));
    
                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");
    
                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));
    
                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##Prueba de la aplicación

####Pruebas de emulador
Si desea probar en un emulador, asegúrese de que la imagen del emular admita el nivel de API de Google que elija para la aplicación. Si la imagen no es compatible con las API de Google, recibirá la excepción **SERVICE_NOT_AVAILABLE**.

Asegúrese también de haber agregado su cuenta de Google al emulador en ejecución en **Configuración**->**Cuentas**. De lo contrario, sus intentos de registrarse con GCM podrían generar la excepción **AUTHENTICATION_FAILED**.

####Prueba de la aplicación     

1. Ejecute la aplicación y observe que el identificador de registro se informa para un registro correcto.

   	![][18]

2. Escriba un mensaje de notificación que se enviará a todos los dispositivos Android registrados con el centro.

   	![][19]

3. Presione **Enviar notificación**. Los dispositivos que tengan la aplicación en ejecución mostrarán un `AlertDialog` con el mensaje de notificación. Los dispositivos que no tengan la aplicación en ejecución, pero que anteriormente se registraron para recibir las notificaciones, recibirán una notificación agregada al administrador de notificaciones. Para ver las notificaciones, deslice el dedo hacia abajo desde la esquina superior izquierda.

   	![][21]

##Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Notificación a los usuarios con los Centros de notificaciones de Azure], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Obtenga más información sobre el uso de Centros de notificaciones en la [Orientación sobre los Centros de notificaciones].


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Notificación a los usuarios con los Centros de notificaciones de Azure]: notification-hubs-aspnet-backend-android-notify-users.md
[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-aspnet-backend-android-breaking-news.md
 

<!---HONumber=July15_HO3-->