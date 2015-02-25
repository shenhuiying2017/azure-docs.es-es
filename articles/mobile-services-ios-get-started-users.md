<properties pageTitle="Introducción a la autenticación (iOS) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación iOS a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft." services="mobile-services" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación de iOS.  En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.  

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]
4. [Almacenamiento de tokens de autenticación en la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

Para completar este tutorial necesita XCode 4.5 y iOS 5.0 o versiones posteriores.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>En Xcode, abra el proyecto que ha creado al completar el tutorial <a href="/es-es/documentation/articles/mobile-services-ios-get-started">Introducción a los Servicios móviles</a>.</p></li>
<li><p>Presione el botón <strong>Run</strong> (Ejecutar) para crear el proyecto e iniciar la aplicación en el emulador de iPhone; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.<p>

   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Almacenamiento de tokens de autenticación en la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps
[Almacenamiento de tokens de autenticación en la aplicación]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portal de administración de Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
