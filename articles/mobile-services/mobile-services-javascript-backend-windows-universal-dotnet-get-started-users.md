<properties 
	pageTitle="Introducción a la autenticación (Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación de la Tienda Windows a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)] 

Este tema muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación universal para Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla para los usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]
5. [Almacenar tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

>[AZURE.NOTE]Este tutorial muestra cómo autenticar usuarios en aplicaciones de la Tienda Windows y la Tienda de Windows Phone 8.1. Para una aplicación de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a la autenticación en Servicios móviles](mobile-services-windows-phone-get-started-users.md).

>Este tutorial muestra el flujo de autenticación que administra Servicios móviles mediante varios proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con la autenticación administrada por el cliente e incluir el inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Autenticación de la aplicación de la Tienda Windows con la autenticación administrada por el cliente mediante la cuenta de Microsoft](mobile-services-windows-store-dotnet-single-sign-on.md). Con la autenticación administrada por el cliente, la aplicación puede acceder a los datos de usuario adicionales que mantiene el proveedor de identidades. Para obtener los mismos datos de usuario en el servicio móvil, llame a la función **user.getIdentities()** en los scripts del servidor. Para obtener más información, consulte [este artículo](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)] 
 
>[AZURE.NOTE]Cuando se conecta una aplicación a un servicio móvil con las herramientas de Visual Studio, la herramienta genera dos conjuntos de definiciones **MobileServiceClient**, una para cada plataforma cliente. Ahora es el momento de simplificar el código generado. Para ello, unifique las definiciones del contenedor `#if...#endif` **MobileServiceClient** en una única definición sin contenedor, que deben usar las dos versiones de la aplicación. No necesitará hacer esto cuando descargue la aplicación de inicio rápido desde el Portal de administración de Azure.

##<a name="add-authentication"></a> Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

Ahora, cualquier usuario autenticado por los proveedores de identidad de confianza puede tener acceso a la tabla *TodoItem*. Para proteger mejor datos específicos del usuario, también debe implementar la autorización. Para ello obtendrá el identificador de usuario de un usuario determinado, que puede utilizarse para determinar el nivel de acceso que el usuario debe tener para un recurso determinado.

##<a name="tokens"></a>Almacenamiento del token de autorización en el cliente

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles](mobile-services-javascript-backend-service-side-authorization.md), usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET].

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla para los usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Almacenar tokens de autenticación en el cliente]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introducción a los Servicios móviles]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54--> 