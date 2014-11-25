<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn="" Gailey" />

# Introducción a la autenticación en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tema se muestra c&oacute;mo autenticar usuarios en Servicios m&oacute;viles de Azure desde su aplicaci&oacute;n. En este tutorial podr&aacute; agregar la autenticaci&oacute;n al proyecto de inicio r&aacute;pido mediante un proveedor de identidades compatible con Servicios m&oacute;viles. Una vez que Servicios m&oacute;viles haya realizado la autenticaci&oacute;n y autorizaci&oacute;n correctamente, se mostrar&aacute; el valor de identificador de usuario.</p>
<p>Puede ver la versi&oacute;n en v&iacute;deo de este tutorial haciendo clic en el clip de la derecha.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">10:04</span></div>

</div>

> [WACOM.NOTE]Actualmente, no se admite la autenticación para las aplicaciones de Windows Phone 8.1 de la Tienda Windows si se usa la biblioteca de clientes JavaScript de Servicios móviles. Si tiene un proyecto de aplicación universal de Windows que usa el cliente JavaScript, por el momento no podrá autenticar los usuarios en Windows Phone.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados][Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación][Incorporación de autenticación a la aplicación]
4.  [Almacenamiento de tokens de autenticación en el cliente][Almacenamiento de tokens de autenticación en el cliente]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

> [WACOM.NOTE] En este tutorial se muestra el flujo de autenticación que administra Servicios móviles mediante una variedad de proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con una autenticación administrada por el cliente y proporcionar una experiencia de inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect][Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]. Al usar la autenticación administrada por el cliente, su aplicación tiene acceso a los datos de usuario que mantiene el proveedor de identidades. Puede obtener los mismos datos de usuario en el servicio móvil al llamar a la función **user.getIdentities()** en los scripts de servidor. Para obtener más información, consulte [este artículo][este artículo].

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Opcional) Complete los pasos en [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft][Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft].

    <div class="dev-callout"><b>Nota:</b>
    <p>Este paso es opcional porque solo se aplica al proveedor de inicio de sesi&oacute;n de la cuenta de Microsoft. Cuando registre la informaci&oacute;n del paquete de la aplicaci&oacute;n de la Tienda Windows con Servicios m&oacute;viles, el cliente podr&aacute; volver a usar las credenciales de inicio de sesi&oacute;n de la cuenta de Microsoft para conseguir una experiencia de inicio de sesi&oacute;n &uacute;nico. Si no realiza este procedimiento, los usuarios de inicio de sesi&oacute;n de la cuenta de Microsoft visualizar&aacute;n una solicitud de inicio de sesi&oacute;n cada vez que se llame al m&eacute;todo de inicio de sesi&oacute;n. Complete este paso cuando tenga previsto utilizar el proveedor de identidades de la cuenta de Microsoft.</p>
    </div>

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles][1].

2.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Almacenamiento del token de autorización en el cliente

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][Autorización en el servicio de usuarios de Servicios móviles], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles. Obtenga más información sobre cómo usar Servicios móviles con .NET en [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET].

 


  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Almacenamiento de tokens de autenticación en el cliente]: #tokens
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-get-started/
  [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [este artículo]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [1]: /es-es/documentation/articles/mobile-services-windows-store-get-started
  [Autorización en el servicio de usuarios de Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
