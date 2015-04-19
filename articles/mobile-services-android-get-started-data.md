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
	ms.date="02/03/2015" 
	ms.author="ricksal,glenga"/>

# Agregar Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

## Resumen

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a><span class="time">15:32</span></div>
</div>

<p>Este tutorial está destinado a ayudarle a comprender cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Android. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es su primera experiencia con Servicios móviles, considere la posibilidad de completar primero el tutorial <a href="/develop/mobile/tutorials/get-started-android">Introducción a los Servicios móviles</a>.</p>

> [AZURE.NOTE] Si desea ver el código fuente de la aplicación final, vaya <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData">aquí</a>.

Si desea ver la versión Eclipse de este tutorial, vaya a 
[Empezar a trabajar con datos utilizando Eclipse](mobile-services-android-get-started-data-EC.md).

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Prueba gratuita de Azure</a>.

Este tutorial requiere el [SDK de Android para Servicios móviles]; el <a  href="https://developer.android.com/sdk/index.html" target="_blank">Entorno de desarrollo integrado de Studio Android</a>, que incluye el SDK de Android y Android 4.2 o una versión posterior. 

> [AZURE.NOTE] Este tutorial proporciona instrucciones para la instalación del SDK de Android y del SDK de Android para Servicios móviles. El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.
<!-- -->

> [AZURE.NOTE] Este tutorial utiliza la última versión del SDK de Servicios móviles. Puede encontrar una versión anterior del mismo <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> aquí</a> para compatibilidad con versiones anteriores, pero el código contenido en estos tutoriales no funciona con él.

<h2><a name="download-app"></a>Descarga del proyecto GetStartedWithData</h2>

###Obtención del código de ejemplo

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###Inspección y ejecución del código de ejemplo

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>Creación de un servicio móvil nuevo en el Portal de administración</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Agregar una tabla nueva al servicio móvil</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo</h2>

Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar el proyecto.

	De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

5. Como antes, escriba texto descriptivo y, a continuación, haga clic en **Add**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Android.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles. 

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts]
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Cuando haya completado la serie de datos, pruebe estos otros tutoriales de Android:

* [Introducción a la autenticación] 
	<br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción] 
  <br/>Aprenda la manera de enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

<!-- Anchors. -->
[Descarga del proyecto de la aplicación Android]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualización de la aplicación para usar Servicios móviles]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Validación y modificación de datos con scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /develop/mobile/tutorials/add-paging-to-data-android
[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started-android
[Introducción a los datos]: /develop/mobile/tutorials/get-started-with-data-android
[Empezar a trabajar con datos (Eclipse)]: /documentation/articles/mobile-services-android-get-started-data-EC/
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /develop/mobile/tutorials/get-started-with-push-android

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Android para Servicios móviles]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[SDK de Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=47-->
