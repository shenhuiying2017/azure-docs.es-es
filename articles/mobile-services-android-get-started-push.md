<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />


Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-js "JavaScript para Tienda Windows")[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/es-es/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Este tema muestra cómo puede utilizar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE] Este tema muestra cómo habilitar las notificaciones de inserción con la compatibilidad heredada que brindan los Servicios móviles. Centros de notificaciones de Azure se integra con Servicios móviles para permitirle enviar notificaciones de inserción basadas en plantillas entre plataformas a millones de dispositivos. De manera predeterminada, no están habilitadas las notificaciones de inserción con Centros de notificaciones y actualmente no hay compatibilidad con Centro de notificaciones para Android en las bibliotecas de Servicios móviles. Sin embargo, puede enviar notificaciones de inserción desde su servicio móvil si usa las bibliotecas del Centro de notificaciones. Para obtener más información, consulte [Introducción a los Centros de notificaciones](/es-es/documentation/articles/notification-hubs-android-get-started/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

-   [Habilitación del servicio de mensajería en la nube de Google](#register)
-   [Configuración de Servicios móviles](#configure)
-   [Incorporación de notificaciones de inserción a la aplicación](#add-push)
-   [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
-   [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

-   [SDK de Android para Servicios móviles](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)
-   Una cuenta de Google activa

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-android).

Habilitación del servicio de mensajería en la nube de Google
------------------------------------------------------------

[WACOM.INCLUDE [Habilitación de GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

A continuación, utilizará este valor de clave de API para permitir que Servicios móviles se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

Configuración de Servicios móviles para enviar solicitudes de inserción
-----------------------------------------------------------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

	![][18]

2.  Haga clic en la pestaña **Push**, escriba el valor de la **Clave de API** obtenido de GCM en el procedimiento anterior y, a continuación, haga clic en **Save**.

	![][19]

Su servicio móvil ahora está configurado para trabajar con GCM para enviar notificaciones de inserción.

Incorporación de notificaciones de inserción a la aplicación
------------------------------------------------------------

### Incorporación de Google Play Services al proyecto

[WACOM.INCLUDE [Incorporación de Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Incorporación de código

1.  Abra el archivo de proyecto **AndroidManifest.xml**. El servicio de mensajería en la nube de Google tiene algunos requisitos mínimos en el nivel de API para desarrollo y prueba, los que debe cumplir la propiedad **minSdkVersion** del manifiesto. Consulte [Set Up Google Play Services SDK](http://go.microsoft.com/fwlink/?LinkId=389801) para determinar el valor menor que se puede definir, en caso de que necesite definir un valor inferior a 16, debido a que se utiliza un dispositivo anterior. Defina la propiedad como corresponda.

2.  Asegúrese de que en el elemento uses-sdk, la propiedad **targetSdkVersion** esté definida en el número de una plataforma de SDK que se ha instalado (paso 1). Es preferible definirla en la versión más reciente disponible.

3.  La etiqueta **uses-sdk** puede verse de la siguiente manera, dependiendo de las opciones que haya elegido en los pasos anteriores:

         <uses data-morhtml="true"-sdk
             android:minSdkVersion="17"
             android:targetSdkVersion="19" />

4.  En el código que aparece en los próximos dos pasos, reemplace *\*\*my\_app\_package\*\** por el nombre del paquete de aplicaciones para el proyecto, que es el valor del atributo package de la etiqueta manifest.

5.  Agregue los siguientes permisos nuevos después del elemento uses-permission existente:

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses data-morhtml="true"-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses data-morhtml="true"-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses data-morhtml="true"-permission android:name="android.permission.GET_ACCOUNTS" />
         <uses data-morhtml="true"-permission android:name="android.permission.WAKE_LOCK" />

6.  Agregue el siguiente código después de la etiqueta de apertura application:

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent data-morhtml="true"-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

7.  Abra el archivo ToDoItem.java y agregue el siguiente código a la clase **TodoItem**:

             @com.google.gson.annotations.SerializedName("handle")
             private String mHandle;
            
             public String getHandle() {
                 return mHandle;
             }
            
             public final void setHandle(String handle) {
                 mHandle = handle;
             }

    Este código crea una propiedad nueva que contiene el identificador de registro.

    **Nota:**

    Cuando su servicio móvil tenga habilitado el esquema dinámico, una columna **handle** se agregará automáticamente a la tabla **TodoItem** cuando se inserte un elemento nuevo que contenga esta propiedad.

8.  Descargue y descomprima el [SDK de Android para Servicios móviles (en inglés)](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409), abra la carpeta **notificaciones**, copie el archivo **notifications-1.0.1.jar** en la carpeta *libs* del proyecto Eclipse y actualice la carpeta *libs*.

    **Nota:**

    Los números que aparecen al final del nombre del archivo pueden cambiar en versiones de SDK posteriores.

9.  Abra el archivo *ToDoItemActivity.java* y agregue la siguiente instrucción de importación:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Agregue la siguiente variable privada a la clase, donde *&lt;PROJECT data-morhtml="true"\_NUMBER\>* es el número de proyecto que Google asigna a su aplicación en el procedimiento anterior:

        public static final String SENDER_ID = "<PROJECT data-morhtml="true"_NUMBER>";

11. En el método **onCreate**, antes de crear una instancia del MobileServiceClient, agregue este código que registra el controlador de notificación para el dispositivo:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Agregue la siguiente línea de código al método **addItem**, antes de insertar el elemento en la tabla:

        item.setHandle(MyHandler.getHandle());

    Este código define la propiedad handle del elemento en el identificador de registro del dispositivo.

13. En el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodosrc), haga clic en **Nuevo** y luego en **Clase**.

14. En **Nombre**, escriba MyHandler, en **Superclase**, escriba com.microsoft.windowsazure.notifications.NotificationsHandler y luego haga clic en **Finalizar**

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    Con esto se crea la clase MyHandler.

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

Ahora su aplicación está actualizada para ser compatible con las notificaciones de inserción.

Actualización del script de inserción registrado en el Portal de administración
-------------------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

	![][21]

2.  En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insert**.

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

1.  Sustituya la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Escriba la respuesta y luego envíe la notificación en segundo plano
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

Pruebas de notificaciones de inserción en su aplicación
-------------------------------------------------------

**Nota:**

Cuando ejecute esta aplicación en el emulador, asegúrese de utilizar un dispositivo virtual Android (AVD) compatible con las API de Google.

1.  Reinicie Eclipse y, a continuación, en el Explorador de paquetes, haga clic con el botón secundario en el proyecto, haga clic en **Propiedades**, **Android**, marque **API de Google** y, a continuación, haga clic en **Aceptar**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

Con esto, el proyecto se dirige a las API de Google.

1.  En **Ventana**, seleccione **Administrador de dispositivos virtuales de Android**, seleccione su dispositivo y haga clic en **Editar**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  Seleccione **API de Google** en **Destino** y haga clic en Aceptar.

	![][25]

    Con esto, el dispositivo virtual Android se dirige para usar las API de Google.

3.  En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

4.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el botón **Agregar**.

	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  Un cuadro de notificación de color negro aparecerá brevemente en la parte inferior de la pantalla.

	![](./media/mobile-services-android-get-started-push/mobile-push-icon.png)

Ha completado correctamente este tutorial.

Pasos siguientes
----------------

En este ejemplo simple, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. El cliente en la solicitud suministra al servicio móvil el token de dispositivo que GCM utiliza. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación](/es-es/develop/mobile/tutorials/push-notifications-to-users-android), creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción.

<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
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
[Google Cloud Console]: https://cloud.google.com/console
[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started-android
[Get started with data]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /es-es/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /es-es/develop/mobile/tutorials/authorize-users-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services android conceptual]: /es-es/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645
