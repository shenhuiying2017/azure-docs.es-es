<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone" class="current">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-users/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">Ver el tutorial (en inglés)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en inglés)</span></a> <span class="time">10:50</span></div>
</div>

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).

Registro de la aplicaciónRegistro de la aplicación para la autenticación y configuración de Servicios móviles
-------------------------------------------------------------------------------------------------------------

Para poder autenticar usuarios, debe registrar la aplicación con un proveedor de identidades. A continuación, debe registrar el secreto de cliente generado por el proveedor con Servicios móviles.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, haga clic en el servicio móvil.

	![][1]

2.  Haga clic en la pestaña **Panel** y anote el valor de **Mobile Service URL**.

	![][2]

	Es posible que tenga que proporcionar ese valor al proveedor de identidades cuando registre la aplicación.

3.  Seleccione un proveedor de identidades compatible en la lista siguiente y siga los pasos para registrar la aplicación con ese proveedor:

-   [Cuenta Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Inicio de sesión en Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Inicio de sesión en Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Inicio de sesión en Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Recuerde anotar los valores secretos y la identidad del cliente que genera el proveedor.

    <div class="dev-callout"><b>Nota de seguridad</b>

    <p>El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.</p>
	</div>

1.  Vuelva al Portal de administración, haga clic en la pestaña **Identity**, especifique el identificador de la aplicación y los valores secretos compartidos que le proporcionó el proveedor de identidades y haga clic en **Save**.

	![][3]

El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

Restricción de los permisos para los usuarios autenticados
----------------------------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

	![][4]

2.  Haga clic en la pestaña **Permissions**, establezca todos los permisos en **Only authenticated users** y, a continuación, haga clic en **Save**. De esta forma, se garantiza que todas las operaciones en la tabla **TodoItem** requieren un usuario autenticado. Esto también simplifica los scripts del próximo tutorial, ya que no existirá la posibilidad de permitir usuarios anónimos.

	![][5]

3.  En Visual Studio 2012 Express para Windows Phone, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started-wp8).

4.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

	Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

Incorporación de autenticación a la aplicación
----------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

Pasos siguientes
----------------

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8), usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/