<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación Android.
En este tutorial, creará una aplicación Android que recibirá notificaciones de inserción con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

-   [Habilitación del servicio de mensajería en la nube de Google][Habilitación del servicio de mensajería en la nube de Google]
-   [Configuración de su Centro de notificaciones][Configuración de su Centro de notificaciones]
-   [Conexión de su aplicación al Centro de notificaciones][Conexión de su aplicación al Centro de notificaciones]
-   [Envío de una notificación a la aplicación][Envío de una notificación a la aplicación]
-   [Prueba de la aplicación][Prueba de la aplicación]

En este tutorial se demuestra el escenario de difusión sencillo con centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para saber cómo usar los centros de notificaciones para abordar usuarios y grupos de dispositivos específicos.

Este tutorial requiere lo siguiente:

-   El SDK de Android (se presupone que usará Eclipse), que puede descargarse desde [aquí][aquí]
-   El [SDK de Android para Servicios móviles][SDK de Android para Servicios móviles]

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones Android.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <span id="register"></span></a>Habilitación del servicio de mensajería en la nube de Google

[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A continuación, utilizará este valor de clave de API para permitir que su centro de notificaciones se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

## <span id="configure-hub"></span></a>Configuración de su Centro de notificaciones

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure] y, a continuación, haga clic en **+NEW** en la parte inferior de la pantalla.

2.  Haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y finalmente en **Quick Create**.

    ![][0]

3.  Escriba un nombre para su centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Create a new Notification Hub**.

    ![][1]

4.  Haga clic en el espacio de nombres que acaba de crear (por lo general ***notification hub name*-ns**) y, a continuación, haga clic en **Configure** en la parte superior.

    ![][2]

5.  Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el centro de notificaciones que acaba de crear.

    ![][3]

6.  Haga clic en la pestaña **Configure**, escriba el valor de **API Key** obtenido de GCM en la sección anterior y, a continuación, haga clic en **Save**.

    ![][4]

7.  Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **View Connection String**. Anote las dos cadenas de conexión.

Su centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones de inserción.

## <span id="connecting-app"></span></a>Conexión de su aplicación al Centro de notificaciones

### Creación de un nuevo proyecto de Android

1.  En Eclipse ADT, cree un nuevo proyecto de Android (File, New y Android Application).

    ![][5]

2.  Asegúrese de que **Minimum Required SDK** se establezca en *API 8: Android 2.2 (Froyo)* y que las próximas entradas del SDK se establezcan en la última versión disponible. Seleccione Next, y siga el asistente asegurándose de que **Create activity** se encuentra seleccionado para crear una actividad en blanco. Acepte el icono del selector predeterminado en la siguiente casilla y haga clic en **Finish** en la última.

    ![][6]

### Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Incorporación de código

1.  Descargue el SDK para Android de Centros de notificaciones desde [aquí][SDK de Android para Servicios móviles]. Extraiga el archivo .zip y copie el archivo notificationhubs\\notification-hubs-0.1.jar en el directorio \\libs del proyecto en el Explorador de paquetes.

2.  Descargue y descomprima el [SDK de Android para Servicios móviles (en inglés)][SDK de Android para Servicios móviles], abra la carpeta **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto Eclipse y actualice la carpeta *libs*.

    <div class="dev-callout"><b>Nota:</b>
<p>Los n&uacute;meros que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.</p>
</div>

    Ahora, configure la aplicación para obtener un valor de *registrationId* desde GCM y úselo para registrar la instancia de la aplicación en el centro de notificaciones.

3.  En el archivo AndroidManifest.xml file, agregue la siguiente línea debajo del elemento <uses-sdk></uses-sdk>. Asegúrese de reemplazar `<your package>` por el paquete seleccionado para la aplicación en el paso 1 (`com.yourCompany.wams_notificationhubs` de este ejemplo).

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

    <div class="dev-callout"><b>Nota:</b>
<p>Aseg&uacute;rese de establecer SENDER_ID en el n&uacute;mero de proyecto que obtuvo anteriormente.</p>
</div>

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

    <div class="dev-callout"><b>Nota:</b>
<p>Reemplace el marcador de posici&oacute;n por el nombre del paquete.</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo** y después en **Clase**.

10. En **Nombre**, escriba `MyHandler`, en **Superclase** escriba `com.microsoft.windowsazure.notifications.NotificationsHandler` y, a continuación, haga clic en **Finalizar**.

    ![][7]

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
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

## <a name="send"></a>Envío de una notificación a la aplicación

Puede enviar notificaciones mediante los centros de notificaciones desde cualquier back-end que use la [interfaz REST][interfaz REST]. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un backend de los Servicios móviles de Azure integrado en centros de notificaciones, vea **Introducción a las notificaciones de inserción en Servicios móviles** ([.NET backend][.NET backend] | [JavaScript backend][.NET backend]). Para ver un ejemplo de cómo enviar notificaciones con REST API, vea **Uso de Centro de notificaciones desde Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego **Proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**.

    ![][8]

    Esto crea un nuevo proyecto de aplicación de consola.

2.  En el menú **Tools**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**.

    Esto muestra la Consola del Administrador de paquetes.

3.  En la ventana de la consola, ejecute el siguiente comando:

        Install-Package WindowsAzure.ServiceBus

    Esto agrega una referencia al SDK de Bus de servicio de Azure con el [paquete WindowsAzure.ServiceBus NuGet][paquete WindowsAzure.ServiceBus NuGet].

4.  Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.ServiceBus.Notifications;

5.  En la clase **Program**, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    Asegúrese de reemplazar el marcador de posición "nombre de centro" por nombre del centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Sustituya también el marcador de la cadena de conexión por la cadena de conexión llamada **DefaultListenSharedAccessSignature** que obtuvo en la sección "Configurar el centro de notificaciones".

    > [WACOM.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso **Escuchar**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

6.  Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>Prueba de la aplicación

Antes de probar la aplicación en un emulador, necesitará completar estos pasos de configuración del emulador (puede obviarlos si realizará las pruebas en un dispositivo físico):

1.  Asegúrese de que usa un dispositivo virtual de Android (AVD) que sea compatible con API de Google.

2.  En **Ventana**, haga clic en **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y, a continuación, haga clic en **Editar**.

    ![][9]

3.  Seleccione **Google APIs** en **Target** y, a continuación, haga clic en **OK**.

    ![][10]

4.  Para recibir notificaciones de inserción, debe configurar la cuenta de Google en el dispositivo virtual de Android (en el emulador, diríjase a **Settings** y haga clic en **Add Account**). Además, asegúrese de que el emulador esté conectado a Internet.

Siga estos pasos para ejecutar la aplicación en un dispositivo o en un emulador:

1.  En la barra de herramientas superior de Eclipse, haga clic en **Run** y, a continuación, seleccione la aplicación.

    Esto iniciará el emulador (si se utiliza uno) y cargará y ejecutará la aplicación. La aplicación recupera el valor de *registrationId* de GCM y se registra con el Centro de notificaciones.

2.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación de consola.

    Se envía una notificación a su aplicación.

3.  Cuando aparezca un icono en el área de notificaciones (esquina superior izquierda), despliegue el cuadro de notificaciones para ver la notificación.

    ![][11]

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios][Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora]. Obtenga más información sobre el uso de Centros de notificaciones en la [orientación sobre los Centros de notificaciones][orientación sobre los Centros de notificaciones] (en inglés).

 

  [Habilitación del servicio de mensajería en la nube de Google]: #register
  [Configuración de su Centro de notificaciones]: #configure-hub
  [Conexión de su aplicación al Centro de notificaciones]: #connecting-app
  [Envío de una notificación a la aplicación]: #send
  [Prueba de la aplicación]: #run-app
  [aquí]: http://go.microsoft.com/fwlink/?LinkId=389797
  [SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [interfaz REST]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
  [.NET backend]: /es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /es-es/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /es-es/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [paquete WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  [Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
  [orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
