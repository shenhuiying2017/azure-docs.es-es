<properties
	pageTitle="Configuración de la autenticación mediante Twitter para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Twitter para la aplicación de Servicios de aplicaciones."
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

# Configuración de la aplicación para usar el inicio de sesión de Twitter

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Twitter como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico verificada. Para crear una cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registro de la aplicación con Twitter

1. Desplácese hasta el sitio web para [desarrolladores de Twitter], inicie sesión con las credenciales de la cuenta de Twitter y haga clic en **Crear nueva aplicación**.

2. Escriba los valores de **Nombre**, **Descripción** y **Sitio web** para la aplicación. A continuación, en **URL de devolución de llamada**, escriba la dirección URL de la puerta de enlace anexada a la ruta de acceso, _/signin-twitter_. Por ejemplo: `https://contosogateway.azurewebsites.net/signin-twitter`. Asegúrese de que está utilizando el esquema HTTPS.

    ![][0]

3.  En la parte inferior de la página, lea y acepte los términos. A continuación, haga clic en **Crear aplicación de Twitter**. De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

4. Haga clic en la pestaña **Configuración**, active **Permitir que esta aplicación se use para iniciar sesión en Twitter** y, a continuación, haga clic en **Actualizar la configuración de esta aplicación de Twitter**.

5. Seleccione la pestaña **Claves y tokens de acceso**. Tome nota de los valores de **Clave de consumidor (clave de API)** y **Secreto del consumidor (secreto de API)**.

    > [AZURE.NOTE]El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.


## <a name="secrets"> </a>Adición de información de Twitter a la aplicación móvil

6. Inicie sesión en el [Portal de administración de Azure] y vaya a la puerta de enlace de Servicios de aplicaciones.

7. En **Configuración**, elija **Identidad** y, a continuación, seleccione **Twitter**. Pegue los valores de Id. de aplicación y Secreto de la aplicación que obtuvo anteriormente. A continuación, haga clic en **Guardar**.

    ![][1]

De este modo ya estará listo para usar Twitter para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication-preview/app-service-twitter-settings.png

<!-- URLs. -->

[desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de administración de Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-dotnet-backend-xamarin-ios-get-started-users-preview.md
 

<!---HONumber=July15_HO4-->