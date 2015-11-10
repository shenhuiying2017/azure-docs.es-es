<properties
	pageTitle="Crear una aplicación de iOS en aplicaciones móviles del Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Siga este tutorial para aprender a usar back-ends de la aplicación móvil de Azure para el desarrollo de iOS en Objective-C o Swift."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="krisragh"/>

#Creación de una aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Información general

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación móvil iOS con un back-end de la aplicación móvil de Azure. Creará tanto un back-end de aplicación móvil nuevo como una aplicación iOS simple de la _lista de tareas pendientes_ que almacene los datos de la aplicación en Azure.

Completar este tutorial es un requisito previo para todos los demás tutoriales de iOS sobre cómo usar la característica Aplicaciones móviles en el Servicio de aplicaciones de Azure.

## Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

* Una [cuenta de Azure activa](http://azure.microsoft.com/pricing/free-trial/)

* Un PC con [Visual Studio Community 2013] o una versión más reciente

* Un equipo Mac con Xcode v7.0 o una versión posterior

* El [marco de Azure Mobile iOS](https://go.microsoft.com/fwLink/?LinkID=529823), que se incluye automáticamente como parte del proyecto de inicio rápido que descarga.

## Creación de un nuevo back-end de Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Descarga del proyecto de servidor

1. En el PC, visite el [Portal de Azure]. Haga clic en **Examinar todo** > **Aplicaciones móviles** y, después, haga clic en el back-end de aplicaciones móviles que acaba de crear.

2. En la hoja de la aplicación móvil, haga clic en **Configuración** y, en **Aplicación móvil**, haga clic en **Inicio rápido** > **iOS (Objective-C)**. Si prefiere Swift, haga clic en **Inicio rápido** > **iOS (Swift)**.

3. En **Descargar y ejecutar el proyecto de servidor**, haga clic en **Descargar**. Extraiga los archivos comprimidos del proyecto en su equipo y abra la solución en Visual Studio.

## Publicación del proyecto de servidor en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Descargar y ejecutar la aplicación iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Portal de Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO2-->