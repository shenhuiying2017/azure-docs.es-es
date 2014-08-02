<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Introducción a la autenticación en Servicios móviles
====================================================

[C\# para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users "JavaScript para Tienda Windows")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/en-us/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/ "Back-end de JavaScript")

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Puede ver la versión en vídeo de este tutorial haciendo clic en el clip de la derecha.

[Ver el tutorial (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Reproducir vídeo (en inglés)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) 10:04

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles](#register)
2.  [Restricción de los permisos de tabla a usuarios autenticados](#permissions)
3.  [Incorporación de autenticación a la aplicación](#add-authentication)

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started/).

> [WACOM.NOTE]En este tutorial se muestra el método básico que proporciona Servicios móviles para autenticar usuarios mediante una serie de proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Sin embargo, el método también requiere que los usuarios inicien sesión cada vez que se inicie la aplicación. Para usar Live Connect para proporcionar una experiencia de inicio de sesión único en la aplicación de la Tienda Windows, consulte el tema [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect](/en-us/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on).

Registro de la aplicación para la autenticación y configuración de Servicios móviles
------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Opcional) Complete los pasos en [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft](/en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/).

    **Nota:**

    Este paso es opcional porque solo se aplica al proveedor de inicio de sesión de la cuenta de Microsoft. Cuando registre la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, el cliente podrá volver a usar las credenciales de inicio de sesión de la cuenta de Microsoft para conseguir una experiencia de inicio de sesión único. Si no realiza este procedimiento, los usuarios de inicio de sesión de la cuenta de Microsoft visualizarán una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta de Microsoft.

Restricción de los permisos para los usuarios autenticados
----------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-get-started).

2.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

Incorporación de autenticación a la aplicación
----------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

Pasos siguientes
----------------

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles](/en-us/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts), usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library).

