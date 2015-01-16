<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Introducción a las notificaciones de inserción (Android) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación Android." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Introducción a las notificaciones de inserción en Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">C# para Tienda Windows</a>
    <a href="/es-es/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
    <a href="/es-es/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/es-es/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">Backend .NET</a> | <a href="/es-es/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">Backend JavaScript</a></div>

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

>[WACOM.NOTE]Este tema admite servicios móviles <em>existentes</em> que <em>todavía no se han actualizado</em> para usar la integración de Centros de notificaciones. Al crear un <em>servicio móvil</em> nuevo, esta funcionalidad integrada se habilita automáticamente. Para servicios móviles nuevos, consulte [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).
>
>La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala mejorada. <em>Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible</em>. Una vez que haya realizado la actualización, consulte esta versión de [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

* [Habilitación del servicio de mensajería en la nube de Google](#register)
* [Configuración de Servicios móviles](#configure)
* [Incorporación de notificaciones de inserción a la aplicación](#add-push)
* [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
* [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

+ [SDK de Android para Servicios móviles]
+ Una cuenta de Google activa

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a Servicios móviles]. 

##<a id="register"></a>Habilitación del servicio de mensajería en la nube de Google


[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A continuación, utilizará este valor de clave de API para permitir que Servicios móviles se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

##<a id="configure"></a>Configuración de Servicios móviles para enviar solicitudes de inserción

1. Inicie sesión en el [Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

   	![][18]

2. Haga clic en la pestaña **Insertar**, escriba el valor **Clave de API** obtenido de GCM en el procedimiento anterior y, a continuación, haga clic en **Guardar**.

   	![][19]

Su servicio móvil ahora está configurado para trabajar con GCM para enviar notificaciones de inserción.

##<a id="add-push"></a>Incorporación de notificaciones de inserción a la aplicación


###Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de servicios de reproducción](../includes/mobile-services-add-Google-play-services.md)]

###Incorporación de código

1. Abra el archivo de proyecto **AndroidManifest.xml**. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, los que debe cumplir la propiedad **minSdkVersion** del manifiesto. Consulte [Set Up Google Play Services SDK] para determinar el valor menor que se puede definir, en caso de que necesite definir un valor inferior a 16, debido a que se utiliza un dispositivo anterior. Defina la propiedad como corresponda.

2. Asegúrese de que en el elemento `uses-sdk`, la propiedad **targetSdkVersion** esté definida en el número de una plataforma de SDK que se ha instalado (paso 1). Es preferible definirla en la versión más reciente disponible. 

3. La etiqueta **uses-sdk** puede verse de la siguiente manera, dependiendo de las opciones que haya elegido en los pasos anteriores:

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. En el código que aparece en los próximos dos pasos, reemplace  _`**my_app_package**`_ por el nombre del paquete de aplicaciones para el proyecto, que es el valor del atributo `package` de la etiqueta `manifest`. 

5. Agregue los siguientes permisos nuevos después del elemento `uses-permission` existente:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6. Agregue el siguiente código después de la etiqueta de apertura `application`: 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>



7. Abra el archivo ToDoItem.java y agregue el siguiente código a la clase **TodoItem**:

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	This code creates a new property that holds the registration ID.

    <div class="dev-callout"><b>Nota:</b>
	<p>Cuando su servicio móvil tenga habilitado el esquema dinámico, una columna <strong>handle</strong> nueva se agregará automáticamente a la tabla <strong>TodoItem</strong> cuando se inserte un elemento nuevo que contenga esta propiedad.</p>
    </div>

8. Descargue y descomprima el [SDK de Android para Servicios móviles], abra la carpeta **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto Eclipse y actualice la carpeta *libs*.

    <div class="dev-callout"><b>Note</b>
	<p>The numbers at the end of the file name may change in subsequent SDK releases.</p>
    </div>

9.  Abra el archivo *ToDoItemActivity.java* y agregue la siguiente instrucción de importación:

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Agregue la siguiente variable privada a la clase, donde _`<PROJECT_NUMBER>`_ es el número de proyecto que Google ha asignado a su aplicación en el procedimiento anterior:

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. En el método **onCreate**, antes de crear una instancia del MobileServiceClient, agregue este código que registra el controlador de notificación para el dispositivo:

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. Agregue la siguiente línea de código al método **addItem**, antes de insertar el elemento en la tabla:

		item.setHandle(MyHandler.getHandle());

	Este código define la propiedad `handle` del elemento en el identificador de registro del dispositivo.

13. En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo** y, a continuación, en **Clase**.

14. En **Nombre**, escriba `MyHandler`, en **Superclase**, escriba `com.microsoft.windowsazure.notifications.NotificationsHandler` y, a continuación, haga clic en **Finalizar**

	![][6]

	This creates the new MyHandler class.

15. Agregue las siguientes instrucciones de importación:

		import android.content.Context;
		
		import com.microsoft.windowsazure.notifications.NotificationsHandler;
		

16. Agregue el siguiente código:

		@com.google.gson.annotations.SerializedName("handle")
		private static String mHandle;
	
		public static String getHandle() {
			return mHandle;
		}
	
		public static final void setHandle(String handle) {
			mHandle = handle;
		}
	

17. Reemplace el método **onRegistered** existente por el siguiente código:

		@Override
		public void onRegistered(Context context, String gcmRegistrationId) {
			super.onRegistered(context, gcmRegistrationId);
			
			setHandle(gcmRegistrationId);
		}
		

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.


##<a id="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

1. Nuevamente en el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoItem**. 

   	![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.
   
  	![][22]

   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

		function insert(item, user, request) {
			request.execute({
				success: function() {
					// Write to the response and then send the notification in the background
					request.respond();
					push.gcm.send(item.handle, item.text, {
						success: function(response) {
							console.log('Push notification sent: ', response);
						}, error: function(error) {
							console.log('Error sending push notification: ', error);
						}
					});
				}
			});
		}

   	Esto registra un nuevo script de inserción, que usa el [objeto gcm] para enviar una notificación de inserción (el texto insertado) al dispositivo indicado en la solicitud de inserción. 

##<a id="test"></a>Probar las notificaciones de inserción en su aplicación

<div class="dev-callout"><b>Nota:</b>
	<p>Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.</p>
</div>

1. Reinicie Eclipse y, a continuación, en el Explorador de paquetes, haga clic con el botón secundario en el proyecto, haga clic en **Propiedades**, **Android**, marque **API de Google** y, a continuación, haga clic en **Aceptar**.

	![][23]

  	Con esto, el proyecto se dirige a las API de Google.

2. En **Ventana**, seleccione **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y, a continuación, haga clic en **Editar**.

	![][24]

3. Seleccione **API de Google** en **Destino** y, a continuación, haga clic en Aceptar.

   	![][25]

	Con esto, el dispositivo virtual Android se dirige para usar las API de Google.

4. En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

5. En la aplicación, escriba un texto significativo, como _Una nueva tarea de Servicios móviles_ y, a continuación, haga clic en el botón **Agregar**.

  	![][26]

6. Un cuadro de notificación de color negro aparecerá brevemente en la parte inferior de la pantalla. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

Ha completado correctamente este tutorial.

## <a name="next-steps"></a>Pasos siguientes

En este ejemplo simple, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. El cliente en la solicitud suministra al servicio móvil el token de dispositivo que GCM utiliza. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación], creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción. 


<!-- Images. -->

<google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Consola de la nube de Google]: https://cloud.google.com/console
[API de Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Portal de aprovisionamiento de Android]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Configuración del SDK para servicios de Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
[Referencias a un proyecto de biblioteca]: http://go.microsoft.com/fwlink/?LinkId=389800
[SDK de Android para Servicios móviles]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Introducción a Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android
[Notificaciones de inserción para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-android
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-android

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
[Modelo conceptual de Android para Servicios móviles]: /es-es/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=282645

