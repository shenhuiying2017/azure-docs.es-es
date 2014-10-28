<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Twitter para la autenticación con Servicios móviles de Azure.

<div class="dev-callout"><b>Nota:</b>
<p>Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una direcci&oacute;n de correo electr&oacute;nico verificada. Para crear una cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div>

1.  Desplácese hasta el sitio web para [desarrolladores de Twitter][] (en inglés), inicie sesión con las credenciales de su cuenta de Twitter y haga clic en **Create a new application**.

    ![][]

2.  Escriba los valores de **Name**, **Description** y **Website** de su aplicación y especifique la dirección URL del servicio móvil anexada a la ruta de acceso */login/twitter* en **Callback URL**.

    > [WACOM.NOTE] Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil anexada a la ruta de acceso *signin-twitter* como servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-twitter`.

    ![][1]

3.  En la parte inferior de la página, lea y acepte los términos, escriba las palabras correctas del código CAPTCHA y, a continuación, haga clic en **Create your Twitter application**.

    ![][2]

    De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

4.  Tome nota de los valores indicados en **Consumer key** y **Consumer secret**.

    ![][3]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de usuario es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

5.  Haga clic en la pestaña **Settings**, desplácese hacia abajo y marque la casilla **Allow this application to be used to sign in with Twitter** y, a continuación, haga clic en **Update this Twitter application's settings**.

    ![][4]

De este modo ya estará listo para usar un inicio de sesión de Twitter para autenticarse en su aplicación proporcionando los valores de clave de usuario y secreto de usuario a Servicios móviles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [twitter.com]: http://go.microsoft.com/fwlink/p/?LinkID=268287
  [desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
