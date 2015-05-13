<properties 
	pageTitle="Creación de un paquete de aplicaciones de API" 
	description="Aprenda a publicar un paquete de aplicaciones de API en Azure Marketplace." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Publicación de un paquete de aplicaciones de API en Azure Marketplace

## Información general

En este artículo se muestra cómo publicar un paquete de aplicaciones de API en [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Para aprender a crear una aplicación de API, consulte [Creación de una aplicación de API con Visual Studio](app-service-dotnet-create-api-app.md).
- Para aprender a crear un paquete de aplicaciones de API, consulte [Creación de un paquete de aplicaciones de API](app-service-api-create-package).

## Flujo general de publicación

A continuación se muestra el flujo general de publicación.

1. Cree un paquete Nuget para la aplicación de API mediante las instrucciones del tutorial [Creación de un paquete de aplicaciones de API](app-service-api-create-package).
2. Publíquelo en una galería basada en Nuget en https://apiapps.nuget.org.
3. A continuación, se sincronizará con [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) automáticamente.
4. Vaya a [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) y al [Portal de vista previa de Azure](https://portal.azure.com) para comprobar la experiencia de un extremo a otro.

## Publicación en la galería basada en Nuget

1. Vaya a https://apiapps.nuget.org.

    ![Página principal de la galería basada en Nuget](./media/app-service-api-publish-package/nuget-homepage.png)

2. Haga clic en la opción para **Iniciar sesión** con la cuenta de Azure.
3. Haga clic en la pestaña **Aplicaciones de API**, haga clic en **Cargar aplicación de API** y, a continuación, cargue el paquete de aplicaciones de API.

    ![Página para cargar paquetes de la galería basada en Nuget](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. La lista desplegable de **Espacio de nombres** mostrará todos los dominios comprobados de los inquilinos de Azure Active Directory de su cuenta de Azure. Asegúrese de seleccionar uno que coincida con la propiedad de espacio de nombres del archivo apiapp.json del paquete de aplicaciones de API. Este se comprueba para asegurarse de que los publicadores reclaman espacios de nombres válidos para sus paquetes de aplicaciones de API.
5. Asegúrese de que la opción **Eliminar esta aplicación de API de la lista** está desactivada.
6. Haga clic en **Publicar**.
7. Si hay algún error de validación, corríjalo y cargue de nuevo.

## Visualización del paquete de aplicaciones de API en Azure Marketplace

Transcurridos unos minutos, el paquete de aplicaciones de API se sincronizará con Azure Marketplace. Vaya [aquí](http://azure.microsoft.com/marketplace/api-apps/) para comprobar el título, la descripción, el icono, etc.. Si desea realizar algún cambio, solo tiene que hacer la modificación correspondiente en el paquete de aplicaciones de API y publicarlo de nuevo.

![Página de aplicaciones de API de Azure Marketplace](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Implementación del paquete de aplicaciones de API en el Portal de vista previa de Azure

También puede iniciar sesión en el [Portal de vista previa de Azure](https://portal.azure.com) con la cuenta de Azure \(puede ser una cuenta distinta de la que usa para publicar el paquete de aplicaciones de API\). Aquí puede comprobar la experiencia de creación del Portal de vista previa de Azure para el paquete de aplicaciones de API. Si desea realizar algún cambio, solo tiene que hacer la modificación correspondiente en el paquete de aplicaciones de API y publicarlo de nuevo.

Para obtener información detallada sobre cómo implementar un paquete de aplicaciones de API en el portal de Azure, vea un ejemplo de implementación de DropboxConnector [aquí](app-service-api-connnect-your-app-to-saas-connector.md).

<!--HONumber=52-->
