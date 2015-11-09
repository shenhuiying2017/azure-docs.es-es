<properties
	pageTitle="Configuración de la autenticación mediante Facebook para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Facebook para la aplicación de Servicios de aplicaciones."
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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Configuración de la aplicación para usar el inicio de sesión de Facebook

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar las Aplicaciones móviles de Azure para usar Facebook como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com].

## <a name="register"> </a>Registro de la aplicación con Facebook

1. Inicie sesión en el [Portal de administración de Azure] y vaya a la aplicación móvil. Copie la **Dirección URL**. La usará para configurar la aplicación de Facebook.
 
2. Haga clic en **Configuración**, **Autenticación del usuario** y, después, en **Facebook**. Ahora, copie el **URI de redireccionamiento** de la hoja de Facebook. La usará con la aplicación de Facebook.
 
3. En otra ventana del explorador, navegue hasta el sitio web de [Desarrolladores de Facebook] e inicie sesión con las credenciales de su cuenta de Facebook.

4. (Opcional) Si aún no está registrado, haga clic en **Aplicaciones**, luego en **Registrarse como desarrollador**, acepte la política y siga los pasos de registro.

5. Haga clic en **Mis aplicaciones** y luego en **Agregar una nueva aplicación**.

6. Seleccione **Sitio web** como plataforma. Elija un nombre exclusivo para la aplicación y, a continuación, haga clic en **Crear nuevo id. de aplicación de Facebook**.

7. Elija una categoría para la aplicación en la lista desplegable. A continuación, haga clic en **Crear id. de aplicación**.

8. En la siguiente página, seleccione **Omitir Inicio rápido** en la próxima visita en la esquina superior derecha. Esto le llevará al panel del desarrollador de la aplicación.

9. En el campo **Secreto de la aplicación**, haga clic en **Mostrar**, escriba la contraseña si se le solicita y, a continuación, anote los valores de **Id. de aplicación** y **Secreto de la aplicación**. Configurará estos valores en la hoja de configuración de autenticación de Facebook de la aplicación móvil.

	> [AZURE.NOTE]**Nota de seguridad** El secreto de la aplicación es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

10. En la barra de navegación de la izquierda, haga clic en **Configuración**. Escriba la **Dirección URL** de la aplicación móvil en **Dominios de aplicación** y escriba un **Correo electrónico de contacto**.

    ![][0]

11. Si no ve una sección de sitio web a continuación, haga clic en **Agregar plataforma** y seleccione **Sitio Web**. Escriba la **Dirección URL** de la aplicación móvil en el campo **Dirección URL del sitio** y haga clic en **Guardar cambios**.

12. Haga clic en la pestaña **Avanzado** y agregue el **URI de redireccionamiento** que copió anteriormente a los **URI de redireccionamiento OAuth válidos**. A continuación, haga clic en **Guardar cambios**. El URI de redireccionamiento es la dirección URL de la puerta de enlace de la aplicación móvil anexada a la ruta de acceso, _/signin-facebook_. Por ejemplo: `https://contosogateway.azurewebsites.net/signin-facebook`. Asegúrese de que está utilizando el esquema HTTPS.

13. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar otras cuentas de Facebook, haga clic en **Estado y revisión** en el panel de navegación de la izquierda. A continuación, haga clic en **Sí** para habilitar el acceso del público en general.


## <a name="secrets"> </a>Adición de información de Facebook a la aplicación móvil


12. De vuelta en el [Portal de administración de Azure], vaya de nuevo a la hoja de configuración de Facebook de la aplicación móvil. Pegue los valores de Id. de aplicación y Secreto de la aplicación que obtuvo anteriormente. A continuación, haga clic en **Guardar**.

    ![][1]

De este modo ya estará listo para usar Facebook para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /es-ES/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de administración de Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO1-->