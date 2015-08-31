<properties
	pageTitle="Configuración de la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
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

# Configuración de la aplicación para usar el inicio de sesión de la cuenta Microsoft

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

En este tema se muestra cómo configurar las Aplicaciones móviles de Azure para usar la cuenta Microsoft como proveedor de autenticación.

## <a name="register"> </a>Registro de la aplicación con la cuenta Microsoft

1. Inicie sesión en el [Portal de administración de Azure] y vaya a la aplicación móvil.

2. Haga clic en **Configuración**, **Autenticación del usuario** y, después, haga clic en **Cuenta Microsoft**. Copie la **Dirección URL de redireccionamiento**. La usará para configurar una aplicación nueva para su cuenta Microsoft.

3. Vaya a la página [Mis aplicaciones] del Centro para desarrolladores de la cuenta Microsoft e inicie sesión con su cuenta Microsoft, si procede.

4. Haga clic en **Crear aplicación** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **Acepto**.

5. Haga clic en **Configuración de API**. Seleccione **Sí** para **Aplicación cliente móvil o de escritorio**. En el campo **Dirección URL de redireccionamiento**, escriba la **Dirección URL de redireccionamiento** que copió anteriormente. Se trata de la puerta de enlace de la aplicación móvil anexada, _/signin-microsoft_. Por ejemplo: `https://contosogateway.azurewebsites.net/signin-microsoft`. Asegúrese de que está utilizando el esquema HTTPS. Después de escribir la dirección URL de redireccionamiento, haga clic en **Guardar**.

	![][0]

	>[AZURE.NOTE]Para un registro de aplicación de la cuenta Microsoft existente, es posible que primero tenga que habilitar **Seguridad de redireccionamiento mejorada**.

4. Haga clic en **Configuración de aplicaciones** y tome nota de los valores de **Id. de cliente** y **Secreto de cliente**.

    > [AZURE.NOTE]El secreto de cliente es una credencial de seguridad importante, No comparta este secreto del cliente con nadie ni lo distribuya en una aplicación cliente.


## <a name="secrets"> </a>Adición de información de la cuenta de Microsoft a la aplicación móvil

1. De vuelta en el [Portal de administración de Azure], en la hoja de configuración de la cuenta Microsoft de la aplicación móvil, pegue los valores de Id. de cliente y Secreto de cliente que obtuvo anteriormente. A continuación, haga clic en **Guardar**.

    ![][1]

De este modo ya estará listo para usar la cuenta Microsoft para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal de administración de Azure]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->