<properties pageTitle="Introducción a los Centros de notificaciones de Azure" description="Aprenda a usar los Centros de notificaciones de Azure para las notificaciones de inserción." services="notification-hubs" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>
# Introducción a los Centros de notificaciones

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/es-es/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/es-es/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tema muestra cómo puede usar Centros de notificaciones de Azure para enviar notificaciones de inserción a una aplicación de Android. 
En este tutorial, puede crear una aplicación de Android en blanco que reciba notificaciones de inserción mediante el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, podrá difundir notificaciones de inserción a todos los dispositivos que ejecutan su aplicación usando su Centro de notificaciones.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Habilitación del servicio de mensajería en la nube de Google](#register)
* [Configuración del Centro de notificaciones](#configure-hub)
* [Conexión de la aplicación al Centro de notificaciones](#connecting-app)
* [Envío de una notificación a la aplicación](#send)
* [Prueba de la aplicación](#run-app)

En este tutorial se demuestra el escenario de difusión sencillo con Centros de notificaciones. Asegúrese de seguirlo junto con el próximo tutorial para saber cómo usar los Centros de notificaciones para abordar usuarios y grupos de dispositivos específicos. 

Este tutorial requiere lo siguiente:

+ El SDK de Android (se presupone que usará Eclipse), que puede descargarse desde <a href="http://go.microsoft.com/fwlink/?LinkId=389797">aquí</a>
+ el [SDK de Android para Servicios móviles]

Completar este tutorial es un requisito previo para todos los tutoriales de Centros de notificaciones para aplicaciones Android. 

> [AZURE.NOTE] para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure.](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target="_blank")

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google

[AZURE.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A continuación, utilizará este valor de clave de API para permitir que su Centro de notificaciones se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

##<a id="configure-hub"></a>Configuración del Centro de notificaciones

1. Inicie sesión en el [Portal de administración de Azure] y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de notificaciones** y, a continuación, en **Creación rápida**.

   	![][7]

3. Escriba un nombre para su Centro de notificaciones, seleccione la región deseada y, a continuación, haga clic en **Crear un nuevo Centro de notificaciones**.

   	![][8]

4. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del Centro de notificaciones*-ns**) y, a continuación, haga clic en **Configurar** en la parte superior.

   	![][9]

5. Haga clic en la pestaña **Centros de notificaciones** en la parte superior y, a continuación, haga clic en el Centro de notificaciones que acaba de crear.

   	![][10]

6. Haga clic en la pestaña **Configurar** que aparece en la parte superior, escriba el valor **Clave de API** que obtuvo en la sección anterior y, a continuación, haga clic en **Guardar**.

   	![][11]

7. Seleccione la pestaña **Panel** en la parte superior y, a continuación, haga clic en **Ver cadena de conexión**. Anote las dos cadenas de conexión.


Su Centro de notificaciones está ahora configurado para funcionar con GCM y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones de inserción.

##<a id="connecting-app"></a>Conexión de la aplicación al Centro de notificaciones

###Creación de un nuevo proyecto de Android

1. En Eclipse ADT, cree un nuevo proyecto de Android (File, New y Android Application).

   	![][13]

2. Asegúrese de que el **SDK mínimo requerido** está establecido en *API 8: Android 2.2 (Froyo)*, y que las dos entradas SDK se establecen en la última versión disponible. Seleccione Siguiente y siga el asistente, asegurándose de que **Crear actividad** se encuentre seleccionado para crear una actividad en blanco. Acepte el icono del selector predeterminado en la siguiente casilla y haga clic en **Finalizar** en la última.

   	![][14]

###Incorporación de Google Play Services al proyecto

[AZURE.INCLUDE [Incorporación de Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Incorporación de código

1. Descargue el SDK para Android de Centros de notificaciones desde <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">aquí</a>. Extraiga el archivo .zip y copie el archivo notificationhubs\notification-hubs-0.1.jar en el directorio \libs del proyecto en el Explorador de paquetes.

2. Descargue y descomprima el [SDK de Android para Servicios móviles], abra la carpeta de **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto de Eclipse y actualice la carpeta  *libs*.

    > [AZURE.NOTE] Los números que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.

	Ahora, configure la aplicación para obtener un valor de  *registrationId* desde GCM y úselo para registrar la instancia de la aplicación en el Centro de notificaciones.

3. En el archivo AndroidManifest.xml, agregue la siguiente línea justo debajo del elemento <uses-sdk/>. Asegúrese de reemplazar `<your package>` por el paquete seleccionado para la aplicación en el paso 1 (`com.yourCompany.wams_notificationhubs` de este ejemplo).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<su paquete>.permission.C2D_MESSAGE"/>

4. En la clase **MainActivity**, agregue las siguientes instrucciones.

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Agregue los siguientes miembros privados en la parte superior de la clase.

	> [AZURE.NOTE] Asegúrese de establecer SENDER_ID en el número de proyecto que obtuvo anteriormente.

		private String SENDER_ID = "<su número de proyecto>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. En el método **OnCreate**, agregue el siguiente código y asegúrese de reemplazar los marcadores de posición por la cadena de conexión con el acceso de escucha obtenido en la sección anterior y el nombre del Centro de notificaciones que aparece en la parte superior de la pantalla de la página en Azure para el centro (**no** la dirección URL completa).

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<su cadena de conexión de acceso de escucha>";
		hub = new NotificationHub("<su nombre del Centro de notificaciones>", connectionString, this);
		
		registerWithNotificationHubs();

7. En MainActivity.java, cree el siguiente método:

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

8. Puesto que Android no muestra las notificaciones, debe escribir su propio receptor. En **AndroidManifest.xml**, agregue el siguiente elemento dentro del elemento `<application/>`.

	> [AZURE.NOTE] Reemplace el marcador de posición por el nombre del paquete.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. En el Explorador de paquetes, haga clic con el botón derecho en el paquete (en el nodo `src`), haga clic en **Nuevo** y después en **Clase**.

10. En **Nombre**, escriba `MyHandler`; en **Superclase**, escriba `com.microsoft.windowsazure.notifications.NotificationsHandler` y luego haga clic en **Finalizar**

	![][6]

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
	

##<a name="send"></a>Envío de una notificación a la aplicación

Puede enviar notificaciones mediante los Centros de notificaciones desde cualquier back-end que use la <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaz REST</a>. En este tutorial puede enviar notificaciones con una aplicación de consola .NET. Para ver un ejemplo de cómo enviar notificaciones desde un back-end de los Servicios móviles de Azure integrado en Centros de notificaciones, consulte **Introducción a las notificaciones de inserción en Servicios móviles** (back-end de [.NET](/es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [back-end de JavaScript](/es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  Para obtener un ejemplo de cómo enviar notificaciones mediante las API de REST, consulte **Uso de los Centros de notificaciones de Java y PHP** ([Java](/es-es/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/es-es/documentation/articles/notification-hubs-php-backend-how-to/)).

1. En Visual Studio, en el menú **Archivo**, seleccione **Nuevo** y luego **Proyecto...**, a continuación en **Visual C#** haga clic en **Windows** y **Aplicación de consola** y haga clic en **Aceptar**.  

   	![][20]

	Esto crea un nuevo proyecto de aplicación de consola.

2. En el menú **Herramientas**, haga clic en **Administrador de paquetes de biblioteca** y, a continuación, en **Consola del Administrador de paquetes**. 

	Esto muestra la Consola del Administrador de paquetes.

3. En la ventana de la consola, ejecute el siguiente comando:

        Install-Package WindowsAzure.ServiceBus
    
	Esta acción agrega una referencia al SDK de Bus de servicio de Azure con el <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">paquete WindowsAzure.ServiceBus de NuGet</a>. 

4. Abra el archivo Program.cs y agregue la siguiente instrucción  `using`:

        using Microsoft.ServiceBus.Notifications;

5. En la clase **Program**, agregue el siguiente método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Asegúrese de reemplazar el marcador de posición "nombre de centro" por el nombre del Centro de notificaciones que aparece en el portal en la pestaña **Centros de notificaciones**. Reemplace también el marcador de posición de la cadena de conexión por la cadena de conexión llamada **DefaultFullSharedAccessSignature** que obtuvo en la sección "Configuración del Centro de notificaciones". 

	>[AZURE.NOTE]Asegúrese de utilizar la cadena de conexión con acceso **Total**, no con acceso de **Escucha**. La cadena de acceso escuchar no tiene permisos para enviar notificaciones.

5. Después agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Prueba de la aplicación

Antes de probar la aplicación en un emulador, necesitará completar estos pasos de configuración del emulador (puede obviarlos si realizará las pruebas en un dispositivo físico):

1. Asegúrese de que usa un dispositivo virtual de Android (AVD) que sea compatible con API de Google.

2. En **Ventana**, haga clic en **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y, a continuación, haga clic en **Editar**.

   	![][18]

3. Seleccione **Google APIs** (API de Google) en **Target** (Destino) y, a continuación, haga clic en **OK** (Aceptar).

   	![][19]

4. 	Para recibir notificaciones de inserción, debe configurar la cuenta de Google en el dispositivo virtual de Android (en el emulador, diríjase a <strong>Settings</strong> (Configuración) y haga clic en <strong>Add Account</strong> [Agregar cuenta]). Además, asegúrese de que el emulador esté conectado a Internet.


Siga estos pasos para ejecutar la aplicación en un dispositivo o en un emulador:

1. En la barra de herramientas superior de Eclipse, haga clic en **Run** (Ejecutar) y, a continuación, seleccione la aplicación. 
 
	Esto iniciará el emulador (si se utiliza uno) y cargará y ejecutará la aplicación. La aplicación recupera el valor de  *registrationId* de GCM y se registra con el Centro de notificaciones.

3. En Visual Studio, presione la tecla F5 para ejecutar la aplicación de consola. 

	Se envía una notificación a su aplicación.  
 
5. Cuando aparezca un icono en el área de notificaciones (esquina superior izquierda), despliegue el cuadro de notificaciones para ver la notificación.  

   	![][21]

## <a name="next-steps"> </a>Pasos siguientes

En este sencillo ejemplo, difunde notificaciones a todos sus dispositivos Android. Para dirigirse a usuarios específicos, consulte el tutorial [Uso de Centros de notificaciones para insertar notificaciones en los usuarios], mientras que, si desea segmentar sus usuarios por grupos de interés, puede leer [Uso de los Centros de notificaciones para enviar noticias de última hora]. Obtenga más información sobre el uso de Centros de notificaciones en la [Orientación sobre los Centros de notificaciones].


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
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android
[Notificaciones de inserción para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-android
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript y HTML]: /es-es/develop/mobile/tutorials/get-started-with-push-js
[Referencia a un proyecto de biblioteca]: http://go.microsoft.com/fwlink/?LinkId=389800
[Portal de administración de Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Orientación sobre los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx

[Uso de Centros de notificaciones para insertar notificaciones en los usuarios]: /es-es/manage/services/notification-hubs/notify-users-aspnet
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet


<!--HONumber=45--> 
