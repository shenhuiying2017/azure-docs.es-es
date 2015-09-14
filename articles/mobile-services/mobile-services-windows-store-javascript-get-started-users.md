<properties
	pageTitle="Introducción a la autenticación (JavaScript) | Microsoft Azure"
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación de JavaScript de la Tienda Windows a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla para los usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]
4. [Almacenar tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

>[AZURE.NOTE]Este tutorial muestra el flujo de autenticación que administra Servicios móviles mediante varios proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con la autenticación administrada por el cliente e incluir el inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]. Con la autenticación administrada por el cliente, la aplicación puede acceder a los datos de usuario adicionales que mantiene el proveedor de identidades. Para obtener los mismos datos de usuario en el servicio móvil, llame a la función **user.getIdentities()** en los scripts del servidor. Para obtener más información, consulte [este artículo](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>(Opcional) Complete los pasos en <a href="/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft</a>.</p>


	<p>Tenga en cuenta que este paso es opcional porque solo se aplica al proveedor de inicio de sesión de la cuenta Microsoft. Cuando registre la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, el cliente podrá volver a usar las credenciales de inicio de sesión de la cuenta Microsoft para conseguir una experiencia de inicio de sesión único. Si no realiza este procedimiento, los usuarios de inicio de sesión de la cuenta Microsoft visualizarán una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta Microsoft.</p>
</li>
</ol>
El servicio móvil y la aplicación están ahora configurados para que funcionen con el proveedor de autenticación seleccionado.

##<a name="permissions"></a> Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial <a href="/develop/mobile/tutorials/get-started/">Introducción a los Servicios móviles</a>.</p></li>
<li><p>Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.</p>

   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a> Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

##<a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][Authorize users with scripts], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles.


<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla para los usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Almacenar tokens de autenticación en el cliente]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]: mobile-services-windows-store-javascript-single-sign-on.md
[Introducción a los Servicios móviles]: ../mobile-services-windows-store-get-started.md
[Get started with data]: mobile-services-windows-store-javascript-get-started-data.md
[Get started with authentication]: mobile-services-windows-store-javascript-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-javascript-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Register your Windows Store app package for Microsoft authentication]: /develop/mobile/how-to-guides/register-windows-store-app-package

<!---HONumber=September15_HO1-->