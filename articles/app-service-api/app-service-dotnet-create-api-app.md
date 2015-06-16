<properties 
	pageTitle="Creación de una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure" 
	description="Aprenda a crear una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure con Visual Studio 2013." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Creación de una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure

## Información general

En este tutorial se muestra cómo crear un proyecto de API web ASP.NET desde cero y cómo configurarlo para implementarlo en la nube como [aplicación de API](app-service-api-apps-why-best-platform.md) en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md). Si ya tiene un proyecto de API web y desea convertirlo en aplicación de API, consulte el artículo [Configuración de un proyecto de API web como aplicación de API](./app-service-dotnet-create-api-app-visual-studio). En los tutoriales posteriores de la serie se muestra cómo [implementar](app-service-dotnet-deploy-api-app.md) y [depurar](../app-service-dotnet-remotely-debug-api-app.md) el proyecto de aplicación de API creado en este tutorial.

Para obtener información sobre las aplicaciones de API, consulte [¿Qué son las Aplicaciones de API?](app-service-api-apps-why-best-platform.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Este tutorial requiere la versión 2.5.1 o posterior del SDK de Azure para .NET.

## Creación de un proyecto de aplicación de API 

En esta sección se muestra cómo usar la plantilla de proyecto de aplicación de API de Azure para crear una aplicación de API desde cero. Para aprender a configurar un proyecto de API web existente como aplicación de API, vaya a la [sección siguiente](#configure-a-web-api-project-as-an-api-app).

1. Abra Visual Studio 2013.

2. Seleccione **Archivo > Nuevo proyecto**.

3. Seleccione la plantilla **Aplicación Web ASP.NET**.

4. Asigne al proyecto el nombre *ContactsList*.

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. Haga clic en **Aceptar**.

6. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla de proyecto **Aplicación de API de Azure**.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. Haga clic en **Aceptar** para generar el proyecto.

Visual Studio crea un proyecto de API web configurado para implementarlo como aplicación de API.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Pasos siguientes

La aplicación de API ya está lista para implementarse. Para ello, puede seguir el tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).

<!--HONumber=54--> 