<properties
	pageTitle="Incorporación de autenticación a una aplicación de Servicios móviles de Azure existente (iOS) | Centro de desarrollo móvil"
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

En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades admitido.

Este tutorial está basado en el [Tutorial de inicio rápido de Servicios móviles], que debe completar primero.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

En Xcode, abra el proyecto. Presione el botón **Ejecutar** para iniciar la aplicación. Compruebe que se produce una excepción con el código de estado 401 (No autorizado) después de iniciarse la aplicación. Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Almacenamiento de tokens de autenticación en la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de usuarios de Servicios móviles], podrá usar el valor de identificador de usuario para filtrar los datos devueltos.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Autorización en el servicio de usuarios de Servicios móviles]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Tutorial de inicio rápido de Servicios móviles]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54-->