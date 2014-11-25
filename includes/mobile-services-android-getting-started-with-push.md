1.  Abra el archivo `AndroidManifest.xml`. En el código que aparece en los próximos dos pasos, reemplace _`**my_app_package**`_ por el nombre del paquete de aplicaciones para el proyecto, que es el valor del atributo `package` de la etiqueta `manifest`.

2.  Agregue los siguientes permisos nuevos después del elemento `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Agregue el siguiente código después de la etiqueta de apertura `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  Descargue y descomprima el [SDK de Android para Servicios móviles (en inglés)][SDK de Android para Servicios móviles (en inglés)], abra la carpeta **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto Eclipse y actualice la carpeta *libs*.

    <div class="dev-callout"><b>Nota:</b>
<p>Los n&uacute;meros que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.</p>
</div>

5.  Abra el archivo *ToDoItemActivity.java* y agregue la siguiente instrucción de importación:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Agregue la siguiente variable privada a la clase, donde *`<PROJECT_NUMBER>`* es el número de proyecto que Google asigna a su aplicación en el procedimiento anterior:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  En el método **onCreate**, antes de crear una instancia del MobileServiceClient, agregue este código que registra el controlador de notificación para el dispositivo:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Más adelante definiremos MyHandler.class, al que se hace referencia en este código.

8.  En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo** y después en **Clase**.

9.  En **Nombre**, escriba `MyHandler`, en **Superclase** escriba `com.microsoft.windowsazure.notifications.NotificationsHandler` y, a continuación, haga clic en **Finalizar**.

    ![][0]

    Con esto se crea la clase MyHandler.

10. Agregue las siguientes instrucciones para la clase `MyHandler`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. A continuación, agregue los miembros siguientes para la clase `MyHandler`:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. Enla clase `MyHandler`, agregue el siguiente código para invalidar el método **onRegistered**: el cual registra el dispositivo con el Centro de notificaciones del servicio móvil.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. En la clase `MyHandler`, agregue el siguiente código para invalidar el método **onReceive**, que ocasiona que se visualice la notificación al recibirla.

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.



  [SDK de Android para Servicios móviles (en inglés)]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
