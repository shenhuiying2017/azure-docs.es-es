<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-users-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación HTML. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-html).

Registro de la aplicaciónRegistro de la aplicación para la autenticación y configuración de Servicios móviles
-------------------------------------------------------------------------------------------------------------

Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

     ![](./media/mobile-services-html-get-started-users/mobile-services-selection.png)

2.  Haga clic en la pestańa **Panel** y anote el valor de **Mobile Service URL**.

     ![](./media/mobile-services-html-get-started-users/mobile-service-uri.png)

    Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3.  Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:
	-   [Cuenta Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Inicio de sesión en Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Inicio de sesión en Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Inicio de sesión en Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

    	**Nota de seguridad**

    	El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

1.  Vuelva al Portal de administración, haga clic en la pestańa **Identity**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Save**.

     ![](./media/mobile-services-html-get-started-users/mobile-identity-tab.png)

El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

Restricción de permisosRestricción de permisos a usuarios autenticados
----------------------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestańa **Data** y, a continuación, en la tabla **TodoItem**.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png)

2.  Haga clic en la pestańa **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png)

3.  En el directorio de aplicaciones, inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.
    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

    **Nota:**

    En un equipo con Windows, escriba "R" cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

    De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

4.  Abra la URL <http://localhost:8000/> en un explorador web para iniciar la aplicación.

    No se pueden cargar los datos. Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

5.  (Opcional) Abra el depurador de scripts para su explorador web y vuelva a cargar la página. Compruebe que se produce un error de acceso denegado.

A continuación, actualizará la aplicación para permitir la autenticación antes de solicitar recursos del servicio móvil.

Incorporación de autenticaciónIncorporación de autenticación a la aplicación
----------------------------------------------------------------------------

**Nota:**

Como el inicio de sesión se realiza en una ventana emergente, debería invocar el método **login** desde el evento Click del botón. Si no, muchos exploradores suprimirían la ventana de inicio de sesión.

1.  Abra el archivo del proyecto index.html, localice el elemento H1 y agregue el siguiente fragmento de código debajo de él:

        <div id="logged-in"> 
             Ha iniciado sesión como <span id="login-name"></span>.
             <button id="log-out">Cerrar sesión</button>
        </div>
        <div id="logged-out">
            No ha iniciado sesión.
            <button>Iniciar sesión</button>
        </div>

    Esto le permite iniciar sesión en los Servicios móviles desde la página.

2.  En el archivo app.js, localice la línea de código situada al final del archivo que llama a la función refreshTodoItems y reemplácela por el código siguiente:

         function refreshAuthDisplay() {
             var isLoggedIn = client.currentUser !== null;
             $("#logged-in").toggle(isLoggedIn);
             $("#logged-out").toggle(!isLoggedIn);

             if (isLoggedIn) {
                 $("#login-name").text(client.currentUser.userId);
                 refreshTodoItems();
             }
         }

         function logIn() {
             client.login("facebook").then(refreshAuthDisplay, function(error){
                 alert(error);
             });
         }

         function logOut() {
             client.logout();
             refreshAuthDisplay();
             $('#summary').html('Debe iniciar sesión para obtener acceso a los datos.');
         }

         // En la página de inicio, capture los datos y configure los controladores de eventos.
         $(function () {
             refreshAuthDisplay();
             $('#summary').html('Debe iniciar sesión para obtener acceso a los datos.');           
             $("#logged-out button").click(logIn);
             $("#logged-in button").click(logOut);
         });

    De este modo se crea un conjunto de funciones para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook.

    **Nota:**

    Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método **login** anterior por uno de los siguientes: *microsoftaccount*, *facebook*, *twitter* o *google*.

3.  Vuelva al explorador en el que se ejecuta su aplicación y actualice la página.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

    **Nota:**

    Si usa Internet Explorer, podría recibir el error siguiente después de iniciar sesión: `Cannot reach window opener. It may be on a different Internet Explorer zone`. Esto sucede porque la ventana emergente se ejecuta en una zona de seguridad diferente (Internet) del localhost (intranet). Esto solo afecta a las aplicaciones durante el desarrollo que usan localhost. También puede abrir la pestańa **Seguridad** de **Opciones de Internet**, hacer clic en **Intranet local**, después en **Sitios** y deshabilitar **Detectar redes intranet automáticamente**. Acuérdese de volver a cambiar esta configuración cuando haya terminado la prueba.

Pasos siguientes
----------------

En el siguiente tutorial, [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html), usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Encontrará más información acerca de cómo usar los Servicios móviles con HTML/JavaScript en [Referencia conceptual de Servicios móviles HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

