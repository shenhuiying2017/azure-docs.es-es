<properties
	pageTitle="Incorporación de autenticación a una aplicación de Servicios móviles de Azure existente (iOS)  |  Centro de desarrollo móvil"
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación iOS a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# Incorporación de autenticación a una aplicación de Servicios móviles de Azure existente

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades admitido. Este tutorial está basado en el [Tutorial de inicio rápido de Servicios móviles], que debe completar primero.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

En Xcode, abra el proyecto. Presione el botón **Ejecutar** para iniciar la aplicación. Compruebe que se produce una excepción con el código de estado 401 (No autorizado) después de iniciarse la aplicación. Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Almacenamiento de tokens de autenticación en la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de usuarios de Servicios móviles], podrá usar el valor de identificador de usuario para filtrar los datos devueltos.

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps
[Almacenamiento de tokens de autenticación en la aplicación]:#store-authentication

<!-- URLs. -->
[Autorización en el servicio de usuarios de Servicios móviles]: mobile-services-dotnet-backend-service-side-authorization.md
[Tutorial de inicio rápido de Servicios móviles]: mobile-services-dotnet-backend-ios-get-started.md
[Introducción a los datos]: mobile-services-dotnet-backend-ios-get-started-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-ios-get-started-push.md
[Autorización de usuarios con scripts]: mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->