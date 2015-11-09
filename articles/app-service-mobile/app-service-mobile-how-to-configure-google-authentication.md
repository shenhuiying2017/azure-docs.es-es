<properties
	pageTitle="Configuración de la autenticación mediante Google para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Google para la aplicación de Servicios de aplicaciones."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Configuración de la aplicación para usar el inicio de sesión de Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar las aplicaciones móviles para usar Google como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Inicie sesión en el [Portal de administración de Azure] y vaya a la aplicación móvil. Copie la **Dirección URL**. La usará más adelante con la aplicación de Google.
 
2. Haga clic en **Configuración**, **Autenticación del usuario** y luego en **Google**. Copie la **URI de redirección**. La usará para configurar la aplicación de Google.

3. Diríjase al sitio web [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), inicie sesión con las credenciales de su cuenta de Google, haga clic en **Crear proyecto**, proporcione un **Nombre de proyecto**, y haga clic en **Crear**.

4. En la barra de navegación de la izquierda, haga clic en **API y autenticación** y luego, en **API sociales**, haga clic en **API de Google+** > **Habilitar API**.

5. Haga clic en**API y autenticación** > **Credenciales** > **Pantalla de consentimiento de OAuth**, seleccione su **Dirección de correo electrónico**, escriba un **Nombre de producto** y haga clic en **Guardar**.

6. En la pestaña **Credenciales**, haga clic en **Agregar credenciales** > **Id. de cliente de OAuth 2.0** y luego seleccione **Aplicación web**.

7. Pegue la **URL** de las aplicaciones móviles que copió anteriormente en **Orígenes de JavaScript autorizados** y luego pegue el **URI de redirección** que copió antes en la **el URI de redirección autorizado**. Este URI de redirección es la puerta de enlace de la aplicación móvil anexada a la ruta de acceso, _/signin-google_. Por ejemplo: `https://contosogateway.azurewebsites.net/signin-google`. Asegúrese de que está utilizando el esquema HTTPS. A continuación, haga clic en **Crear**.

8. En la siguiente pantalla, tome nota de los valores de id. de cliente y el secreto del cliente.

    > [AZURE.IMPORTANT]El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

9. De vuelta en el [Portal de administración de Azure], en la hoja de configuración de Google de la aplicación móvil, pegue los valores de Id. de cliente y Secreto de cliente que obtuvo anteriormente. A continuación, haga clic en **Guardar**.

     ![][1]

De este modo ya estará listo para usar Google para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de administración de Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO1-->