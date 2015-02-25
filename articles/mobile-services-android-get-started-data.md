<properties pageTitle="Introducción a los datos (Android) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación Android." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal,glenga"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Android. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">ver el tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo</span></a> <span class="time">15:32</span></div>
</div>

> [AZURE.NOTE] Este tutorial está destinado a ayudarle a comprender cómo los Servicios móviles le permiten usar Azure para almacenar y recuperar datos de una aplicación Android. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-android).
> 
> Si desea ver el código fuente de la aplicación finalizada, haga clic [aquí](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).


Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación Android] 
2. [Crear el servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualización de la aplicación para usar Servicios móviles]
5. [Probar la aplicación en Servicios móviles]

> [AZURE.IMPORTANT] para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, vea [Evaluación gratuita de Azure](.http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AED8DE357"%20target="_blank) 

Este tutorial requiere el [SDK de Android` para Servicios móviles]; el <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">SDK de Android</a>, que incluye el entorno de desarrollo integrado de Eclipse (IDE) y el complemento de Herramientas para desarrolladores de Android (ADT); y Android 4.2 o una versión más reciente. 

> [AZURE.NOTE] Este tutorial proporciona instrucciones para la instalación del SDK de Android y del SDK de Android para Servicios móviles. El proyecto GetStartedWithData descargado requiere Android 4.2 o una versión más reciente. No obstante, el SDK para Servicios móviles solo requiere Android 2.2 o una versión más reciente.

>[AZURE.NOTE] Este tutorial utiliza la última versión del SDK de Servicios móviles. Puede encontrar una versión anterior <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> aquí</a> para fines de compatibilidad con versiones anteriores, pero el código contenido en estos tutoriales no es aplicable en este caso.

<h2><a name="download-app"></a>Descarga del proyecto GetStartedWithData</h2>

###Obtención del código de ejemplo

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [Comprobar el SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###Inspección y ejecución del código de ejemplo

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="create-service"></a>Creación de un servicio móvil en el Portal de administración</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Incorporación de una tabla nueva al servicio móvil</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]


<h2><a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo</h2>

Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Run**, haga clic en **Run** para iniciar el proyecto.

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

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos gestionados en una única solicitud.

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
[Validar y modificar datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-android
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started-android
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Android para Servicios móviles]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125


<!--HONumber=42-->
