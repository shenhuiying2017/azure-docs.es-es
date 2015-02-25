<properties pageTitle="Introducción a la autenticación (Windows Phone) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación de Windows Phone a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">ver el tutorial</a><a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a> <span class="time">10:50</span></div>
</div>  

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles]. 

>[AZURE.NOTE]Este tutorial muestra el flujo de autenticación que administra Servicios móviles mediante varios proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con la autenticación administrada por el cliente e incluir el inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Inicio de sesión único para aplicaciones de Windows Phone mediante Live Connect]. Con la autenticación administrada por el cliente, la aplicación puede acceder a los datos de usuario adicionales que mantiene el proveedor de identidades. Para obtener los mismos datos de usuario en el servicio móvil, llame a la función **user.getIdentities()** en los scripts del servidor. Para obtener más información, consulte [este artículo](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles


[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 


##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. En Visual Studio 2012 Express para Windows Phone, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles]. 

4. Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación. 
   
   	Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. 

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Inicio de sesión único para aplicaciones de Windows Phone mediante Live Connect]: /es-es/documentation/articles/mobile-services-windows-phone-single-sign-on


<!--HONumber=42-->
