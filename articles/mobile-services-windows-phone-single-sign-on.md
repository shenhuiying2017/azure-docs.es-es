<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

Autenticación de una aplicación de Windows Phone 8 con el inicio de sesión único de Live Connect
================================================================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/ "Windows Phone")

En este tema se muestra cómo utilizar el inicio de sesión único de Live Connect para autenticar a los usuarios en Servicios móviles de Azure desde una aplicación de Windows Phone 8. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

**Nota:**

Este tutorial muestra las ventajas de usar la experiencia de inicio de sesión único que ofrece Live Connect para las aplicaciones de Windows Phone. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. Para ver una experiencia de autenticación más generalizada que admita varios proveedores de autenticación, consulte el tema [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-wp8/).

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial requiere lo siguiente:

-   [SDK de Live para Windows y Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express para Windows Phone

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).

Registro de la aplicaciónRegistro de la aplicación con Live Connect
-------------------------------------------------------------------

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de Live Connect. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

	![][4]

2.  Haga clic en la pestaña **Panel** y anote el valor de **Site URL**.

	![][5]

    Este valor se usará para definir el dominio de redirección.

3.  Vaya a la página [Mis aplicaciones](http://go.microsoft.com/fwlink/p/?LinkId=262039) del Centro para desarrolladores de Live Connect e inicie sesión con cuenta Microsoft, si procede.

4.  Haga clic en **Create application** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **I accept**.

	![][1] 

	Así se registra la aplicación con Live Connect.

5.  Haga clic en la **página de configuración de la aplicación**, después en **API Settings** y, a continuación, anote los valores de **Client ID** y **secreto de cliente**.

	![][2]

    **Nota de seguridad**

    El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

6.  En **Redirect domain**, especifique la dirección URL del servicio móvil del paso 2, haga clic en **Yes** en **Mobile client app** y, a continuación, haga clic en **Save**.

7.  Una vez haya vuelto al Portal de administración, haga clic en la pestaña **Identity**, escriba el valor de **Client secret** obtenido de Live Connect y, a continuación, haga clic en **Save**.

	![][13]

El servicio móvil y la aplicación están ahora configurados para funcionar con Live Connect.

Restricción de permisosRestricción de permisos a usuarios autenticados
----------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

	![][14]

2.  Haga clic en la pestaña **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

	![][15]

3.  En Visual Studio 2012 Express para Windows Phone, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).

4.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se genera una excepción con el código de estado 401 (No autorizado).

        Esto se produce porque la aplicación obtiene acceso a Servicios móviles como usuario autenticado, pero ahora la tabla _TodoItem_ requiere autenticación.

A continuación, actualizará la aplicación para autenticar a los usuarios con Live Connect antes de solicitar recursos del servicio móvil.

Incorporación de autenticaciónIncorporación de autenticación a la aplicación
----------------------------------------------------------------------------

1.  Descargue e instale el [SDK Live para Windows y Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Ensamblados**, haga clic en **Extensiones**, active la casilla **Microsoft.Live** y, a continuación, haga clic en **Aceptar**.

	![][16]

De esta forma se agrega al proyecto una referencia al SDK de Live.

1.  Abra el archivo de proyecto mainpage.xaml.cs y agregue las siguientes instrucciones using:

         using Microsoft.Live;      

2.  Agregue el siguiente fragmento de código a la clase MainPage:

         private LiveConnectSession session;
         private async System.Threading.Tasks.Task Authenticate()
         {
             LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERTAR AQUÍ EL IDENTIFICADOR DE CLIENTE >>");

             while (session == null)
             {
                 LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                 if (result.Status == LiveConnectSessionStatus.Connected)
                 {
                     session = result.Session;
                     LiveConnectClient client = new LiveConnectClient(result.Session);
                     LiveOperationResult meResult = await client.GetAsync("me");
                     MobileServiceUser loginResult = await App.MobileService
                         .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                     string title = string.Format("Bienvenido/a {0}", meResult.Result["first_name"]);
                     var message = string.Format("Ahora ha iniciado sesión en - {0}", loginResult.UserId);
                     MessageBox.Show(message, title, MessageBoxButton.OK);                    
                 }
                 else
                 {
                     session = null;
                     MessageBox.Show("Debe iniciar sesión.", "Inicio de sesión obligatorio", MessageBoxButton.OK);                    
                 }
             }
          }

    De esta forma se crea una variable de miembro para el almacenamiento de la sesión de Live Connect actual y un método para gestionar el proceso de autenticación.

3.  Actualice la cadena *&lt;&lt; INSERTAR IDENTIFICADOR DE CLIENTE AQUÍ \>\>* del paso anterior con el valor del identificador de cliente que se generó cuando registró su aplicación con Live Connect.

    **Nota:**

    En una aplicación de Windows Phone 8, para crear una instancia de la clase **LiveAuthClient** se pasa el valor del identificador de cliente al constructor de clases. En una [aplicación de la Tienda Windows](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/), para crear instancias de la misma clase se pasa el URI del dominio de redirección.

4.  Elimine o convierta en comentario el método **OnNavigatedTo** existente y reemplácelo por el siguiente método que administra el evento **Loaded** para la página.

         async void MainPage_Loaded(object sender, RoutedEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

        Este método llama al nuevo método **Authenticate**. 

5.  Reemplace el constructor MainPage con el siguiente código:

         // Constructor
         public MainPage()
         {
             InitializeComponent();
             this.Loaded += MainPage_Loaded;
         }

        Este constructor también registra el controlador para el evento Loaded.

6.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft.

        Cuando haya iniciado sesión correctamente, la aplicación se ejecuta sin errores y podrá consultar a Servicios móviles y realizar actualizaciones de datos.

Pasos siguientes
----------------

En el siguiente tutorial, [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-wp8).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png





[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows and Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: h