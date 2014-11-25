<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Introducción a los datos en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="C# para Tienda Windows">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Back-end de .NET" class="current">Back-end de .NET</a> | 
    <a href="/es-es/develop/mobile/tutorials/get-started-with-data-android/"  title="Back-end de JavaScript">Back-end de JavaScript</a>
</div>

Este tema muestra cómo utilizar Servicios móviles de Azure como origen de datos de back-end para una aplicación de Android. En este tutorial, creará un nuevo servicio móvil, descargará un proyecto Android en Eclipse para una aplicación que almacena datos en memoria, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que cree en este tutorial admitirá el tiempo de ejecución .NET. Esto le permitirá utilizar lenguajes .NET y Visual Studio para la lógica de negocios de servidor en el servicio móvil. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript][versión back-end de JavaScript] de este tema.

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial requiere Visual Studio 2013.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Creación de un servicio móvil][Creación de un servicio móvil]
2.  [Descarga del servicio móvil de manera local][Descarga del servicio móvil de manera local]
3.  [Prueba del servicio móvil][Prueba del servicio móvil]
4.  [Publicación del servicio móvil en Azure][Publicación del servicio móvil en Azure]
5.  [Descarga del proyecto GetStartedWithData][Descarga del proyecto GetStartedWithData]
6.  [Actualización de la aplicación para usar el servicio móvil para el acceso a datos][Actualización de la aplicación para usar el servicio móvil para el acceso a datos]
7.  [Prueba de la aplicación con el servicio móvil publicado][Prueba de la aplicación con el servicio móvil publicado]

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <a name="create-service"></a><span class="short-header">Creación de un servicio móvil</span>Creación de un servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service"></a><span class="short-header">Descarga del servicio</span>Descarga del servicio en el equipo local

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">Prueba del servicio</span>Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <a name="publish-the-service"></a><span class="short-header">Publicación del servicio</span>Publicación del servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="download-app"></a><span class="short-header">Descarga del proyecto</span>Descarga del proyecto GetStartedWithData

### Obtención del código de ejemplo

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)

### Comprobación de la versión del SDK de Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

### Inspección y ejecución del código de ejemplo

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación</span>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con el servicio móvil publicado

Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1.  En el menú **Run**, haga clic en **Run** para iniciar el proyecto.

    De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

2.  Como antes, escriba un texto descriptivo y, a continuación, haga clic en **Add**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

    Puede reiniciar la aplicación para ver si los cambios se aplicaron en la base de datos de Azure. También puede examinar la base de datos mediante el Portal de administración de Azure: en los dos pasos siguientes se hará esto para ver los cambios en la base de datos.

3.  En el Portal de administración de Azure, haga clic en la opción Manage correspondiente a la base de datos asociada con el servicio móvil.

    ![][0]

4.  En el Portal de administración, ejecute una consulta para ver los cambios que realiza la aplicación de la Tienda Windows. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Así concluye el tutorial **Introducción a los datos** para Android.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

Pruebe uno de estos otros tutoriales:

-   [Introducción a la autenticación][Introducción a la autenticación]
    <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET][Referencia conceptual de Servicios móviles con .NET]
    <br/>Obtenga más información sobre cómo utilizar Servicios móviles con .NET.





  [versión back-end de JavaScript]: /es-es/develop/mobile/tutorials/get-started-with-data-android
  [Creación de un servicio móvil]: #create-service
  [Descarga del servicio móvil de manera local]: #download-the-service-locally
  [Prueba del servicio móvil]: #test-the-service
  [Publicación del servicio móvil en Azure]: #publish-mobile-service
  [Descarga del proyecto GetStartedWithData]: #download-app
  [Actualización de la aplicación para usar el servicio móvil para el acceso a datos]: #update-app
  [Prueba de la aplicación con el servicio móvil publicado]: #test-app
  [0]: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-android
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
