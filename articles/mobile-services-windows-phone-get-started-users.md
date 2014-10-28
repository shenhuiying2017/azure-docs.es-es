<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# Introducción a la autenticación en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>En este tema se muestra c&oacute;mo autenticar usuarios en Servicios m&oacute;viles de Azure desde su aplicaci&oacute;n. En este tutorial podr&aacute; agregar la autenticaci&oacute;n al proyecto de inicio r&aacute;pido mediante un proveedor de identidades compatible con Servicios m&oacute;viles. Una vez que Servicios m&oacute;viles haya realizado la autenticaci&oacute;n y autorizaci&oacute;n correctamente, se mostrar&aacute; el valor de identificador de usuario.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">10:50:00</span></div>

</div>

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][]
2.  [Restricción de los permisos de tabla a usuarios autenticados][]
3.  [Incorporación de autenticación a la aplicación][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][].

> [WACOM.NOTE] En este tutorial se muestra el flujo de autenticación que administra Servicios móviles mediante una variedad de proveedores de identidades. Este método es fácil de configurar y es compatible con varios proveedores. Para usar Live Connect con una autenticación administrada por el cliente y proporcionar una experiencia de inicio de sesión único en la aplicación de Windows Phone, consulte el tema [Inicio de sesión único para aplicaciones de Windows Phone mediante Live Connect][]. Al usar la autenticación administrada por el cliente, su aplicación tiene acceso a los datos de usuario que mantiene el proveedor de identidades. Puede obtener los mismos datos de usuario en el servicio móvil al llamar a la función **user.getIdentities()** en los scripts de servidor. Para obtener más información, consulte [este artículo][].

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  En Visual Studio 2012 Express para Windows Phone, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles][].

2.  Presione la tecla F5 para ejecutar esta aplicación basada en el inicio rápido; compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app][]]

## <a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token][]]

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Ver el tutorial (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=298631
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-get-started
  [Inicio de sesión único para aplicaciones de Windows Phone mediante Live Connect]: /es-es/documentation/articles/mobile-services-windows-phone-single-sign-on
  [este artículo]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-windows-phone-authenticate-app]: ../includes/mobile-services-windows-phone-authenticate-app.md
  [mobile-services-windows-phone-authenticate-app-with-token]: ../includes/mobile-services-windows-phone-authenticate-app-with-token.md
  [Autorización en el servicio de usuarios de Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
