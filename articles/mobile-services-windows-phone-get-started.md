<properties pageTitle="Get Started with Azure Mobile Services for Windows Phone apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for Windows Phone development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="hero-article" ms.date="01/01/1900" ms.author="glenga"></tags>

# <a name="getting-started"> </a>Introducción a los Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tutorial se muestra c&oacute;mo agregar un servicio back-end basado en la nube a una aplicaci&oacute;n de Windows Phone 8 con los Servicios m&oacute;viles de Azure. Con este tutorial crear&aacute; tanto un servicio m&oacute;vil nuevo como una aplicaci&oacute;n simple de <em>Lista de pendientes</em> que almacena datos de la aplicaci&oacute;n en el servicio m&oacute;vil nuevo.</p>
<p>Si prefiere ver un v&iacute;deo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial. En el v&iacute;deo, Nick Harris ofrece una introducci&oacute;n a los Servicios m&oacute;viles y describe la creaci&oacute;n de su primer servicio m&oacute;vil y c&oacute;mo conectar con &eacute;l desde una aplicaci&oacute;n de la Tienda Windows.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="label">Ver el tutorial (en ingl&eacute;s)</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=290816" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir v&iacute;deo (en ingl&eacute;s)</span></a> <span class="time">13:18:00</span></div>

</div>

Con este tutorial creará tanto un servicio móvil nuevo como una aplicación simple de *Lista de pendientes* que almacena datos de la aplicación en el servicio móvil nuevo. El servicio móvil que se creará utiliza JavaScript para la lógica de negocios de servidor. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en los lenguajes compatibles de .NET con Visual Studio, consulte la [versión de back-end de .NET][] de este tema.

La siguiente captura de pantalla muestra la aplicación final:

![][]

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. Si no dispone de ninguna cuenta, puede registrarse para obtener una versión de evaluación de Azure y conseguir hasta 10 servicios móviles gratuitos que podrá seguir usando incluso después de que finalice la evaluación. Para obtener más información, consulte [Evaluación gratuita de Azure][]. Para crear una nueva aplicación de Windows Phone 8.1, debe disponer de la Actualización 2 de Visual Studio 2013 o una versión posterior.

## <a name="create-new-service"> </a>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service][]]

## 

## <span class="short-header">Creación de una nueva aplicación</span>Creación de una nueva aplicación de Windows Phone

</h2>
Una vez que haya creado el servicio móvil, podrá seguir una introducción rápida en el Portal de administración para crear una nueva aplicación o modificar una ya existente a fin de conectarla a dicho servicio.

En esta sección, creará una aplicación de Windows Phone 8 que se conecta al servicio móvil.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  En la pestaña de inicio rápido, haga clic en **Windows Phone 8** en **Choose platform** y, a continuación, expanda **Create a new Windows Phone 8 app**.

    ![][1]

    Con esto se muestran los tres sencillos pasos requeridos para crear una aplicación de Windows Phone conectada al servicio móvil.

    ![][2]

3.  Si todavía no lo ha hecho, descargue e instale [Visual Studio 2012 Express para Windows Phone][] en el equipo local.

    > [WACOM.NOTE] Para crear una nueva aplicación de Windows Phone 8.1, debe tener instalada la Actualización 2 de Visual Studio 2013.

4.  Haga clic en **Create TodoItem table** para crear una tabla donde almacenar datos de la aplicación.

5.  En **Download and run app**, haga clic en **Download**.

    De este modo se descarga el proyecto para la aplicación de *lista de pendientes* de muestra que está conectada al servicio móvil. Guarde el archivo comprimido del proyecto en el equipo local y anote dónde lo guardó.

## Ejecución de la aplicación de Windows Phone

La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de solución en Visual Studio.

2.  (Opcional) Si está creando una aplicación de Windows Phone 8.1, haga clic con el botón secundario en el Explorador de soluciones, haga clic en **Propiedades**, establezca **Versión de destino del SO Windows Phone** en **Windows Phone 8.1** y haga clic en **Sí** para actualizar el proyecto.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba un texto significativo, como *Realice el tutorial* y, a continuación, haga clic en **Save**.

    ![][3]

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    > [WACOM.NOTE]Puede revisar el código que tiene acceso a su servicio móvil para consultar e insertar datos, el que se encuentra en el archivo MainPage.xaml.cs.

5.  Nuevamente en el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItems**.

    ![][4]

    Esto le permite examinar los datos que la aplicación inserta en la tabla.

    ![][5]

## <a name="next-steps"> </a>Pasos siguientes

Ahora que completó el inicio rápido, aprenda a realizar importantes tareas adicionales en los Servicios móviles:

-   [Introducción a los datos][]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la sincronización de datos sin conexión][]
    
	Aprenda a usar la sincronización de datos sin conexión para mejorar la capacidad de respuesta y reforzar la solidez de su aplicación.

-   [Introducción a la autenticación][]
    
	Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Introducción a las notificaciones de inserción][]
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [mobile-services-selector-get-started]: ../includes/mobile-services-selector-get-started.md
  [Ver el tutorial (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=290816
  [versión de back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  []: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-completed-wp8.png
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A30A4DDE2&returnurl=http%3A%2F%2FFes-es%2Fdocumentation%2Farticles%2Fmobile-services-windows-phone-get-started%2F
  [mobile-services-create-new-service]: ../includes/mobile-services-create-new-service.md
  [1]: ./media/mobile-services-windows-phone-get-started/mobile-portal-quickstart-wp8.png
  [2]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-steps-wp8.png
  [Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [3]: ./media/mobile-services-windows-phone-get-started/mobile-quickstart-startup-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started/mobile-data-tab.png
  [5]: ./media/mobile-services-windows-phone-get-started/mobile-data-browse.png
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data/
  [Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-offline-data
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
