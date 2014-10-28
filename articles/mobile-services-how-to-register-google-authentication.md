<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registro de las aplicaciones para el inicio de sesión de Google con Servicios móviles

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Google para la autenticación con Servicios móviles de Azure.

<div class="dev-callout"><b>Nota:</b>
<p>Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una direcci&oacute;n de correo electr&oacute;nico verificada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Diríjase al sitio web [Google apis][], inicie sesión con las credenciales de su cuenta de Google, haga clic en **Crear proyecto**, proporcione un **Nombre de proyecto**, y haga clic en **Crear**.

    ![][]

2.  Haga clic en **API y autorización**, en **Credenciales**, y luego en **Crear identificador de cliente nuevo**.

    ![][1]

3.  Seleccione **Aplicación web**, escriba la dirección URL del servicio móvil en **Orígenes de JavaScript autorizados**, reemplace la dirección URL generada en **URI de redireccionamiento autorizados** por la dirección URL del servicio móvil con la ruta de acceso */login/google* anexada y, a continuación, haga clic en **Crear identificador de cliente**.

    > [WACOM.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso *signin-google* anexada como servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-google`.

    ![][2]

4.  En **Client ID for web applications**, tome nota de los valores de **Client ID** y **Client secret**.

    ![][3]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

De este modo ya estará listo para usar un inicio de sesión de Google para autenticarse en su aplicación proporcionando los valores de identificador de cliente y secreto de cliente a Servicios móviles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
