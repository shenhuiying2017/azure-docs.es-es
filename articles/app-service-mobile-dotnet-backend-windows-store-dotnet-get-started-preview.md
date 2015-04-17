<properties
	pageTitle="Introducción a los back-ends de aplicación móvil de la Tienda Windows | Centro de desarrollo móvil"
	description="Siga este tutorial para aprender a usar back-ends de aplicación móvil de Azure para el desarrollo de la Tienda Windows en C#, VB o JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Creación de una aplicación Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

En este tutorial se muestra cómo agregar un servicio de back-end basado en la nube para una aplicación universal de Windows con un back-end de aplicaciones móviles de Azure. Las soluciones de aplicaciones universales de Windows incluyen aplicaciones y un proyecto compartido común tanto para la Tienda Windows 8.1 como para la Tienda de Windows Phone 8.1.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 aplicaciones móviles gratuitas que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Si desea empezar a trabajar con Servicios de aplicaciones de Azure antes de registrarse para abrir una cuenta de Azure, vaya a [Prueba de Servicios de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en Servicios de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a name="create-new-service"> </a>Creación de un nuevo back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Creación de una nueva aplicación universal de Windows

Una vez que haya creado el back-end de aplicación móvil, podrá seguir una introducción rápida en el Portal de Azure para crear una nueva aplicación o modificar una ya existente para conectarla al back-end de aplicación móvil.

En esta sección, creará una nueva aplicación universal de Windows que se conecta al back-end de aplicaciones móviles.

1. En el Portal de Azure, haga clic en **Aplicación móvil**y, a continuación, haga clic en la aplicación móvil que acaba de crear.

2. En la parte superior de la hoja, haga clic en **Agregar cliente** y expanda **Windows (C#)**.

   ![Mobile App quickstart steps](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al back-end de aplicaciones móviles.

3. Si todavía no lo ha hecho, descargue e instale <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> en el equipo local o máquina virtual.

4. En **Descargar y ejecutar la aplicación y el servicio localmente**, seleccione un idioma para la aplicación de la Tienda Windows y, a continuación, haga clic en **Descargar**.

   De esta forma, se descarga una solución que contiene proyectos para el back-end de aplicación móvil y para la aplicación de ejemplo _To list_ que está conectada al back-end de aplicación móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Prueba de la aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publicación del back-end de aplicación móvil

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Ejecución de la aplicación de Windows

Ahora que el back-end de aplicaciones móviles se ha publicado y el cliente está conectado al back-end de aplicaciones móviles remoto hospedado en Azure, podemos ejecutar la aplicación mediante Azure para el almacenamiento de elementos.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Introducción a los back-end de aplicación móvil]:#getting-started
[Creación de un nuevo back-end de aplicación móvil]:#create-new-service
[Definición de la instancia de back-end de aplicaciones móviles]:#define-mobile-app-backend-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Introducción a la autenticación]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK de aplicación móvil]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal de Azure]: https://portal.azure.com/

<!--HONumber=49-->