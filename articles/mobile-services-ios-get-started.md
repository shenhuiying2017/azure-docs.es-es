<properties 
	pageTitle="Introducción a Servicios móviles de Azure para aplicaciones de iOS" 
	description="Siga este tutorial para empezar a usar Servicios móviles de Azure para el desarrollo de iOS." 
	services="" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se muestra cómo agregar un servicio back-end basado en la nube a una aplicación de iOS con los Servicios móviles de Azure.</p>
<p>Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a> <span class="time">9:38</span></div>
</div>

En este tutorial, creará tanto un servicio móvil nuevo como una aplicación simple _To do list_ que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la [versión de back-end de .NET] de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Para completar este tutorial necesita XCode 4.5 y iOS 5.0 o versiones posteriores.

> [AZURE.NOTE] para completar este tutorial, deberá tener una cuenta de Azure. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure](.http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20 target="_blank")

## <a name="create-new-service"> </a>Creación de un servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Creación de una nueva aplicación iOS</h2>

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de iOS que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. En la pestaña de inicio rápido, haga clic en **iOS** en **Seleccionar plataforma** y, a continuación, expanda **Crear una nueva aplicación iOS**.

   	![][6]

   	Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de iOS conectada al servicio móvil.

  	![][7]

3. Si todavía no lo ha hecho, descargue e instale [Xcode v4.4] o una versión posterior.

4. Haga clic en **Crear tabla TodoItems** para crear una tabla donde almacenar los datos de la aplicación.

5. En **Descargar y ejecutar la aplicación**, haga clic en **Descargar**.

  	De este modo se descarga el proyecto para la aplicación _To do list_ de ejemplo que está conectado con su servicio móvil, junto con el SDK de Servicios móviles para iOS. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la nueva aplicación de iOS

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>De vuelta en el Portal de administración, haga clic en la pestaña <strong>Datos</strong> y, a continuación, en la tabla <strong>TodoItems</strong>.<p></li></ol>

![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

Esto le permite examinar los datos que la aplicación inserta en la tabla.</p>

![](./media/mobile-services-ios-get-started/mobile-data-browse.png)


## <a name="next-steps"> </a>Pasos siguientes
Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la sincronización de datos sin conexión]
  <br/>Aprenda a usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.


<!-- Anchors. -->
[Introducción a los Servicios móviles]:#getting-started
[Creación de un servicio móvil]:#create-new-service
[Definición de la instancia del servicio móvil]:#define-mobile-service-instance
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-ios-get-started-users/
[Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-ios-get-started-offline-data
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Portal de administración]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Versión para el back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started



<!--HONumber=42-->
