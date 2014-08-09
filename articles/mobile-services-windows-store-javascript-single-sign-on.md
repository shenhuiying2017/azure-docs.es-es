<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-js" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (JavaScript)" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso, Azure Javascript SSO" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/" services="" documentationCenter="Mobile" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect
==================================================================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/single-sign-on-windows-8-js "JavaScript para Tienda Windows")[Windows Phone](/es-es/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación de la Tienda Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante Live Connect. Una vez que Live Connect haya realizado la autenticación correctamente, se dará la bienvenida al usuario que ha iniciado sesión por su nombre y se mostrará el valor de identificador de usuario.

**Nota:**

Este tutorial muestra las ventajas de usar la experiencia de inicio de sesión único que ofrece Live Connect para las aplicaciones de la Tienda Windows. Esto le permite autenticar más fácilmente a un usuario que ya haya iniciado sesión en el servicio móvil. Para ver una experiencia de autenticación más generalizada que admita varios proveedores de autenticación, consulte el tema [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-js/).

Este tutorial le guiará a través de estos pasos básicos para habilitar la autenticación de Live Connect:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial requiere lo siguiente:

-   [SDK de Live para Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express para Windows 8 RC o una versión posterior.

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).

Registro de la aplicaciónRegistro de la aplicación para la Tienda Windows
-------------------------------------------------------------------------

Para poder autenticar a los usuarios, debe enviar la aplicación a la Tienda Windows. A continuación, debe registrar el secreto de cliente para integrar Live Connect en Servicios móviles.

1.  Si aún no ha registrado su aplicación, vaya a la [página Enviar una aplicación](http://go.microsoft.com/fwlink/p/?LinkID=266582) en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta de Microsoft y, a continuación, haga clic en **App name**.

  ![][0]

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

  ![][1]

  Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

  ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png) 

  Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

1.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

2.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

  ![][3]

  Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

3.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

  ![][4]

4.  Haga clic en la pestaña **Panel** y anote el valor de **Site URL**.

  ![][5]

  Este valor se usará para definir el dominio de redirección.

5.  Desplácese hasta la página [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) del Centro para desarrolladores de Live Connect y haga clic en su aplicación en la lista **My applications**.

  ![][6] 

6.  Haga clic en **Edit settings**, después en **API Settings** y anote los valores de **Client ID** y **Client secret**.

  ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Nota de seguridad</b>
    <p>El secreto de cliente es una credencial de seguridad importante. No comparta su secreto de cliente con nadie ni lo distribuya con su aplicación.</p>
    </div>

1.  En **Redirect domain**, escriba la dirección URL de su servicio móvil que anotó en el paso 8 y, a continuación, haga clic en **Save**.

2.  Una vez haya vuelto al Portal de administración, haga clic en la pestaña **Identity**, escriba el valor de **Client secret** obtenido de la Tienda Windows y haga clic en **Save**.

  ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png)

El servicio móvil y la aplicación están ahora configurados para funcionar con Live Connect.

Restricción de permisosRestricción de permisos a usuarios autenticados
----------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

  ![][14]

2.  Haga clic en la pestaña **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

  ![][15]

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started).

4.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se genera una excepción con el código de estado 401 (No autorizado).

  Esto se produce porque la aplicación obtiene acceso a Servicios móviles como usuario autenticado, pero ahora la tabla _TodoItem_ requiere autenticación.

A continuación, actualizará la aplicación para autenticar a los usuarios con Live Connect antes de solicitar recursos del servicio móvil.

Incorporación de autenticaciónIncorporación de autenticación a la aplicación
----------------------------------------------------------------------------

1.  Descargue e instale el [SDK de Live para Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  En el menú **Proyecto** de Visual Studio, haga clic en **Agregar referencia**, a continuación expanda **Windows**, haga clic en **Extensiones**, active la casilla **SDK de Live** y, a continuación, haga clic en **Aceptar**.

  ![](./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png)

  De esta forma se agrega al proyecto una referencia al SDK de Live.

1.  Abra el archivo de proyecto default.html y agregue el siguiente elemento &lt;script\> en el elemento &lt;head\>.

		<script src="///LiveSDKHTML/js/wl.js"></script>

  De este modo se habilita Microsoft IntelliSense en el archivo default.html.

2.  Abra el archivo de proyecto default.js y agregue el siguiente comentario a la parte superior del archivo.

		/// <reference path="///LiveSDKHTML/js/wl.js" />

  De este modo se habilita Microsoft IntelliSense en el archivo default.js.

3.  En la sobrecarga del método **app.OnActivated**, sustituya la llamada al método **refreshTodoItems** por el siguiente código:

         var session = null;   

         var logout = function () {
             return new WinJS.Promise(function (complete) {
                 WL.getLoginStatus().then(function () {
                     if (WL.canLogout()) {
                         WL.logout(complete);                            
                     }
                     else {
                         complete();
                     }
                 });
             });
         };                  

         var login = function () {
             return new WinJS.Promise(function (complete) {                    
                 WL.login({ scope: "wl.basic"}).then(function (result) {
                     session = result.session;

                     WinJS.Promise.join([
                         WL.api({ path: "me", method: "GET" }),
                         client.login(result.session.authentication_token)
                     ]).done(function (results) {
                         var profile = results[0];
                         var mobileServicesUser = results[1];
                         refreshTodoItems();
                         var title = "Welcome " + profile.first_name + "!";
                         var message = "You are now logged in as: " + mobileServicesUser.userId;
                         var dialog = new Windows.UI.Popups.MessageDialog(message, title);
                         dialog.showAsync().done(complete);                                
                     });                       
                 }, function (error) {                        
                     session = null;
                     var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
                     dialog.showAsync().done(complete);                        
                 });
             });
         }

         var authenticate = function () {
             // Forzar el cierre de sesión para facilitar las pruebas con varias cuentas Microsoft
             logout().then(login).then(function () {
                 if (session === null) {
                     // Error de autenticación, vuelva a intentarlo.
                     authenticate();
                 }
             });
         }

         WL.init({
             redirect_uri: "<< INSERT REDIRECT DOMAIN HERE >>"
         });           
                
         authenticate();

    De este modo se inicializa el cliente Live Connect, se fuerza el cierre de sesión, se envía una nueva solicitud de inicio de sesión a Live Connect, se envía el token de autenticación devuelto a Servicios móviles y, a continuación, se muestra la información del usuario que ha iniciado sesión.

    **Nota:**

    Este código fuerza un cierre de sesión, cuando sea posible, para asegurarse de que se solicitan las credenciales al usuario cada vez que se ejecute la aplicación. Esto hace más fácil probar la aplicación con diversas cuentas Microsoft a fin de asegurarse de que la autenticación funciona correctamente. Este mecanismo solo funcionará si el usuario que ha iniciado sesión no tiene una cuenta Microsoft conectada.

4.  Actualice la cadena *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* del paso anterior con el dominio de redirección que se especificó al configurar la aplicación en Live Connect, con el formato **https://*nombre-servicio*.azure-mobile.net/**.

5.  Presione la tecla F5 para ejecutar la aplicación e iniciar sesión en Live Connect con su cuenta Microsoft.

  Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

Pasos siguientes
----------------

En el siguiente tutorial, [Autorización de usuarios con scripts](/es-es/develop/mobile/tutorials/authorize-users-in-scripts-js), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Para obtener información sobre cómo usar otros proveedores de identidades para la autenticación, consulte [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-js).

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-select-app-name.png
[4]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-service-uri.png
[6]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-live-connect-app-api-settings.png





[13]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-store-javascript-single-sign-on/mobile-add-reference-live-js.png