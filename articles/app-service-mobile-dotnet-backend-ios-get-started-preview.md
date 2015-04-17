<properties
	pageTitle="Introducción a las aplicaciones iOS móviles del Servicio de aplicaciones de Azure"
	description="Siga este tutorial para empezar a usar el Servicio de aplicaciones de Azure para el desarrollo de iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="yuaxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Creación de una aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

Este tutorial muestra cómo crear una aplicación iOS con las aplicaciones móviles del Servicio de aplicaciones de Azure. Creará un nuevo back-end de aplicaciones móviles y una aplicación _To do list_ simple que almacena los datos de la aplicación. El tutorial utiliza .NET y Visual Studio para la lógica del lado servidor.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Si desea empezar a trabajar con Servicios de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Prueba de Servicios de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en Servicios de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a name="create-new-service"> </a>Creación de un nuevo back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creación de una nueva aplicación iOS

Una vez que haya creado el back-end móvil, podrá seguir una fácil guía de inicio rápido en el Portal de vista previa de Azure para crear una nueva aplicación o modificar una existente a fin de conectarla a dicho back-end de aplicaciones móviles.

1. En el Portal de Azure, haga clic en **Aplicación móvil** y, a continuación, haga clic en el back-end de aplicaciones móviles que acaba de crear.

2. En la parte superior de la hoja, haga clic en Agregar cliente y expanda iOS.

	![][6]

	Esto muestra los pasos para crear una aplicación iOS conectada a su back-end de aplicaciones móviles.

3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o máquina virtual. 

4. Descargue e instale [Xcode] v4.4 o una versión posterior y [Xamarin Studio]. También puede usar Xamarin para Visual Studio.

5. En **Descargar y publicar el servicio en la nube**, haga clic en **Descargar**.

 De esta forma, se descarga una solución que contiene proyectos tanto para el back-end de aplicaciones móviles como para la aplicación _To do list_ de ejemplo conectada a su back-end de aplicaciones móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

6. Descargue su perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba de la aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicación de su aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ejecución de la aplicación iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Portal de administración]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

<!--HONumber=49-->