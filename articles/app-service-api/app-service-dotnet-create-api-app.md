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
	ms.topic="hero-article" 
	ms.date="08/14/2015"
	ms.author="tdykstra"/>

# Creación de una aplicación de API ASP.NET en el Servicio de aplicaciones de Azure

> [AZURE.SELECTOR]
- [Visual Studio 2015 or 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Información general

En este tutorial se muestra cómo crear un proyecto de API web ASP.NET que esté configurado para su implementación en la nube como [aplicación de API en el Servicio de aplicaciones de Azure](app-service-api-apps-why-best-platform.md). Para obtener información sobre cómo configurar un proyecto de API web existente para implementarlo como una aplicación de API, consulte [Configuración de un proyecto Web API como una aplicación de API](app-service-dotnet-create-api-app-visual-studio.md).

En los tutoriales posteriores de la serie se muestra cómo [implementar](app-service-dotnet-deploy-api-app.md) y [depurar](../app-service-dotnet-remotely-debug-api-app.md) el proyecto de aplicación de API creado en este tutorial.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Este tutorial requiere la versión 2.6 o posterior de SDK de Azure para .NET.

## Creación de un proyecto de aplicación de API

Cuando las instrucciones indican que escriba un nombre para el proyecto, escriba *ContactsList*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Pasos siguientes

La aplicación de API ya está lista para implementarse. Para ello, puede seguir el tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).
 

<!---HONumber=August15_HO8-->