<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn="" Gailey" />

# Introducción a la autenticación en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tema muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación universal para Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados][Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación][Incorporación de autenticación a la aplicación]
4.  [Almacenar tokens de autenticación en el cliente][Almacenar tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

> [WACOM.NOTE]Este tutorial muestra cómo autenticar usuarios en aplicaciones de la Tienda Windows y la Tienda de Windows Phone 8.1. Para obtener información sobre las aplicaciones de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte esta versión de [Introducción a la autenticación en Servicios móviles][Introducción a la autenticación en Servicios móviles].

> Este tutorial muestra el flujo de autenticación que administra Servicios móviles mediante varios proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con la autenticación administrada por el cliente e incluir el inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect][Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]. Con la autenticación administrada por el cliente, la aplicación puede acceder a los datos de usuario adicionales que mantiene el proveedor de identidades. Para obtener los mismos datos de usuario en el servicio móvil, llame a la función **user.getIdentities()** en los scripts del servidor. Para obtener más información, consulte [este artículo][este artículo].

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Opcional) Complete los pasos en [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft][Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft].

    <div class="dev-callout"><b>Nota:</b>
    <p>Este paso es opcional porque solo se aplica al proveedor de inicio de sesi&oacute;n de la cuenta de Microsoft. Cuando registre la informaci&oacute;n del paquete de la aplicaci&oacute;n de la Tienda Windows con Servicios m&oacute;viles, el cliente podr&aacute; volver a usar las credenciales de inicio de sesi&oacute;n de la cuenta de Microsoft para conseguir una experiencia de inicio de sesi&oacute;n &uacute;nico. Si no realiza este procedimiento, los usuarios de inicio de sesi&oacute;n de la cuenta de Microsoft visualizar&aacute;n una solicitud de inicio de sesi&oacute;n cada vez que se llame al m&eacute;todo de inicio de sesi&oacute;n. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta de Microsoft.</p>
    </div>

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  En Visual Studio, haga clic con el botón secundario en el proyecto de la Tienda Windows para la aplicación TodoList y con el botón primario en **Establecer como proyecto de inicio**.

2.  En el proyecto compartido, abra el archivo de proyecto App.xaml.cs, busque la definición de [MobileServiceClient][MobileServiceClient] y asegúrese de que se ha configurado para conectar con el servicio móvil que se ejecuta en Azure.

    <div class="dev-callout"><strong>Nota:</strong><p>Cuando se conecta una aplicaci&oacute;n a un servicio m&oacute;vil con las herramientas de Visual Studio, la herramienta genera dos conjuntos de definiciones <strong>MobileServiceClient</strong>, una para cada plataforma cliente. Ahora es el momento de simplificar el c&oacute;digo generado. Para ello, unifique las definiciones <strong>MobileServiceClient</strong> del contenedor <code data-inline="1">#if...#endif</code> en una &uacute;nica definici&oacute;n sin contenedor, que deben usar las dos versiones de la aplicaci&oacute;n.</p></div>

3.  Presione la tecla F5 para ejecutar la aplicación de la Tienda Windows y compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado).

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Almacenar el token de autorización en el cliente

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][Autorización en el servicio de usuarios de Servicios móviles], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Uso de un cliente .NET para Servicios móviles de Azure][Uso de un cliente .NET para Servicios móviles de Azure].



  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Almacenar tokens de autenticación en el cliente]: #tokens
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
  [Introducción a la autenticación en Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-users
  [Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [este artículo]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [MobileServiceClient]: http://msdn.microsoft.com/es-es/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-dotnet-authenticate-app]: ../includes/mobile-services-windows-universal-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Autorización en el servicio de usuarios de Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Uso de un cliente .NET para Servicios móviles de Azure]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
