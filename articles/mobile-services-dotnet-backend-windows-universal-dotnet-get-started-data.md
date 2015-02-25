<properties pageTitle="Introducción a los datos (Tiendas Windows) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación de la Tienda Windows." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc"/>

# Incorporación de servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo usar Servicios móviles de Azure como origen de datos back-end para una aplicación de la Tienda Windows. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y verá los cambios que se hicieron en los datos durante la ejecución de la aplicación.

El servicio móvil que se crea en este tutorial es un servicio móvil back-end de .NET. El back-end de .NET permite usar lenguajes .NET y Visual Studio para la lógica de negocios del lado servidor en el servicio móvil. Este servicio puede ejecutarse y depurarse en el equipo local. Para crear un servicio móvil que permita escribir la lógica de negocios del lado servidor en JavaScript, consulte la versión back-end JavaScript de este tema.

>[AZURE.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para conectar un nuevo servicio móvil a una aplicación universal para Windows. Se pueden seguir estos mismos pasos para conectar un servicio móvil a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para conectar un servicio móvil a una aplicación de Windows Phone Silverlight 8.1 o Windows Phone 8.0, consulte [Introducción a los datos para Windows Phone](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

> Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data) del tema.

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación de la Tienda Windows] 
2. [Creación de un servicio móvil nuevo desde Visual Studio]
3. [Prueba del proyecto de servicio móvil de forma local]
4. [Actualización de la aplicación para usar el servicio móvil]
5. [Publicación del servicio móvil en Azure]
6. [Prueba de aplicación con el servicio hospedado en Azure]
7. [Vista de los datos almacenados en Base de datos SQL]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, vea [Evaluación gratuita de Azure.](http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 o una versión posterior). <br/>Hay disponible una versión de prueba gratuita. 

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Creación de un servicio móvil nuevo desde Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>En el Explorador de soluciones, abra el archivo de código App.xaml.cs en la carpeta de proyecto GetStartedWithData.Shared y observe el nuevo campo estático que se agregó a la clase <strong>App</strong> en un bloque de compilación condicional de aplicación de la Tienda Windows, similar al ejemplo siguiente:</p> 

		<pre><code>public static Microsoft.MicrosoftAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.MicrosoftAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Este código proporciona acceso al nuevo servicio móvil de su aplicación mediante el uso de una instancia de <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a>. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Este campo estático está disponible para todas las páginas en la aplicación.</p>
</li>
<li><p>Haga clic con el botón derecho en el proyecto de aplicación de Windows Phone, haga clic en <strong>Agregar</strong>, en <strong>Servicio conectado...</strong>, seleccione el servicio móvil que acaba de crear y luego haga clic en <strong>Aceptar</strong>. </p>
<p>Se agrega el mismo código al archivo compartido App.xaml.cs, aunque esta vez en un bloque de compilación condicional de aplicación de Windows Phone.</p></li>
</ol>

En este punto, se conectan al nuevo servicio móvil las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone. El paso siguiente consiste en probar el nuevo proyecto de servicio móvil.


##<a name="test-the-service-locally"></a>Prueba del proyecto de servicio móvil de forma local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil

En esta sección, se actualiza la aplicación universal para Windows para usar el servicio móvil como servicio back-end para la aplicación. Solo debe realizar cambios en el archivo de proyecto MainPage.xaml.cs de la carpeta de proyecto GetStartedWithData.Shared. 

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


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
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.
  
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



<!-- URLs. -->
[Validar y modificar datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Introducción a la sincronización de datos sin conexión]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!--HONumber=42-->
