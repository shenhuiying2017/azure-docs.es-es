<properties pageTitle="Introducción a la autenticación (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey" />

# Incorporación de autenticación a la aplicación de Servicios móviles 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tema muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación universal para Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que se haya realizado la autenticación y autorización correctamente en los Servicios móviles, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]
4. [Almacenar tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a Servicios móviles]. 

>[WACOM.NOTE]Este tutorial muestra cómo autenticar usuarios en aplicaciones de la Tienda Windows y la Tienda de Windows Phone 8.1. Para obtener información sobre las aplicaciones de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a la autenticación en Servicios móviles](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users).

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>En Visual Studio, haga clic con el botón secundario en el proyecto de la Tienda Windows para la aplicación TodoList y con el botón primario en <strong>Establecer como proyecto de inicio</strong>.</p></li>
<li><p>En el proyecto compartido, abra el archivo de proyecto App.xaml.cs, busque la definición de <a href="http://msdn.microsoft.com/es-es/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> y asegúrese de que se ha configurado para conectar con el servicio móvil que se ejecuta en Azure.</p>
<div class="dev-callout"><strong>Nota</strong><p>Cuando se conecta una aplicación a un servicio móvil con las herramientas de Visual Studio, la herramienta genera dos conjuntos de definiciones <strong>MobileServiceClient</strong>, una para cada plataforma cliente. Ahora es el momento de simplificar el código generado. Para ello, unifique las definiciones <strong>MobileServiceClient</strong> del contenedor <code>#if...#endif</code> en una única definición sin contenedor, que deben usar las dos versiones de la aplicación. No necesitará hacer esto cuando descargue la aplicación de inicio rápido desde el Portal de administración de Azure.</p></div>
</li> 
<li><p>Presione la tecla F5 para ejecutar la aplicación de la Tienda Windows y compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado).</p>
   
   	<p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla <em>TodoItem</em> requiere ahora autenticación.</p></li>
</ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

[WACOM.NOTE]Si ha registrado la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, debe llamar al método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> proporcionando un valor <strong>true</strong> para el parámetro <em>useSingleSignOn</em>. Si no realiza este procedimiento, los usuarios seguirán visualizando una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión.

##<a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de los usuarios de Servicios móviles][Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET].

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Almacenar tokens de autenticación en el cliente]: #tokens
[Pasos siguientes]:#next-steps


<!-- URLs. -->
[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK de Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript y HTML]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
