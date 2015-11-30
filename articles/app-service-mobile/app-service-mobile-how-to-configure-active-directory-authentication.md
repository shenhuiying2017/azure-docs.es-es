<properties 
	pageTitle="Configuración de la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones" 
	description="Obtenga información acerca de cómo configurar la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones" 
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
	ms.date="10/29/2015" 
	ms.author="mahender"/>

# Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Azure Active Directory como proveedor de autenticación.


	> [AZURE.NOTE] This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. If using the gateway, please see the [alternative method]. Differences that apply to using the gateway are called out in notes throughout that section.


## <a name="express"> </a>Configuración de Azure Active Directory mediante la configuración rápida

13. En el [Portal de administración de Azure], vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación o autorización**.

14. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

15. Haga clic en **Azure Active Directory** y luego haga clic en **Rápida** en **Modo de administración**.

16. Haga clic en **Aceptar** para registrar la aplicación en Azure Active Directory. Se creará un nuevo registro. Si, por el contrario, desea elegir un registro existente, haga clic en **Seleccionar una aplicación existente** y luego busque el nombre de un registro creado anteriormente en el inquilino. Haga clic en el registro para seleccionarlo y haga clic en **Aceptar**. A continuación, haga clic en **Aceptar** en la hoja de configuración de Active Directory.

    ![][0]
	
16. De forma predeterminada, el Servicio de aplicaciones proporciona inicio de sesión, pero no restringe el acceso al contenido y las API del sitio, esto es responsabilidad del código de la aplicación. Si desea que el sitio esté totalmente protegido por el inicio de sesión de Azure Active Directory, cambie la lista desplegable **Acción por realizar cuando no se autentique la solicitud** para usar la opción **Azure Active Directory**. Para ello, todas las solicitudes deberán estar autenticadas; las solicitudes sin autenticar se redirigirán para iniciar sesión con Azure Active Directory.

17. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="advanced"> </a>(Método alternativo) Configuración manual de Azure Active Directory con la configuración avanzada
También puede elegir proporcionar los valores de configuración manualmente. Esta es la solución preferida si el inquilino de AAD que desea usar es diferente de aquel con el que inicia sesión en Azure. Para completar la configuración, primero debe crear un registro en Azure Active Directory y luego proporcionar algunos de los detalles de registro al Servicio de aplicaciones.

### <a name="register"> </a>Registro de la aplicación con Azure Active Directory

1. Inicie sesión en el [Portal de administración de Azure] en vista previa y vaya a la aplicación móvil. Copie la **dirección URL**. Se usará para configurar la aplicación de Azure Active Directory.

3. Inicie sesión en el [Portal de administración de Azure] y vaya a **Active Directory**.

    ![][2]

4. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.

5. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

6. En el asistente para agregar aplicaciones, escriba un **nombre** para la aplicación y haga clic en el tipo **Aplicación web o API web**. A continuación, haga clic para continuar.

7. En el cuadro **URL DE INICIO DE SESIÓN**, pegue la dirección URL que copió anteriormente. Escriba esa misma dirección URL en el cuadro **URI de id. de aplicación**. A continuación, haga clic para continuar.

8. Una vez que se haya agregado la aplicación, haga clic en la pestaña **Configurar**. Edite la **URL de respuesta** en **Inicio de sesión único** para que sea la URL de la aplicación anexada a la ruta de acceso, _/.auth/login/aad/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Asegúrese de que está utilizando el esquema HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]Si usa la puerta de enlace en lugar de la característica Autenticación o autorización del Servicio de aplicaciones, la URL de respuesta usará en su lugar la URL de la puerta de enlace con la ruta de acceso _/signin-aad_.


9. Haga clic en **Guardar**. A continuación, copie **Id. de cliente** de la aplicación. Más adelante configurará la aplicación para usar este valor.

10. En la barra de comandos, haga clic en **Ver extremos** y luego copie la URL del **documento de metadatos de federación** y descargue ese documento o vaya a él en un explorador.

11. En el elemento raíz **EntityDescriptor**, debe haber un atributo **entityID** del formulario `https://sts.windows.net/` seguido de un GUID específico para el inquilino (denominado "identificador de inquilino"). Copie este valor, actuará como **URL del emisor**. Más adelante configurará la aplicación para usar este valor.

### <a name="secrets"> </a>Incorporación de información de Azure Active Directory a la aplicación


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Azure Active Directory**. Paste in the ClientID and add the tenant ID to the **Allowed Tenants** list. Click **Save**.


13. En el [Portal de administración de Azure] en vista previa, vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación o autorización**.

14. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

15. Haga clic en **Azure Active Directory** y luego haga clic en **Avanzada** en **Modo de administración**. Pegue el valor de id. de cliente y URL del emisor que obtuvo anteriormente. y, a continuación, haga clic en **Aceptar**.

    ![][1]
	
16. De forma predeterminada, el Servicio de aplicaciones proporciona inicio de sesión, pero no restringe el acceso al contenido y las API del sitio, esto es responsabilidad del código de la aplicación. Si desea que el sitio esté totalmente protegido por el inicio de sesión de Azure Active Directory, cambie la lista desplegable **Acción por realizar cuando no se autentique la solicitud** para usar la opción **Azure Active Directory**. Para ello, todas las solicitudes deberán estar autenticadas; las solicitudes sin autenticar se redirigirán para iniciar sesión con Azure Active Directory.

17. Haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal de administración de Azure]: https://portal.azure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[alternative method]: #advanced

<!---HONumber=Nov15_HO4-->