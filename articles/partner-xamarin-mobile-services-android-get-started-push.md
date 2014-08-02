<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.Android apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services" documentationCenter="Mobile" authors="" />

Introducción a las notificaciones de inserción en Servicios móviles
===================================================================

[C\# de Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[JavaScript de Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de Xamarin.Android. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de mensajería en la nube de Google (GCM). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Registro de la aplicación para las notificaciones de inserción](#register)
2.  [Configuración de Servicios móviles](#configure)
3.  [Incorporación de notificaciones de inserción a la aplicación](#add-push)
4.  [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
5.  [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

-   Una cuenta de Google activa

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-xamarin-android).

Registro de la aplicaciónRegistro de la aplicación para las notificaciones de inserción
---------------------------------------------------------------------------------------

**Nota:**

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google con una dirección de correo electrónico verificada. Para crear una nueva cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Desplácese hasta el sitio web de las [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) (en inglés), inicie sesión con las credenciales de la cuenta de Google y, a continuación, haga clic en **Create project...**

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png)

    > [WACOM.NOTE] Si dispone ya de un proyecto existente, se le dirigirá a la página **Panel** después de iniciar sesión. Para crear un nuevo proyecto desde el panel, expanda **API Project&lt;**, haga clic en **Create...** debajo de **Other projects**, especifique un nombre de proyecto y haga clic en **Create project**.

2.  Haga clic en el botón Overview de la columna izquierda y anote el número de proyecto en la sección Panel.

    Posteriormente en el tutorial establecerá este valor como la variable **PROJECT\_ID** en el cliente.

3.  En la página de [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) (en inglés), haga clic en **Services** y, a continuación, haga clic en el cambiador para activar el **servicio de mensajería en la nube de Google para Android** y aceptar los términos del servicio.

4.  Haga clic en **API Access** y, a continuación, haga clic en **Create new Server key...**

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png)

5.  En **Configure Server Key for API Project**, haga clic en **Create**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png)

6.  Anote el valor de **API key**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png)

A continuación, utilizará este valor de clave de API para permitir que Servicios móviles se autentique con GCM y envíe notificaciones de inserción en nombre de su aplicación.

&lt;h2\>Configuración del servicioConfiguración de Servicios móviles para enviar solicitudes de inserción&lt;/h2\>

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en su aplicación.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png)

2.  Haga clic en la pestaña **Push**, escriba el valor de la **Clave de API** obtenido de GCM en el procedimiento anterior y, a continuación, haga clic en **Save**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png)

    Su servicio móvil ahora está configurado para trabajar con GCM para enviar notificaciones de inserción.

&lt;h2\>Incorporación de notificaciones de inserciónIncorporación de notificaciones de inserción a la aplicación&lt;/h2\>

1.  Primero tendrá que agregar **PushSharp** como referencia en nuestro proyecto. Para hacer esto, debemos compilar la última versión de PushSharp y agregar el DLL compilado como referencia a nuestro proyecto Xamarin.Android.

2.  Visite la página [PushSharp Github](https://github.com/Redth/PushSharp) y descargue la última versión. Una vez que haya extraído la colección de archivos, desplácese a la siguiente carpeta de proyecto de ejemplo:

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. y abra el archivo de proyecto:

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Cree el ejemplo de cliente MonoForAndroid PushSharp en el modo **Release**.

4.  Cree una carpeta **\_external** en la carpeta de proyecto Xamarin.Android.

5.  Copie el siguiente archivo del ejemplo de cliente MonoForAndroid PushSharp a la carpeta recién creada **\_external** en la carpeta de proyecto Xamarin.Android:

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Abra el proyecto Xamarin.Android en Xamarin Studio (o Visual Studio).

7.  Haga clic con el botón secundario en la carpeta **References** del proyecto y seleccione **Edit References...**

8.  Diríjase a la pestaña **.Net Assembly**, vaya a la carpeta **\_external** del proyecto, seleccione el archivo **PushSharp.Client.MonoForAndroid.dll** creado anteriormente y haga clic en **Add**. Haga clic en OK para cerrar el cuadro de diálogo.

9.  Abra **Constants.cs** y agregue la siguiente línea reemplazando **PROJECT\_ID** por el Project\_ID de Google que anotó anteriormente:

         public const string SenderID = "PROJECT_ID"; // Número de proyecto de la API de Google

10. Copie el archivo **PushService.cs** del ejemplo del cliente MonoForAndroid PushSharp cliente a la carpeta del proyecto Xamarin.Android y agréguelo al proyecto.

11. Cambie el espacio de nombre usado en **PushService.cs** para que coincida con el espacio de nombre del proyecto (ejemplo: XamarinTodoQuickStart).

12. Cambie la matriz **SENDER\_IDS** en **PushService.cs** para hacer referencia a la constante **SenderID** creada anteriormente:

        public static string[] SENDER_IDS = new string[] { Constants.SenderID };

13. Agregue una nueva propiedad estática a **PushHandlerService** en **PushService.cs** para realizar un seguimiento del identificador de registro del dispositivo:

        public static string RegistrationID { get; private set; }

14. Actualice el método **OnRegistered** en **PushService.cs** para almacenar el identificador de registro recibido en la variable estática local:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
        }

15. Actualice el método **OnMessage** en **PushService.cs** para mostrar el mensaje de inserción recibido como parte de la notificación (reemplace la llamada **createNotification** existente):

        string message = intent.Extras.GetString("message");
        createNotification("Nuevo elemento todo", "Elemento Todo: " + message);

16. Observe que el método **OnMessage** tiene el siguiente código predeterminado para almacenar el último mensaje de inserción recibido:

        //Almacene el mensaje.
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. Actualice el método **createNotification** en **PushService.cs** para hacer referencia a **TodoActivity** en lugar de a **DefaultActivity**.

18. Abra **TodoActivity.cs** y agregue la siguiente instrucción Using:

        using PushSharp.Client;

19. En **TodoActivity.cs**, inserte las siguientes líneas justo encima de donde se creó **MobileServiceClient**:

        // Realice la comprobación para asegurarse de que todo se ha configurado correctamente.
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Registre las notificaciones de inserción.
        System.Diagnostics.Debug.WriteLine("Registrando...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. Abra **TodoItem.cs** y agregue un campo nuevo para realizar un seguimiento del identificador del dispositivo registrado para la persona que agregó TodoItem:

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. En **TodoActivity.cs**, actualice el método **AddItem** para establecer el valor de **RegistrationID** del **TodoItem** recién creado en el identificador de registro del dispositivo recibido durante el registro.

        // Cree un nuevo elemento.
        var item = new TodoItem() {
            Text = textNewTodo.Text,
            Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
        };

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

Actualización del script de inserciónActualización del script de inserción registrado en el Portal de administración
--------------------------------------------------------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  En **TodoItem**, haga clic en la pestaña **Script** y seleccione **Insert**.

![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png)

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

1.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Escriba la respuesta y luego envíe la notificación en segundo plano
                     request.respond();
                     push.gcm.send(item.channel, item.text, {
                         success: function(response) {
                             console.log('Notificación de inserción enviada: ', response);
                         }, error: function (error) {
                             console.log('Error al enviar notificación de inserción: ', error);
                         }
                     });
                 }
             });
         }

    Esto registra un nuevo script de inserción, que usa el [gcm (objeto)](http://go.microsoft.com/fwlink/p/?LinkId=282645) para enviar una notificación de inserción (el texto insertado) al dispositivo indicado en la solicitud de inserción.

Prueba de la aplicaciónPruebas de notificaciones de inserción en su aplicación
------------------------------------------------------------------------------

1.  Ejecute la aplicación y agregue un nuevo elemento Todo. Asegúrese de que recibe una notificación de inserción sobre el nuevo elemento Todo que se agrega.

2.  Revise la pestaña **Logs** de la aplicación móvil en el Portal de administración de Azure para ver los mensajes de registro que agregamos al método **Insert** en la tabla **TodoItem** anterior.

3.  Observe la tabla **TodoItem** del Portal de administración de Azure para ver la nueva columna **channel** que se agregó y que contiene identificadores de registro de dispositivos exclusivos.

Ha completado correctamente este tutorial.

Obtención del ejemplo completo
------------------------------

Descargue el [proyecto de ejemplo completo](http://go.microsoft.com/fwlink/p/?LinkId=331303). Asegúrese de actualizar las variables **ApplicationURL**, **ApplicationKey** y **SenderID** con su propia configuración de Azure.

Pasos siguientes
----------------

En este ejemplo simple, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación](/en-us/develop/mobile/tutorials/push-notifications-to-users-android), creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción.

