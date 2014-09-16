<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect
==================================================================================================

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#" class="current">C\# para Tienda Windows</a><a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">JavaScript para Tienda Windows</a><a href="/en-us/develop/mobile/tutorials/single-sign-on-wp8" title="Windows Phone">Windows Phone</a>
</div>

En este tema se muestra cómo utilizar el inicio de sesión único de Live Connect para autenticar a los usuarios en Servicios móviles de Azure desde una aplicación de la Tienda Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

<div class="dev-callout"><b>Nota:</b>

<p>Este tutorial muestra las ventajas de usar la experiencia de inicio de sesión único que ofrece Live Connect para las aplicaciones de la Tienda Windows. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. Para ver una experiencia de autenticación más generalizada que admita varios proveedores de autenticación, consulte el tema <a href="/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/">Introducción a la autenticación</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial requiere lo siguiente:

-   [SDK de Live para Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express para Windows 8 RC o una versión posterior.

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started).

<a name="register"></a>
Registro de la aplicaciónRegistro de la aplicación para la Tienda Windows
-------------------------------------------------------------------------

Para poder autenticar a los usuarios, debe enviar la aplicación a la Tienda Windows. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

1.  Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación](http://go.microsoft.com/fwlink/p/?LinkID=266582) en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **App name**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png)

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png)

    Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started).

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png) 
    
    Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

1.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

2.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png)

     Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

3.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png)

4.  Haga clic en la pestańa **Panel** y anote el valor de **Site URL**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png)

      Este valor se usará para definir el dominio de redirección.

5.  Desplácese hasta la página [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) del Centro para desarrolladores de Live Connect y haga clic en su aplicación en la lista **My applications**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png)

6.  Haga clic en **Edit settings**, después en **API Settings** y anote los valores de **Client ID** y **Client secret**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Nota de seguridad</b>
    <p>El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.</p>
    </div>

1.  En **Redirect domain**, escriba la dirección URL de su servicio móvil que anotó en el paso 8 y, a continuación, haga clic en **Save**.

2.  Una vez haya vuelto al Portal de administración, haga clic en la pestańa **Identity**, escriba el valor de **Client secret** obtenido de la Tienda Windows y haga clic en **Save**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png)

El servicio móvil y la aplicación están ahora configurados para funcionar con Live Connect.

<a name="permissions"></a>
Restricción de permisosRestricción de permisos a usuarios autenticados
----------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestańa **Data** y, a continuación, en la tabla **TodoItem**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png)

2.  Haga clic en la pestańa **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png)

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started).

4.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se genera una excepción con el código de estado 401 (No autorizado).

      Esto se produce porque la aplicación obtiene acceso a Servicios móviles como usuario autenticado, pero ahora la tabla _TodoItem_ requiere autenticación.

A continuación, actualizará la aplicación para autenticar a los usuarios con Live Connect antes de solicitar recursos del servicio móvil.

<a name="add-authentication"></a>
Incorporación de autenticaciónIncorporación de autenticación a la aplicación
----------------------------------------------------------------------------

1.  Descargue e instale el [SDK de Live para Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Windows**, haga clic en **Extensiones**, active la casilla **SDK de Live** y, a continuación, haga clic en **Aceptar**.

    ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png) 
    
    De esta forma se agrega al proyecto una referencia al SDK de Live.

1.  Abra el archivo de proyecto MainPage.xaml.cs y agregue la siguiente instrucción using:

         using Microsoft.Live;        

2.  Agregue el siguiente fragmento de código a la clase MainPage:

         private LiveConnectSession session;
         private async System.Threading.Tasks.Task Authenticate()
         {
             LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERTAR AQUÍ DOMINIO DE REDIRECCIÓN >>");

             while (session == null)
             {
                 // Forzar el cierre de sesión para facilitar las pruebas con varias cuentas Microsoft
                 if (liveIdClient.CanLogout)
                     liveIdClient.Logout();

                 LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                 if (result.Status == LiveConnectSessionStatus.Connected)
                 {
                     session = result.Session;
                     LiveConnectClient client = new LiveConnectClient(result.Session);
                     LiveOperationResult meResult = await client.GetAsync("me");
                     MobileServiceUser loginResult = await App.MobileService
                         .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                     string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                     var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                     var dialog = new MessageDialog(message, title);
                     dialog.Commands.Add(new UICommand("OK"));
                     await dialog.ShowAsync();
                 }
                 else
                 {
                     session = null;
                     var dialog = new MessageDialog("You must log in.", "Login Required");
                     dialog.Commands.Add(new UICommand("OK"));
                     await dialog.ShowAsync();
                 }
             }
          }

    De esta forma se crea una variable de miembro para el almacenamiento de la sesión de Live Connect actual y un método para gestionar el proceso de autenticación.

    <div class="dev-callout"><b>Nota:</b>

    <p>Este código fuerza un cierre de sesión, cuando sea posible, para asegurarse de que se solicitan las credenciales al usuario cada vez que se ejecute la aplicación. Esto hace más fácil probar la aplicación con diversas cuentas Microsoft a fin de asegurarse de que la autenticación funciona correctamente. Este mecanismo solo funcionará si el usuario que ha iniciado sesión no tiene una cuenta Microsoft conectada.</p>
	</div>

3.  Actualice la cadena *&lt;&lt; INSERTAR AQUÍ DOMINIO DE REDIRECCIÓN \>\>* del paso anterior con el dominio de redirección que se especificó al configurar la aplicación en Live Connect, con el formato **https://*nombre-servicio*.azure-mobile.net/**.

    <div class="dev-callout"><b>Nota:</b>

    <p>En una aplicación de la Tienda Windows, para crear una instancia de la clase **LiveAuthClient** se pasa el valor del URI del dominio de redirección al constructor de clases. En una <a href="/en-us/develop/mobile/tutorials/single-sign-on-wp8/">aplicación de Windows Phone 8</a>, para crear instancias de la misma clase se pasa el identificador de cliente.</p>
	</div>

4.  Reemplace el controlador de eventos **OnNavigatedTo** existente por el controlador que llama al nuevo método **Authenticate**:

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

5.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

<a name="next-steps"> </a>
Pasos siguientes
----------------

En el siguiente tutorial, [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet). Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library).

