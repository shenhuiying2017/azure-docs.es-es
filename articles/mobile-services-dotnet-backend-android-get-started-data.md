<properties 
	pageTitle="Introducción a los datos (Android) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación Android.." 
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
	ms.author="ricksal"/>

# Agregar Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo utilizar los Servicios móviles de Azure como origen de datos de back-end para una aplicación Android. En este tutorial, creará un nuevo servicio móvil, descargará un proyecto Android en Eclipse para una aplicación que almacena datos en memoria, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que cree en este tutorial admitirá el tiempo de ejecución .NET. Esto le permitirá utilizar lenguajes .NET y Visual Studio para la lógica de negocios de servidor en el servicio móvil. Si desea crear un servicio móvil que le permita escribir su lógica de negocios de servidor en JavaScript, consulte la [versión back-end de JavaScript] de este tema.

> [AZURE.IMPORTANT] Este tutorial requiere Visual Studio 2013.

<!-- -->

> [AZURE.NOTE] Si desea ver la versión de Eclipse de este tutorial, vaya a: [Empezar a trabajar con datos (Eclipse)].

Este tutorial le guiará a través de estos pasos básicos:


1. [Creación de un servicio móvil]
2. [Descarga del servicio de forma local]
3. [Prueba del servicio móvil]
4. [Publicación del servicio móvil en Azure]
5. [Descarga del proyecto GetStartedWithData]
4. [Actualización de la aplicación para usar el servicio móvil para el acceso a datos]
5. [Prueba de la aplicación con el servicio móvil publicado]


> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F"%20target="_blank). 


<h2><a name="create-service"></a>Creación de un servicio móvil</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]


<h2><a name="download-the-service"></a>Descarga del servicio de forma local</h2>

[AZURE.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

<h2><a name="test-the-service"></a>Prueba del servicio móvil</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

<h2><a name="publish-the-service"></a>Publicación del servicio móvil en Azure</h2>

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<h2><a name="download-app"></a>Descarga del proyecto GetStartedWithData</h2>

###Obtención del código de ejemplo

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/download-android-sample-code.md)]

###Comprobación de la versión del SDK de Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]


###Inspección y ejecución del código de ejemplo

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

<h2><a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

<h2><a name="test-app"></a>Prueba de la aplicación con el servicio móvil publicado</h2>


Ahora que la aplicación se ha actualizado para usar los Servicios móviles para almacenamiento back-end, puede probarla con los Servicios móviles usando el emulador de Android o un teléfono Android.

1. En el menú **Ejecutar**, haga clic en **Ejecutar aplicación** para iniciar el proyecto.

	De este modo se ejecuta su aplicación, que se ha creado con el SDK de Android, y se usa la biblioteca del cliente para enviar una consulta que devuelve los elementos desde su servicio móvil.

5. Como antes, escriba texto descriptivo y, a continuación, haga clic en **Add**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

    Puede reiniciar la aplicación para ver si los cambios se aplicaron en la base de datos de Azure. También puede examinar la base de datos mediante el Portal de administración de Azure: realice los dos pasos siguientes para ver los cambios en la base de datos.


4. En el Portal de administración de Azure, haga clic en la opción Administrador correspondiente a la base de datos asociada con el servicio móvil.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. En el Portal de administración, ejecute una consulta para ver los cambios que realiza la aplicación de la Tienda Windows. La consulta será similar a la siguiente, pero con el nombre de la base de datos en lugar de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

Así concluye el tutorial **Introducción a los datos** para Android.



## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación Android para que funcione con los datos de los Servicios móviles. 

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:

* [Validate and modify data with scripts]
  <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.

* [Refine queries with paging]
  <br/>Learn how to use paging in queries to control the amount of data handled in a single request.

Once you have completed the data series, try
-->

Pruebe uno de estos otros tutoriales:

* [Introducción a la autenticación]
  <br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de servicios móviles con .NET]
  <br/>Obtenga más información acerca del uso de Servicios móviles con .NET.
  
<!-- Anchors. -->

[Creación de un servicio móvil]: #create-service
[Descarga del servicio de forma local]: #download-the-service-locally
[Prueba del servicio móvil]: #test-the-service
[Descarga del proyecto GetStartedWithData]: #download-app
[Actualización de la aplicación para usar el servicio móvil para el acceso a datos]: #update-app
[Prueba de la aplicación Android con el servicio hospedado de manera local]: #test-locally-hosted
[Publicación del servicio móvil en Azure]: #publish-mobile-service
[Prueba de la aplicación Android con el servicio hospedado en Azure]: #test-azure-hosted
[Prueba de la aplicación con el servicio móvil publicado]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/new-local-todoitem.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-show-local-table-data.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/local-item-checked.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Empezar a trabajar con datos (Eclipse)]: mobile-services-dotnet-backend-android-get-started-data-EC.md
[Validación y modificación de datos con scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introducción a la autenticación]: /develop/mobile/tutorials/get-started-with-users-android
[Introducción a las notificaciones de inserción]: /develop/mobile/tutorials/get-started-with-push-android
[JavaScript y HTML]: /develop/mobile/tutorials/get-started-with-data-js
[Versión de back-end de JavaScript]: /develop/mobile/tutorials/get-started-with-data-android

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referencia conceptual de servicios móviles con .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[Referencia conceptual de servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md  

<!--HONumber=49-->