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
	ms.date="09/18/2014" 
	ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

Este tema muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación universal para Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]
5. [Almacenamiento de tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles]. 

>[AZURE.NOTE]Este tutorial muestra cómo autenticar usuarios en aplicaciones de la Tienda Windows y la Tienda de Windows Phone 8.1. En el caso de aplicaciones de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a la autenticación en Servicios móviles](/es-es/documentation/articles/mobile-services-windows-phone-get-started-users).

>Este tutorial muestra el flujo de autenticación que administra Servicios móviles mediante varios proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con la autenticación administrada por el cliente e incluir el inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]. Con la autenticación administrada por el cliente, la aplicación puede acceder a los datos de usuario adicionales que mantiene el proveedor de identidades. Para obtener los mismos datos de usuario en el servicio móvil, llame a la función **user.getIdentities()** en los scripts del servidor. Para obtener más información, consulte [este artículo](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Opcional) Complete los pasos en <a href="/es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft</a>.</p>
<p>Tenga en cuenta que este paso es opcional porque solo se aplica al proveedor de inicio de sesión de la cuenta Microsoft. Cuando registre la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, el cliente podrá volver a usar las credenciales de inicio de sesión de la cuenta Microsoft para conseguir una experiencia de inicio de sesión único. Si no realiza este procedimiento, los usuarios de inicio de sesión de la cuenta Microsoft visualizarán una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta Microsoft.</p>
</li>
</ol>

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>En Visual Studio, haga clic con el botón derecho en el proyecto de la Tienda Windows para la aplicación TodoList y haga clic en <strong>Establecer como proyecto de inicio</strong>.</p></li>
<li><p>En el proyecto compartido, abra el archivo de proyecto App.xaml.cs, busque la definición de <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> y asegúrese de que se ha configurado para conectar con el servicio móvil que se ejecuta en Azure.</p>
<p>Tenga en cuenta que cuando se conecta una aplicación a un servicio móvil con las herramientas de Visual Studio, la herramienta genera dos conjuntos de definiciones <strong>MobileServiceClient</strong>, una para cada plataforma cliente. Ahora es el momento de simplificar el código generado. Para ello, unifique las definiciones <code>#if...#endif</code> del contenedor <strong>MobileServiceClient</strong> en una única definición sin contenedor, que deben usar las dos versiones de la aplicación. No necesitará hacer esto cuando descargue la aplicación de inicio rápido desde el Portal de administración de Azure.</p>
</li> 
<li><p>Presione la tecla F5 para ejecutar la aplicación de la Tienda Windows y compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado).</p>
   
   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Almacenamiento del token de autorización en el cliente

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET].

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Almacenamiento de tokens de autenticación en el cliente]: #tokens
[Pasos siguientes]:#next-steps


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript y HTML]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/


<!--HONumber=42-->
