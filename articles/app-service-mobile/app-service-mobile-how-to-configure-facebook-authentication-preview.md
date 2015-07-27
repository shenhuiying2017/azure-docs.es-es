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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Configuración de la aplicación para usar el inicio de sesión de Facebook

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Facebook como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com].

## <a name="register"> </a>Registro de la aplicación con Facebook

1. Desplácese hasta el sitio web para [desarrolladores de Facebook] e inicie sesión con las credenciales de su cuenta de Facebook.

2. (Opcional) Si aún no está registrado, haga clic en **Aplicaciones**, luego en **Registrarse como desarrollador**, acepte la política y siga los pasos de registro.

3. Haga clic en **Aplicaciones** y luego en **Crear una nueva aplicación**.

4. Seleccione **Sitio web** como plataforma. Elija un nombre exclusivo para la aplicación y, a continuación, haga clic en **Crear nuevo id. de aplicación de Facebook**.

5. Elija una categoría para la aplicación en la lista desplegable. A continuación, haga clic en **Crear id. de aplicación**.

6. En la siguiente página, seleccione **Omitir Inicio rápido** en la próxima visita en la esquina superior derecha. Esto le llevará al panel del desarrollador de la aplicación.

7. En el campo **Secreto de la aplicación**, haga clic en **Mostrar**, escriba la contraseña si se le solicita y, a continuación, anote los valores de **Id. de aplicación** y **Secreto de la aplicación**.

	> [AZURE.NOTE]**Nota de seguridad** El secreto de la aplicación es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

8. En la barra de navegación de la izquierda, haga clic en **Configuración**. Escriba la dirección URL de la puerta de enlace en **Dominios de aplicación** y escriba un **correo electrónico de contacto**.

    ![][0]

9. Si no ve una sección de sitio web a continuación, haga clic en **Agregar plataforma** y seleccione **Sitio Web**. Escriba la dirección URL de la puerta de enlace de Servicios de aplicaciones en **Dirección URL del sitio** y haga clic en **Guardar cambios**.

10. Haga clic en la pestaña **Avanzado** y agregue el **URI de redireccionamiento a URI de redireccionamiento OAuth válido**. A continuación, haga clic en **Guardar cambios**. El URI de redireccionamiento es la dirección URL de la puerta de enlace anexada a la ruta de acceso _/signin-facebook_. Por ejemplo: `https://contosogateway.azurewebsites.net/signin-facebook`. Asegúrese de que está utilizando el esquema HTTPS.

11. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar otras cuentas de Facebook, haga clic en **Estado y revisión** en el panel de navegación de la izquierda. A continuación, haga clic en **Sí** para habilitar el acceso del público en general.


## <a name="secrets"> </a>Adición de información de Facebook a la aplicación móvil


12. Inicie sesión en el [Portal de administración de Azure] y vaya a la puerta de enlace de Servicios de aplicaciones.

13. En **Configuración**, elija **Identidad** y, a continuación, seleccione **Facebook**. Pegue los valores de Id. de aplicación y Secreto de la aplicación que obtuvo anteriormente. A continuación, haga clic en **Guardar**.

    ![][1]

De este modo ya estará listo para usar Facebook para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-settings.png

<!-- URLs. -->
[desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de administración de Azure]: https://portal.azure.com/
 

<!---HONumber=July15_HO3-->