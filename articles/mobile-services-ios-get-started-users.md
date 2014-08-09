<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[C\# para Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users "JavaScript para Tienda Windows")[Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/es-es/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/es-es/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/es-es/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")

[Back-end de .NET](/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ "Back-end de .NET") | [Back-end de JavaScript](/es-es/documentation/articles/mobile-services-ios-get-started-users/ "Back-end de JavaScript")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación de iOS. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

Para completar este tutorial necesita XCode 4.5 y iOS 5.0 o versiones posteriores.

Registro de la aplicación para la autenticación y configuración de Servicios móviles
------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

Restricción de los permisos para los usuarios autenticados
----------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  En Xcode, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles](/es-es/documentation/articles/mobile-services-ios-get-started).

2.  Presione el botón **Run** para compilar el proyecto e iniciar la aplicación en el emulador de iPhone y, a continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

    A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil. ##Incorporación de autenticación a la aplicación [WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] ## Pasos siguientes En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: \#register [Restricción de los permisos de tabla a usuarios autenticados]: \#permissions [Incorporación de autenticación a la aplicación]: \#add-authentication [Pasos siguientes]:\#next-steps [4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png [5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png [13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png [14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png [15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png [Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582 [Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039 [SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253 [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios [Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios [Portal de administración de Azure]: https://manage.windowsazure.com/


