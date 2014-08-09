<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

Introducción a los Centros de notificaciones
============================================

[C\# de Tienda Windows](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/es-es/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/es-es/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/es-es/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/es-es/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Este tema muestra cómo puede usar Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación de Android. En este tutorial, puede crear una aplicación de Android en blanco que reciba notificaciones de inserción mediante el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

-   [Habilitación del servicio de mensajería en la nube de Google](#register)
-   [Configuración de su Centro de notificaciones](#configure-hub)
-   [Conexión de su aplicación al Centro de notificaciones](#connecting-app)
-   [Envío de una notificación a la aplicación](#send)
-   [Prueba de la aplicación](#run-app)

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para saber cómo usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos.

Este tutorial requiere lo siguiente:

-   El SDK de Android (se presupone que usará Eclipse), que puede descargarse desde [aquí](http://go.microsoft.com/fwlink/?LinkId=389797)
-   El [SDK de Android para Servicios móviles](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)

Completar este tutorial es un requisito previo para todos los tutoriales de centros de notificaciones para aplicaciones de Android.

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Habilitación del servicio de mensajería en la nube de Google
------------------------------------------------------------

[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A continuación, utilizará este valor de clave de API para permitir que el centro de notificaciones se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

Configuración de su Centro de notificaciones
--------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y, a continuación, haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

	![][7]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

	![][8]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en **Configure** en la parte superior.

	![][9]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

	![][10]

6.  Haga clic en la pestaña **Configure** en la parte superior, escriba el valor de **API Key** obtenido en la sección anterior y, a continuación, haga clic en **Save**.

	![][11]

7.  Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **View Connection String**. Anote las dos cadenas de conexión.

Su centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones de inserción.

Conexión de su aplicación al Centro de notificaciones
-----------------------------------------------------

### Creación de un nuevo proyecto de Android

1.  En Eclipse ADT, cree un nuevo proyecto de Android (File, New y Android Application).

	![][13]

2.  Asegúrese de que **Minimum Required SDK** se establezca en *API 8: Android 2.2 (Froyo)* y que las próximas entradas del SDK se establezcan en la última versión disponible. Seleccione Next, y siga el asistente asegurándose de que **Create activity** se encuentra seleccionado para crear una actividad en blanco. Acepte el icono del selector predeterminado en la siguiente casilla y haga clic en **Finish** en la última.

	![][14]

### Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Incorporación de código

1.  Descargue el SDK para Android de Centros de notificaciones desde [aquí](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409). Extraiga el archivo .zip y copie el archivo notificationhubs\\notification-hubs-0.1.jar en el directorio \\libs del proyecto en el Explorador de paquetes.

2.  Descargue y descomprima el [SDK de Android para Servicios móviles (en inglés)](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409), abra la carpeta **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto Eclipse y actualice la carpeta *libs*.

    **Nota:**

    Los números que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.

    Ahora, configure la aplicación para obtener un valor de *registrationId* desde GCM y úselo para registrar la instancia de la aplicación en el centro de notificaciones.

3.  En el archivo AndroidManifest.xml file, agregue la siguiente línea debajo del elemento &lt;uses-sdk/\>. Asegúrese de reemplazar `<your package>` por el paquete seleccionado para la aplicación en el paso 1 (`com.yourCompany.wams_notificationhubs` de este ejemplo).

         <uses-permission android:name="android.permission.INTERNET"/>
         <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
         <uses-permission android:name="android.permission.WAKE_LOCK"/>
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

         <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
         <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  En la clase **MainActivity**, agregue las siguientes instrucciones.

         import android.os.AsyncTask;    
         import com.google.android.gms.gcm.*;
         import com.microsoft.windowsazure.messaging.*;
         import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  Agregue los siguientes miembros privados en la parte superior de la clase.

    **Nota:**

    Asegúrese de establecer SENDER\_ID en el número de proyecto que obtuvo anteriormente.

         private String SENDER_ID = "<your project number>";
         private GoogleCloudMessaging gcm;
         private NotificationHub hub;

6.  En el método **OnCreate**, agregue el siguiente código y asegúrese de reemplazar los marcadores de posición por la cadena de conexión con el acceso de escucha obtenido en la sección anterior y el nombre del centro de notificaciones que aparece en la parte superior de la pantalla de la página en Azure para el centro (**no** la dirección URL completa).

         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

         gcm = GoogleCloudMessaging.getInstance(this);
            
         String connectionString = "<your listen access connection string>";
         hub = new NotificationHub("<your notification hub name>", connectionString, this);
            
         registerWithNotificationHubs();

7.  En MainActivity.java, cree el siguiente método:

         @SuppressWarnings("unchecked")
         private void registerWithNotificationHubs() {
            new AsyncTask() {
               @Override
               protected Object doInBackground(Object... params) {
                  try {
                     String regid = gcm.register(SENDER_ID);
                     hub.register(regid);
                  } catch (Exception e) {
                     return e;
                  }
                  return null;
              }
            }.execute(null, null, null);
         }

8.  Puesto que Android no muestra las notificaciones, debe escribir su propio receptor. En **AndroidManifest.xml**, agregue el siguiente elemento dentro del elemento `<application/>`.

    **Nota:**

    Reemplace el marcador de posición por el nombre del paquete.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

9.  En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo`src`), haga clic en **Nuevo** y luego en **Clase**.

10. En **Nombre**, escriba `MyHandler`, en **Superclase**, escriba `com.microsoft.windowsazure.notifications.NotificationsHandler` y luego haga clic en **Finalizar**

    ![](./media/notification-hubs-android-get-started/notification-hub-android-new-class.png)

    Con esto se crea la clase MyHandler.

11. Agregue las siguientes instrucciones de importación:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. Agregue el siguiente código a la clase:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

            
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Demostración del centro de notificaciones")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

Envío de una notificación a la aplicación
-----------------------------------------

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz de REST](http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx). En este tutorial mostraremos dos formas de enviar notificaciones: con una aplicación de consola .NET y con un Servicio móvil mediante un script de Node.

### Para enviar notificaciones mediante una aplicación de consola .NET:

1.  Cree una aplicación de consola nueva de Visual C\#:

 	![][20]

2.  Agregue una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet](http://nuget.org/packages/WindowsAzure.ServiceBus/). En el menú principal de Visual Studio, haga clic en **Tools**, luego en **Library Package Manager** y finalmente en **Package Manager Console**. Posteriormente, en la ventana de la consola, escriba:

         Install-Package WindowsAzure.ServiceBus

    y presione Entrar.

3.  Abra el archivo Program.cs y agregue la siguiente instrucción using:

         using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método.

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{  private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }
        quot;data private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }
        quot; : { private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }
        quot;msg private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }
        quot;: private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }
        quot;Hello from Azure! private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
         }
        quot;}}");
         }

5.  Posteriormente, agregue las siguientes líneas al método Main:

          SendNotificationAsync();
          Console.ReadLine();

### Para enviar una notificación mediante un Servicio móvil

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) y seleccione su Servicio móvil. Si no dispone aún de un servicio móvil, consulte [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started/#create-new-service).

2.  Seleccione la pestaña **Scheduler** en la parte superior.

	![][22]

3.  Cree un nuevo trabajo programado, inserte un nombre y, a continuación, seleccione **On demand**.

	![][23]

4.  Cuando se cree el trabajo, haga clic en el nombre del trabajo. A continuación, haga clic en la pestaña **Script** de la barra superior.

5.  Inserte el siguiente script en su función de programador. Asegúrese de reemplazar los marcadores de posición por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente. Haga clic en **Save**.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
         notificationHubService.gcm.send(null,'{"data":{"msg" : "Bienvenido a Servicios móviles"}}',

    function (error) {

             if (!error) {
                console.warn("Notificación correcta");
             }
             else
             {
               console.warn("Notification errónea" + error);
             }
           }
         );

Prueba de la aplicación
-----------------------

Para realizar una prueba de la aplicación con un teléfono real, conéctelo al equipo mediante un cable USB.

Para realizar una prueba de esta aplicación con el emulador:

1.  Asegúrese de que usa un dispositivo virtual de Android (AVD) que sea compatible con API de Google.

2.  En **Ventana**, haga clic en **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y, a continuación, haga clic en **Editar**.

	![][18]

3.  Seleccione **API de Google** en **Destino** y haga clic en **Aceptar**.

	![][19]

4.  Para recibir notificaciones de inserción, debe configurar la cuenta de Google en el dispositivo virtual de Android (en el emulador, diríjase a **Settings** y haga clic en **Add Account**). Asegúrese también de que el emulador disponga de conexión a Internet.

Realice las siguientes tareas independientemente del dispositivo que seleccione:

1.  En la barra de herramientas superior de Eclipse, haga clic en **Run** y, a continuación, seleccione la aplicación. De esa forma, se carga la aplicación en el teléfono vinculado o se inicia el emulador y se carga y ejecuta la aplicación.

2.  La aplicación recupera el *registrationId* de GCM y efectúa el registro con el centro de notificaciones.

3.  Ahora use uno de los métodos en la sección anterior para enviar una notificación a la aplicación:

    -   Si usa una aplicación de la consola .Net, presione la tecla F5 en Visual Studio para ejecutar la aplicación, que enviará una notificación.
    -   En caso contrario, si está usando un script de Servicios móviles, haga clic en **Run Once** en la barra inferior de la pantalla del servicio móvil y el script enviará una notificación.

4.  Aparecerá un icono en la zona de notificaciones (esquina superior izquierda). Despliegue el ciadro de notificaciones para ver la notificación.

	![][21]

Pasos siguientes
----------------

En este sencillo ejemplo, difunde notificaciones a todos los dispositivos de Android. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios](/es-es/manage/services/notification-hubs/notify-users-aspnet), mientras que si desea dividir los usuarios por grupos de interés, puede leer [Uso de Centros de notificaciones para enviar noticias de último minuto](/es-es/manage/services/notification-hubs/breaking-news-dotnet). Para obtener más información sobre el uso de los Centros de notificaciones, consulte la [información general acerca de los centros de notificaciones de Azure](http://msdn.microsoft.com/es-es/library/jj927170.aspx) y los [procedimientos de los Centros de notificaciones para Android](http://msdn.microsoft.com/es-es/library/dn282661.aspx) (en inglés).

<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /es-es/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript and HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/es-es/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /es-es/manage/services/notification-hubs/breaking-news-dotnet