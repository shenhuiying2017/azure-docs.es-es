<properties pageTitle="Get Started with Mobile Services for Windows Store apps | Mobile Dev Center" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Store development in C# or JavaScript. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga" />

# <a name="getting-started"> </a>Introducción a los Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-get-started" title="Tienda Windows" class="current">Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started" title="Android">Android</a>
    <a href="/es-es/documentation/articles/mobile-services-html-get-started" title="HTML">HTML</a>
    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started" title="Xamarin.Android">Xamarin.Android</a>
    <a href="/es-es/documentation/articles/partner-sencha-mobile-services-get-started/" title="Sencha">Sencha</a>
    <a href="/es-es/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/" title="PhoneGap">PhoneGap</a>
    <a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/" title="Back-end de .NET">Back-end de .NET</a> | 
    <a href="/es-es/documentation/articles/mobile-services-windows-store-get-started/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se muestra c&oacute;mo agregar un servicio de back-end basado en la nube para una aplicaci&oacute;n de la Tienda Windows con Servicios m&oacute;viles de Azure.</p>
<p>Si prefiere ver un v&iacute;deo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial. En el v&iacute;deo, Scott Guthrie ofrece una introducci&oacute;n a los Servicios m&oacute;viles y describe la creaci&oacute;n de su primer servicio m&oacute;vil y c&oacute;mo conectar con &eacute;l desde una aplicaci&oacute;n de la Tienda Windows.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">10:08:00</span></div>

</div>

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la versión de back-end de .NET de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![][0]

Completar este tutorial es un requisito previo para todos los tutoriales de Servicios móviles para aplicaciones de la Tienda Windows.

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][Evaluación gratuita de Azure].

> Este tutorial requiere Visual Studio 2013. Para conectar una aplicación de la Tienda Windows mediante Visual Studio 2012, siga los pasos en el tema [Introducción a los datos en Servicios móviles con Visual Studio 2012][Introducción a los datos en Servicios móviles con Visual Studio 2012].

## Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Creación de una aplicación de la Tienda Windows

Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de Tienda Windows que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows** en **Choose platform** y, a continuación, expanda **Crear una nueva aplicación de la Tienda Windows**.

    ![][1]

    Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de la Tienda Windows conectada al servicio móvil.

    ![][2]

3.  Si todavía no lo ha hecho, descargue e instale [Visual Studio 2013 Express para Windows][Visual Studio 2013 Express para Windows] en el equipo local o máquina virtual.

4.  Haga clic en **Create TodoItem table** para crear una tabla donde almacenar datos de la aplicación.

5.  En **Download and run your app**, seleccione un idioma para la aplicación y, a continuación, haga clic en **Download**.

    De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación de Windows

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio 2013 Express para Windows.

2.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*, en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][3]

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y se muestran los datos en la segunda columna de la aplicación.

    > [WACOM.NOTE]Puede revisar el código que obtiene acceso al servicio móvil para consultar e insertar datos. Puede encontrarse en el archivo MainPage.xaml.cs (proyecto C#/XAML) o en el archivo default.js (proyecto JavaScript/HTML).

4.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![][4]

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![][5]

## Pasos siguientes

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   **Introducción a los datos** ( [C#][C#] / [JavaScript][JavaScript] )
    
	Obtenga más información sobre cómo almacenar y consultar datos mediante Servicios móviles.

-   **Introducción a la autenticación** ( [C#][6] / [JavaScript][7] )
    
	Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   **Introducción a las notificaciones de inserción** ( [C#][8] / [JavaScript][9] )
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [0]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-completed.png
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Introducción a los datos en Servicios móviles con Visual Studio 2012]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-store-get-started/mobile-portal-quickstart.png
  [2]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-steps.png
  [Visual Studio 2013 Express para Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [3]: ./media/mobile-services-windows-store-get-started/mobile-quickstart-startup.png
  [4]: ./media/mobile-services-windows-store-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-store-get-started/mobile-data-browse.png
  [C#]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet
  [JavaScript]: /es-es/develop/mobile/tutorials/get-started-with-data-js
  [6]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
  [7]: /es-es/develop/mobile/tutorials/get-started-with-users-js
  [8]: /es-es/develop/mobile/tutorials/get-started-with-push-dotnet
  [9]: /es-es/develop/mobile/tutorials/get-started-with-push-js
