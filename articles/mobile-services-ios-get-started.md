<properties
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de iOS"
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="2/11/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Introducción a Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de iOS con los Servicios móviles de Azure.

En este tutorial, creará tanto un servicio móvil nuevo como una aplicación simple de _To do list_ que almacena datos de la aplicación en el nuevo servicio móvil. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Para crear un servicio móvil con lógica empresarial del lado de servidor, consulte la [Versión para el back-end de .NET] de este tema.

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y [acceder a servicios móviles gratuitos que puede seguir usando incluso después de que finalice dicha evaluación](http://azure.microsoft.com/pricing/details/mobile-services/). Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank").

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Creación de una nueva aplicación iOS</h2>

Puede seguir un sencillo inicio rápido en el Portal de administración para crear una nueva aplicación conectada a su servicio móvil:

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña Inicio rápido, haga clic en **iOS** en **Seleccionar plataforma** y expanda **Crear una nueva aplicación iOS**. Con esto se muestran los pasos requeridos para crear una aplicación iOS conectada a su servicio móvil.

3. Haga clic en **Crear tabla TodoItem** para crear una tabla para almacenar los datos de la aplicación.

4. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**. De este modo se descarga el proyecto para la aplicación _To do list_ de ejemplo que está conectado con su servicio móvil, junto con el SDK de Servicios móviles para iOS. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la nueva aplicación de iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>De nuevo en el Portal de administración, haga clic en la pestaña <strong>Datos</strong> y luego haga clic en la tabla <strong>TodoItems</strong>. Esto le permite examinar los datos que la aplicación inserta en la tabla.<p></li></ol></p>

## <a name="next-steps"> </a>Pasos siguientes
Aprenda a realizar tareas adicionales importantes en Servicios móviles:

* [Incorporación de servicios móviles a una aplicación existente]
	<br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la sincronización de datos sin conexión]
	<br/>Aprenda a usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

* [Incorporación de servicios móviles a una aplicación existente]
	<br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Incorporación de notificaciones de inserción a una aplicación existente]
	<br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.


<!-- Anchors. -->
[Introducción a los Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Incorporación de servicios móviles a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-data.md
[Introducción a la sincronización de datos sin conexión]: mobile-services-ios-get-started-offline-data.md
[Incorporación de servicios móviles a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-users.md
[Incorporación de notificaciones de inserción a una aplicación existente]: mobile-services-dotnet-backend-ios-get-started-push.md


[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Portal de administración]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versión para el back-end de .NET]: mobile-services-dotnet-backend-ios-get-started.md

<!--HONumber=52-->