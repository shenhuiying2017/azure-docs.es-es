<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" />

Introducción a la autenticación en Servicios móviles
====================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-users-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-xamarin-ios).

Para completar este tutorial necesita [Xamarin.iOS], XCode 5.0 e iOS 5.0 o versiones posteriores.

Registro de la aplicaciónRegistro de la aplicación para la autenticación y configuración de Servicios móviles
-------------------------------------------------------------------------------------------------------------

Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

  ![][4]

2.  Haga clic en la pestaña **Panel** y anote el valor de **Site URL**.

  ![][5]

  Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3.  Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:

 -   [Cuenta Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
 -   [Inicio de sesión en Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
 -   [Inicio de sesión en Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
 -   [Inicio de sesión en Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
 -   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

    **Nota de seguridad:**

    El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

1.  Vuelva al Portal de administración, haga clic en la pestaña **Identity**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Save**.

  ![][13]

El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

Restricción de permisosRestricción de permisos a usuarios autenticados
----------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

  ![][14]

2.  Haga clic en la pestaña **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

  ![][15]

3.  En Xcode, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-xamarin-ios).

4.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en el emulador de iPhone; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

  Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

Incorporación de autenticaciónIncorporación de autenticación a la aplicación
----------------------------------------------------------------------------

1.  Abra el archivo de proyecto **TodoService** y agregue las siguientes variables:

         // Usuario que ha iniciado sesión en el servicio móvil
         private MobileServiceUser user; 
         public MobileServiceUser User { get { return user; } }

2.  A continuación, agregue un método nuevo con nombre **Authenticate** a **TodoService** definido como:

         private async Task Authenticate(UIViewController view)
         {
             try
             {
                 user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
             }
             catch (Exception ex)
             {
                 Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
             }
         }

    **Nota:**

    Si usa un proveedor de identidades que no sea una cuenta Microsoft, cambie el valor que ha pasado a **LoginAsync** anteriormente por uno de los siguientes: *Facebook*, *Twitter* o *Google*.

3.  Mueva la solicitud para la tabla **TodoItem** desde el constructor **TodoService** a un nuevo método llamado **CreateTable**:

         private async Task CreateTable()
         {
             // Crear una instancia de MSTable que nos permita trabajar con la tabla TodoItem
             todoTable = client.GetTable<TodoItem>();
         }

4.  Cree un nuevo método público asincrónico llamado **LoginAndGetData** definido como:

         public async Task LoginAndGetData(UIViewController view)
         {
             await Authenticate(view);
             await CreateTable();
         }

5.  En **TodoListViewController**, reemplace el método **ViewDidAppear** y defínalo tal como se observa a continuación. Se registra en el usuario si **TodoService** todavía no tiene control sobre el usuario:

         public override async void ViewDidAppear(bool animated)
         {
             base.ViewDidAppear(animated);

             if (TodoService.DefaultService.User == null)
             {
                 await TodoService.DefaultService.LoginAndGetData(this);
             }

             if (TodoService.DefaultService.User == null)
             {
                 // TODO:: show error
                 return;
             } 
                    
             RefreshAsync();
         }

6.  Quite la llamada original a **RefreshAsync** desde **TodoListViewController.ViewDidLoad**.

7.  Presione el botón **Run** para compilar el proyecto, inicie la aplicación en el emulador de iPhone e inicie sesión con su proveedor de identidades elegido.

  Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

Obtener un ejemplo completado
-----------------------------

Descargue el [proyecto de ejemplo completado](http://go.microsoft.com/fwlink/p/?LinkId=331328). Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

Pasos siguientes
----------------

En el siguiente tutorial, [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.



<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png