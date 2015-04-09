<properties 
	pageTitle="Introducción a la autenticación (Windows Phone) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación de Windows Phone a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios en los Servicios móviles de Azure desde la aplicación de Windows Phone. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

>[AZURE.NOTE] Este tutorial solo es válido para aplicaciones Silverlight de Windows Phone 8.0 y Windows Phone 8.1. Si tiene una aplicación de la Tienda de Windows Phone 8.1 o una aplicación Windows universal, siga en su lugar la [versión de la aplicación Windows universal de este tema](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]
4. [Almacenamiento de tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles]. 


##<a name="register"></a> Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>En Visual Studio, abra el proyecto de aplicación cliente y asegúrese de que en App.xaml.cs la instancia de <strong>MobileServiceClient</strong> se configuró para usar la dirección URL de la nube para el servicio móvil.</li> 
<li><p>Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.</p>
   
   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET].

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Almacenamiento de tokens de autenticación en el cliente]: #tokens
[Pasos siguientes]: #next-steps


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Inicio de sesión único para aplicaciones de Windows Phone mediante Live Connect]: mobile-services-windows-phone-single-sign-on.md
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-windows-phone-get-started.md
[Introducción a los datos]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Introducción a la autenticación]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Autorización de usuarios con scripts]: mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts.md
[JavaScript y HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->