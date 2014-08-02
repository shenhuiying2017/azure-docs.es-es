<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-ios-get-started-users/ "Back-end de JavaScript")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

Registro de la aplicación para la autenticación y configuración de Servicios móviles
------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  En Visual Studio, abra el archivo Web.config para el proyecto de servicio móvil y, en la sección de configuración de la aplicación, establezca los valores de identificador y secreto compartido de la aplicación proporcionados por el proveedor de identidades.

    Esta configuración se utiliza durante el desarrollo local. Tras publicar el proyecto de servicio móvil en Azure, esta configuración se sobrescribe con los valores establecidos en el portal.

Restricción de los permisos para los usuarios autenticados
----------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  En Xcode, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-ios-get-started).

2.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en el emulador de iPhone; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

    A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## Incorporación de autenticación a la aplicación 
[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

## Pasos siguientes 
En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: \#register 
[Restricción de los permisos de tabla a usuarios autenticados]: \#permissions 
[Incorporación de autenticación a la aplicación]: \#add-authentication 
[Pasos siguientes]:\#next-steps 


<!-- URLs. -->
[Introducción a los Servicios móviles]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ 
[Introducción a los datos]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[Introducción a la autenticación]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[Introducción a las notificaciones de inserción]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[Autorización de usuarios con scripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 

[Portal de administración de Azure]: https://manage.windowsazure.com/ 
[Referencia conceptual e instrucciones sobre .NET en los Servicios móviles]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Registro del paquete de aplicaciones de la Tienda Windows para la autenticación de Microsoft]: /en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
