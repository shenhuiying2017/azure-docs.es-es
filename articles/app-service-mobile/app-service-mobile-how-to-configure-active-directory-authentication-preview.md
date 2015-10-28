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
	ms.date="07/27/2015" 
	ms.author="mahender"/>

# Configuración de la aplicación para usar el inicio de sesión de Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

En este tema se muestra cómo configurar Servicios de aplicaciones de Azure para usar Azure Active Directory como proveedor de autenticación.

## <a name="register"> </a>Registro de la aplicación con Azure Active Directory

1. Inicie sesión en el [Portal de administración de vista previa de Azure] y vaya a la aplicación móvil.

2. En **Configuración**, haga clic en **Autenticación del usuario** y, después, haga clic en **Azure Active Directory**. Copie la **dirección URL de la aplicación** y la **dirección URL de respuesta** que aparecen en la lista. Las usará más adelante. Asegúrese de que la **Dirección URL de la aplicación** y la **URL de respuesta** usan el esquema HTTPS.

    ![][1]

3. Inicie sesión en el [Portal de administración de Azure] y vaya a **Active Directory**.

    ![][2]

4. Seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones** en la parte superior. Haga clic en **AGREGAR** en la parte inferior para crear un nuevo registro de aplicación.

5. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

6. En el asistente para agregar aplicaciones, escriba un **nombre** para la aplicación y haga clic en el tipo **Aplicación web o API web**. A continuación, haga clic para continuar.

7. En el cuadro **Dirección URL de inicio de sesión**, pegue el identificador de la aplicación que copió de la configuración del proveedor de identidades de Active Directory de la aplicación móvil. Especifique el mismo identificador de recurso en el cuadro **URI de id. de aplicación**. A continuación, haga clic para continuar.

8. Una vez que se haya agregado la aplicación, haga clic en la pestaña **Configurar**. Edite la **URL de respuesta** en **Inicio de sesión único** para que sea la dirección URL de respuesta de la aplicación móvil que copió anteriormente. Debe ser la puerta de enlace de la aplicación móvil anexada a _/signin-aad_. Por ejemplo: `https://contosogateway.azurewebsites.net/signin-aad`. Asegúrese de que está utilizando el esquema HTTPS.

    ![][3]

9. Haga clic en **Guardar**. A continuación, copie **Id. de cliente** de la aplicación.

## <a name="secrets"> </a>Adición de información de Azure Active Directory para su aplicación móvil

1. Vuelva al portal de administración de vista previa y a la hoja de configuración de **Azure Active Directory** de la aplicación móvil. Pegue en la configuración **Id. de cliente** para el proveedor de identidades de Azure Active Directory.
  
2. En la lista **Inquilinos permitidos** debe agregar el dominio del directorio en el que registró la aplicación (por ejemplo, contoso.onmicrosoft.com). Para buscar el nombre de dominio predeterminado, haga clic en la pestaña **Dominios** en el inquilino de Azure Active Directory. Agregue el nombre de dominio a la lista **Inquilinos permitidos** y haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Autenticación de usuarios de la aplicación móvil con un inicio de sesión único en Azure Active Directory: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/mobile-app-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[Portal de administración de vista previa de Azure]: https://portal.azure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=Oct15_HO3-->