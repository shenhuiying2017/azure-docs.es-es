<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[Tienda Windows C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Tienda Windows C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/en-us/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users/ "Back-end de JavaScript")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

[Ver el tutorial (en inglés)](http://go.microsoft.com/fwlink/?LinkId=298631) [Reproducir vídeo (en inglés)](http://go.microsoft.com/fwlink/?LinkId=298631) 10:50

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

    **Nota de seguridad**

    El secreto que genera el proveedor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

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

