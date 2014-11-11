<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registro de sus aplicaciones para usar un inicio de sesión de la cuenta Microsoft

Este tema indica cómo registrar su aplicación para que pueda usar Live Connect como proveedor de autenticación para Servicios móviles de Azure.

> [WACOM.NOTE] Para configurar la autenticación de la cuenta Microsoft de una aplicación Windows 8.1 o Windows Phone 8.1, consulte [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft][Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft].

1.  Vaya a la página [Mis aplicaciones][Mis aplicaciones] del Centro para desarrolladores de Live Connect e inicie sesión con cuenta Microsoft, si procede.

2.  Haga clic en **Create application** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **I accept**.

    ![][0]

    Así se registra la aplicación con Live Connect.

3.  Haga clic en **Configuración de API**, proporcione un valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` en **Dirección URL de redireccionamiento** y haga clic en **Guardar**.

    > [WACOM.NOTE] Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso *signin-microsoft* anexada como servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-microsoft`.

    ![][1]

    Esto habilita la autenticación de la cuenta Microsoft de la aplicación.

    > [WACOM.NOTE] Para un registro de aplicación Live Connect existente, es posible que primero tenga que habilitar la **seguridad de redireccionamiento mejorada**.

4.  Haga clic en la **Configuración de API** y anote los valores de **Id. de cliente** y **Secreto de cliente**.

    ![][2]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

De este modo ya estará listo para usar una cuenta Microsoft para autenticarse en su aplicación proporcionando los valores de Id. de cliente y secreto de cliente a Servicios móviles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft]: /es-es/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [0]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
