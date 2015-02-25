<properties pageTitle="Introducción a los datos (Tiendas Windows) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación de la Tienda Windows." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo usar Servicios móviles de Azure como origen de datos back-end para una aplicación de la Tienda Windows. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que se crea en este tutorial es un servicio móvil back-end de .NET. El back-end de .NET permite usar lenguajes .NET y Visual Studio para la lógica de negocios del lado servidor en el servicio móvil. Este servicio puede ejecutarse y depurarse en el equipo local. Para crear un servicio móvil que permita escribir la lógica de negocios del lado servidor en JavaScript, consulte la versión back-end JavaScript de este tema.

>[AZURE.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para conectar un nuevo servicio móvil a una aplicación universal para Windows. Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data) del tema.

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación de la Tienda Windows] 
2. [Creación de un servicio móvil nuevo desde Visual Studio]
3. [Prueba del proyecto de servicio móvil de forma local]
4. [Actualización de la aplicación para usar el servicio móvil]
5. [Publicación del servicio móvil en Azure]
6. [Prueba de aplicación con el servicio hospedado en Azure]
7. [Vista de los datos almacenados en Base de datos SQL]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, vea [Evaluación gratuita de Azure.](http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F)
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Hay disponible una versión de prueba gratuita.

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-javascript-download-project.md)]

##<a name="create-service"></a>Creación de un servicio móvil nuevo desde Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="8"><li><p>En el Explorador de soluciones, vaya a la subcarpeta <strong>services\mobileService\scripts</strong>, abra el archivo de script service.js y observe la nueva variable global cuyo aspecto es como en el siguiente ejemplo:</p> 

		<pre><code>var todolistClient = new MicrosoftAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.</p>
</li>
<li><p>Abra el archivo de proyecto default.html, busque la referencia al nuevo archivo de script service.js y asegúrese de que la ruta de acceso a la que se hace referencia es similar a la siguiente:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>Actualmente, se produce un error en Visual Studio que genera un nombre de carpeta incorrecto en la ruta de acceso.</p></li>
<li><p>Haga clic con el botón derecho en el proyecto de aplicación de Windows Phone, haga clic en <strong>Agregar</strong>, en <strong>Servicio conectado...</strong>, seleccione el servicio móvil que acaba de crear y luego haga clic en <strong>Aceptar</strong>. </p>
<p>El mismo archivo de código nuevo se agrega al proyecto de aplicación de la Tienda de Windows Phone. Asegúrese de corregir también en este caso la ruta de acceso de referencia que se agrega al archivo default.html.</p></li>
</ol>

En este punto, se conectan al nuevo servicio móvil las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone. El paso siguiente consiste en probar el nuevo proyecto de servicio móvil.

##<a name="test-the-service-locally"></a>Prueba del proyecto de servicio móvil de forma local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil

En esta sección, se actualiza la aplicación universal para Windows para usar el servicio móvil como servicio back-end para la aplicación. Sólo debe realizar cambios en el archivo default.js del proyecto en la carpeta de proyecto GetStartedWithData.Shared. 

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="publish-mobile-service"></a>Publicación del servicio móvil en Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="test-azure-hosted"></a>Prueba del servicio móvil hospedado en Azure

Ya puede probar las dos versiones de la aplicación universal para Windows con el servicio móvil hospedado en Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>Vista de los datos almacenados en Base de datos SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de un proyecto de aplicación universal para Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos gestionados en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

* [Introducción a la autenticación]
  <br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda cómo enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de servicios móviles con .NET]
  <br/>Obtenga más información acerca de cómo utilizar Servicios móviles con HTML y JavaScript.
  
<!-- Anchors. -->

[Descarga del proyecto de la aplicación de la Tienda Windows] : #download-app
[Creación de un servicio móvil nuevo desde Visual Studio]: #create-service
[Prueba del proyecto de servicio móvil de forma local]: #test-the-service-locally
[Actualización de la aplicación para usar el servicio móvil]: #update-app
[Prueba de la aplicación con el servicio hospedado de manera local]: #test-locally-hosted
[Publicación del servicio móvil en Azure]: #publish-mobile-service
[Prueba de aplicación con el servicio hospedado en Azure]: #test-azure-hosted
[Vista de los datos almacenados en Base de datos SQL]: #view-stored-data
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Validar y modificar datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-js
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library/
[Clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!--HONumber=42-->
