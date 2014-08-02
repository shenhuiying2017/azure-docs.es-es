<properties pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[C\# para Tiendas Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users/ "Back-end de JavaScript")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación de Windows Phone. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/).

> [WACOM.NOTE]En este tutorial se muestra el método básico que proporciona Servicios móviles para autenticar usuarios mediante una serie de proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Sin embargo, el método también requiere que los usuarios inicien sesión cada vez que se inicie la aplicación. Para usar Live Connect para proporcionar una experiencia de inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Inicio de sesión único para aplicaciones de Windows Phone mediante Live Connect](/en-us/documentation/articles/mobile-services-windows-phone-single-sign-on).

Registro de la aplicación para la autenticación y configuración de Servicios móviles
------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  En Visual Studio, abra el archivo Web.config para el proyecto de servicios móviles y en la sección appSettings, defina el identificador de la aplicación y los valores secretos compartidos obtenidos del proveedor de identidades.

    Esta configuración se usa durante el desarrollo local. Después de publicar el proyecto de servicios móviles en Azure, los valores definidos en el portal sobrescriben esta configuración.

Restricción de los permisos para los usuarios autenticados
----------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  En Visual Studio 2013, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/).

2.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

Incorporación de autenticación a la aplicación
----------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

Pasos siguientes
----------------

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts), usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library).

