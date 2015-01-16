<properties pageTitle="Registro para autenticación de Microsoft - Servicios móviles" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Obtenga información acerca de cómo registrarse para la autenticación de Microsoft en la aplicación de Servicios móviles de Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registro de sus aplicaciones para usar un inicio de sesión de la cuenta Microsoft

Este tema indica cómo registrar su aplicación para que pueda usar Live Connect como proveedor de autenticación para Servicios móviles de Azure. 

>[WACOM.NOTE]Para configurar la autenticación de cuentas de Microsoft para una aplicación universal de Windows o para proporcionar una experiencia de inicio de sesión único para una aplicación de la Tienda Windows, consulte [Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft](/en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Navegue a la página <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Mis aplicaciones</a> del Centro para desarrolladores de Live Connect e inicie sesión con cuenta Microsoft, si procede. 

2. Haga clic en **Crear aplicación** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **Acepto**.

   	![][1] 

   	Así se registra la aplicación con Live Connect.

3. Haga clic en **Configuración de API**, proporcione un valor de `https://<servicio_móvil>.azure-mobile.net/login/microsoftaccount` en **Dirección URL de redireccionamiento** y, continuación, haga clic en **Guardar**.

	>[WACOM.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso _signin-microsoft_ anexada como servicio .NET, por ejemplo <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Esto habilita la autenticación de la cuenta Microsoft de la aplicación.

	>[WACOM.NOTE]Para un registro de aplicación Live Connect existente, es posible que primero tenga que habilitar la **seguridad de redireccionamiento mejorada**..

4. Haga clic en la **Configuración de API** y anote los valores de **Id. de cliente** y **Secreto de cliente**.. 

   	![][2]

    <div class="dev-callout"><b>Nota de seguridad</b>
	<p>El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.</p>
    </div>

De este modo ya estará listo para usar una cuenta Microsoft para autenticarse en su aplicación proporcionando los valores de Id. de cliente y secreto de cliente a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portal de administración de Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
