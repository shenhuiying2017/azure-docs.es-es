<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/23/2014" ms.author="ricksal"></tags>

# Introducción a la autenticación en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>En este tema se muestra c&oacute;mo autenticar usuarios en Servicios m&oacute;viles de Azure desde su aplicaci&oacute;n. En este tutorial podr&aacute; agregar la autenticaci&oacute;n al proyecto de inicio r&aacute;pido mediante un proveedor de identidades compatible con Servicios m&oacute;viles. Una vez que Servicios m&oacute;viles haya realizado la autenticaci&oacute;n y autorizaci&oacute;n correctamente, se mostrar&aacute; el valor de identificador de usuario.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a><span class="time">10:42</span></div>

</div>

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][]
2.  [Restricción de los permisos de tabla a usuarios autenticados][]
3.  [Incorporación de autenticación a la aplicación][]
4.  [Almacenamiento de tokens de autenticación en el cliente][]
5.  [Actualización de los tokens expirados][]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][].

Para completar este tutorial necesita Eclipse y Android 4.2 o versiones posteriores.

## <a name="register"></a><span class="short-header">Registro de la aplicación</span>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a><span class="short-header">Restricción de permisos</span>Restricción de permisos a usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  En Eclipse, abra el proyecto que ha creado al completar el tutorial [Introducción a los Servicios móviles][].

2.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación y, a continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

    Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a><span class="short-header">Incorporación de autenticación</span>Incorporación de autenticación a la aplicación

[WACOM.INCLUDE [mobile-services-android-authenticate-app][]]

## <a name="cache-tokens"></a>Almacenamiento en caché de tokens de autenticación en el cliente

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token][]]

## <a name="refresh-tokens"></a>Actualización de la memoria caché de los tokens

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token][]]

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts][], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Ver el tutorial (en inglés)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Almacenamiento de tokens de autenticación en el cliente]: #cache-tokens
  [Actualización de los tokens expirados]: #refresh-tokens
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-android
