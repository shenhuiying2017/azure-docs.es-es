<properties
	pageTitle="Introducción a Aplicaciones móviles en Xamarin iOS"
	description="Introducción al uso de Xamarin iOS para compilar una Aplicación móvil de Azure con el Servicios de aplicaciones de Azure."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"></a>Creación de una aplicación Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube a una aplicación Xamarin iOS con Aplicaciones móviles de Azure. En este tutorial, creará tanto un nuevo servicio .NET como una aplicación simple _To do list_ que almacena datos en el back-end de .NET.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Si desea empezar a trabajar con Servicios de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Prueba de Servicios de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en Servicios de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Creación de un nuevo back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creación de una nueva aplicación Xamarin iOS

Una vez que haya creado el back-end de aplicación móvil, podrá seguir una introducción rápida en el Portal de Azure para crear una nueva aplicación o modificar una ya existente para conectarla al back-end de aplicación móvil.

En esta sección va a descargar una nueva aplicación Xamarin iOS y un proyecto de servicio para su back-end de aplicación móvil.

1. En el Portal de Azure, haga clic en **Aplicación móvil** y, a continuación, haga clic en el back-end de aplicaciones móviles que acaba de crear.

2. En la parte superior de la hoja, haga clic en **Agregar cliente** y expanda **Xamarin iOS**.

	![][6]

	Con esto se muestran los tres sencillos pasos necesarios para crear una aplicación Xamarin iOS conectada al back-end de aplicación móvil.

3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o máquina virtual.

4. Descargue e instale [Xcode] v4.4 o una versión posterior y [Xamarin Studio]. También puede usar Xamarin para Visual Studio.

5. En **Descargar y publicar el servicio en la nube**, haga clic en **Descargar**.

 De esta forma, se descarga una solución que contiene proyectos tanto para el back-end de aplicaciones móviles como para la aplicación _To do list_ de ejemplo conectada a su back-end de aplicaciones móviles. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

6. Descargue su perfil de publicación, guarde el archivo descargado en el equipo local y tome nota de dónde lo guarda.

## Prueba del back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicación del back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ejecución de la aplicación Xamarin iOS

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1. Navegue al proyecto de cliente en la solución de back-end de aplicación móvil, en Visual Studio o en Xamarin Studio.

	![][8]

	![][9]

2. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación en el emulador de iPhone.

3. En la aplicación, escriba un texto significativo, como _Complete el tutorial_ y, a continuación, haga clic en el icono de signo de suma \(\*\*+\*\*\).

	![][10]

	Esta acción envía una solicitud POST al nuevo back-end de aplicación móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El back-end de aplicación móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

>[AZURE.NOTE]Puede revisar el código que obtiene acceso al back-end de aplicación móvil para consultar e insertar datos en el archivo de C\# QSTodoService.cs.


<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
[JavaScript backend version]: partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=52-->
