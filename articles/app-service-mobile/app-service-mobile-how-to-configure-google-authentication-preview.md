<properties 
	pageTitle="Configuración de la autenticación mediante Google para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Google para la aplicación de Servicios de aplicaciones." 
    services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Configuración de la aplicación para usar el inicio de sesión de Google

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Google como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Registro de la aplicación con Google

1. Vaya al sitio web de <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API de Google</a>, inicie sesión con sus credenciales de la cuenta de Google, haga clic en **Crear proyecto**, proporcione un **Nombre de proyecto** y haga clic en **Crear**.

2. En la barra de navegación de la izquierda, haga clic en **API y autenticación**. A continuación, haga clic en **Pantalla de consentimiento**. Seleccione la **dirección de correo electrónico** y, a continuación, especifique un **nombre de producto**. A continuación, haga clic en **Guardar**.

3. También en **API y autenticación**, seleccione **API** y habilite la **API de Google+**.

4. Una vez más, en **API y autenticación**, seleccione **Credenciales** y, a continuación, **Crear nuevo Id. de cliente**.

5. Seleccione **Aplicación web**. Escriba la dirección URL de la puerta de enlace de Servicios de aplicaciones en **Orígenes de JavaScript autorizados** y, a continuación, reemplace la dirección URL generada en el **URI de redirección autorizado** con la dirección URL de la puerta de enlace que se anexa a la ruta de acceso, _/signin-google_. Por ejemplo: `https://contoso.azurewebsites.net/signin-google`. Asegúrese de que está utilizando el esquema HTTPS. A continuación, haga clic en **Crear Id. de cliente**.

     ![][0]

6. En **Id. de cliente para aplicaciones web**, anote los valores de **Id. de cliente** y **Secreto de cliente**.

    > [AZURE.IMPORTANT]El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.


## <a name="secrets"> </a>Adición de información de Google a la aplicación móvil

7. Inicie sesión en el [Portal de administración de Azure] y vaya a la puerta de enlace de Servicios de aplicaciones.

8. En **Configuración**, elija **Identidad** y, a continuación, seleccione **Google**. Pegue los valores de Id. de cliente y Secreto del cliente que obtuvo anteriormente. A continuación, haga clic en **Guardar**.

     ![][1]


De este modo ya estará listo para usar Google para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de administración de Azure]: https://portal.azure.com/

<!--HONumber=54--> 