<properties 
	pageTitle="Empezar a trabajar con datos (Windows Universal) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación universal de Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

En este tema se muestra cómo usar Servicios móviles de Azure para aprovechar los datos en una aplicación universal de Windows. Las soluciones de aplicaciones universales de Windows incluyen aplicaciones y un proyecto compartido común tanto para la Tienda Windows 8.1 como para la Tienda de Windows Phone 8.1. Para obtener más información, consulte [Generación de aplicaciones universales dirigidas a Windows y a Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación universal de Windows que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

>[AZURE.NOTE]Este tema muestra cómo usar las herramientas de Visual Studio Professional 2013 Update 3 para conectar un nuevo servicio móvil a una aplicación universal para Windows. Se pueden seguir estos mismos pasos para conectar un servicio móvil a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para conectar un servicio móvil a una aplicación de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte [Introducción a los datos en Windows Phone](/es-es/documentation/articles/mobile-services-windows-phone-get-started-data).

> Si no puede actualizar a Visual Studio Professional 2013 Update 3 o prefiere agregar de forma manual el proyecto de servicio móvil a la solución de aplicación de la Tienda Windows, consulte [esta versión](/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data) del tema.

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de aplicación de la Tienda Windows][Obtención de una aplicación de la Tienda Windows] 
2. [Crear el servicio móvil desde Visual Studio]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualización de la aplicación para usar el servicio móvil]
5. [Probar la aplicación en Servicios móviles]
6. [Ver los datos cargados en el Portal de administración de Azure]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure.](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F)
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 para Windows</a> (Update 2 o una versión posterior).


##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Creación de un servicio móvil nuevo desde Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>En el Explorador de soluciones, abra el archivo de código App.xaml.cs en la carpeta de proyecto GetStartedWithData.Shared y observe el nuevo campo estático que se agregó a la clase <strong>App</strong> en un bloque de compilación condicional de aplicación de la Tienda Windows, similar al ejemplo siguiente:</p> 

		<pre><code>public static Microsoft.MicrosoftAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.MicrosoftAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Este código proporciona acceso al nuevo servicio móvil en su aplicación mediante el uso de una instancia de la <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">clase MobileServiceClient</a>. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Este campo estático está disponible para todas las páginas en la aplicación.</p>
</li>
<li><p>Haga clic con el botón derecho en el proyecto de aplicación de Windows Phone, haga clic en <strong>Agregar</strong> y <strong>Servicio conectado...</strong>, seleccione el servicio móvil que acaba de crear y haga clic en <strong>Aceptar</strong>. </p>
<p>Se agrega el mismo código al archivo compartido App.xaml.cs, aunque esta vez en un bloque de compilación condicional de aplicación de Windows Phone.</p></li>
</ol>

En este punto, se conectan al nuevo servicio móvil las aplicaciones de la Tienda Windows y de la Tienda de Windows Phone. El paso siguiente consiste en crear la tabla TodoItem nueva en el servicio móvil.

##<a name="add-table"></a>Incorporación de una tabla nueva al servicio móvil

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Las tablas nuevas se crean con las columnas Id, __createdAt, __updatedAt y __version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Actualización de la aplicación para utilizar el servicio móvil

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Prueba del servicio móvil hospedado en Azure

Ya puede probar las dos versiones de la aplicación universal para Windows con el servicio móvil hospedado en Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>En el <a href="https://manage.windowsazure.com/" target="_blank">Portal de administración</a>, haga clic en <strong>Servicios móviles</strong> y, a continuación, en su servicio móvil.<p></li>
<li><p>Haga clic en la pestaña <strong>Datos</strong> y, a continuación, en <strong>Examinar</strong>.</p>
<p>Observe que la tabla <strong>TodoItem</strong> ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación universal de Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos gestionados en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

* [Introducción a la autenticación]
  <br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de servicios móviles con .NET]
  <br/>Obtenga más información sobre el uso de Servicios móviles con .NET.
  
<!-- Anchors. -->

[Obtener la aplicación de la Tienda Windows]: #download-app
[Crear el servicio móvil desde Visual Studio]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualización de la aplicación para usar el servicio móvil]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Ver los datos cargados en el Portal de administración de Azure]: #view-data
[Pasos siguientes]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Validar y modificar datos con scripts]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
[Limitación de consultas con paginación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Referencia conceptual de Servicios móviles con .NET]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkID=510826

[Clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030


<!--HONumber=42-->
