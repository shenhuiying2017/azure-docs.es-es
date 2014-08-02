<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Register your apps for Google login with Mobile Services" authors="" solutions="" manager="" editor="" />

Registro de las aplicaciones para el inicio de sesión de Google con Servicios móviles
=====================================================================================

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Google para la autenticación con Servicios móviles de Azure.

**Nota:**

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google con una dirección de correo electrónico verificada. Para crear una nueva cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Desplácese hasta el sitio web de las [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) (en inglés), inicie sesión con las credenciales de la cuenta de Google y, a continuación, haga clic en **Create project...**

	![][1]   

2.  Haga clic en **API Access** y después en **Create an OAuth 2.0 client ID...**.

	![][2]

3.  En **Branding Information**, escriba un valor para **Product name** y, a continuación, haga clic en **Next**.

	![][3]

4.  En **Client ID Settings**, seleccione **Web application**, escriba la dirección URL del servicio móvil en **Your site or hostname**, haga clic en **more options**, reemplace la dirección URL generada en **Authorized Redirect URIs** por la dirección URL del servicio móvil con la ruta de acceso */login/google* anexada y, a continuación, haga clic en **Create client ID**.

	![][4]

    > [WACOM.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso *signin-google* anexada como servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-google`.

5.  En **Client ID for web applications**, tome nota de los valores de **Client ID** y **Client secret**.

	![][5]

    **Nota de seguridad**

    El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

De este modo ya estará listo para usar un inicio de sesión de Google para autenticarse en su aplicación proporcionando los valores de identificador de cliente y secreto de cliente a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/