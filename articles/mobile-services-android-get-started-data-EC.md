<properties 
	pageTitle="Introducción a los datos (Android) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="ricksal"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">(en inglés) Reproducir vídeo (en inglés)</span></a><span class="time">15:32</span></div>
</div>

> [AZURE.NOTE]Este tutorial está destinado a ayudarle a comprender cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Android. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](get-started-android-EC.md).
> 
> Si desea ver el código fuente de la aplicación final, vaya [aquí](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).

Para completar este tutorial, necesitará lo siguiente:

+ Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-android-get-started-data-EC%2F). 

+ El [SDK de Android para Servicios móviles]; el <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">SDK de Android</a>, que incluye el entorno de desarrollo integrado (IDE) de Eclipse y el complemento de las herramientas del desarrollador de Android (ADT); y Android 4.2 o una versión más reciente.

> [AZURE.NOTE]Este tutorial proporciona instrucciones para la instalación del SDK de Android y del SDK de Android para Servicios móviles. El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

<!-- -->

> [AZURE.NOTE]Este tutorial utiliza la última versión del SDK de Servicios móviles. Puede encontrar una versión anterior <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> aquí</a> para fines de compatibilidad con versiones anteriores, pero el código contenido en estos tutoriales no es aplicable en este caso.

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

###Obtención del código de ejemplo

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code-EC.md)]

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [Comprobar el SDK](../includes/mobile-services-verify-android-sdk-version-EC.md)]


###Inspección y ejecución del código de ejemplo

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code-EC.md)]

##<a name="create-service"></a>Creación de un servicio móvil nuevo en el Portal de administración

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Agregar una tabla nueva al servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data-EC.md)]

##<a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar el proyecto.

	De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

5. Como antes, escriba texto descriptivo y, a continuación, haga clic en **Agregar**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos para Android**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles.

A continuación, pruebe estos otros tutoriales de Android:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción] <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Introducción a la autenticación]: mobile-services-android-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-javascript-backend-android-get-started-push-EC.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Android para Servicios móviles]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=54-->