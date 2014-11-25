<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your apps for Twitter login with Mobile Services" authors="" solutions="" manager="" editor="" />

Registro de las aplicaciones para el inicio de sesión en Twitter con Servicios móviles
======================================================================================

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Twitter para la autenticación con Servicios móviles de Azure.

**Nota:**

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico verificada. Para crear una cuenta de Twitter, vaya a [twitter.com](http://go.microsoft.com/fwlink/p/?LinkID=268287).

1.  Desplácese hasta el sitio web para [desarrolladores de Twitter](http://go.microsoft.com/fwlink/p/?LinkId=268300) (en inglés), inicie sesión con las credenciales de la cuenta de Twitter y haga clic en **Create a new application**.

	![][1]

2.  Escriba los valores de **Name**, **Description** y **Web Site** de su aplicación y especifique la dirección URL del servicio móvil en **Callback URL**.

	![][2]

    > [WACOM.NOTE]Para un servicio móvil de back-end de .NET publicado en Azure con Visual Studio, la dirección URL de redireccionamiento es la dirección URL del servicio móvil con la ruta de acceso *signin-twitter* anexada como servicio .NET, por ejemplo `https://todolist.azure-mobile.net/signin-twitter`.
    > El valor **Web Site** es obligatorio, pero no se usa.

3.  En la parte inferior de la página, lea y acepte los términos, escriba las palabras correctas del código CAPTCHA y, a continuación, haga clic en **Create your Twitter application**.

![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png) De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

1.  Tome nota de los valores indicados en **Consumer key** y **Consumer secret**.

	![][4]

    **Nota de seguridad**

    El secreto de usuario es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

2.  Haga clic en la pestaña **Settings**, desplácese hacia abajo y marque la casilla **Allow this application to be used to sign in with Twitter** y, a continuación, haga clic en **Update this Twitter application's settings**.

	![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png)

De este modo ya estará listo para usar un inicio de sesión de Twitter para autenticarse en su aplicación proporcionando los valores de clave de usuario y secreto de usuario a Servicios móviles.




[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png



[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/