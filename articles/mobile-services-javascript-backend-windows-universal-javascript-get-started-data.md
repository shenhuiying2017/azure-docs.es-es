<properties urlDisplayName="Get Started with Data" pageTitle="Introducción a los datos (JavaScript para la Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación JavaScript de la Tienda Windows." metaCanonical="https://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />


# Incorporación de servicios móviles a una aplicación existente
[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

En este tema se muestra cómo usar Servicios móviles de Azure para aprovechar los datos en una aplicación universal de Windows. Las soluciones de aplicaciones universales de Windows incluyen aplicaciones y un proyecto compartido común tanto para la Tienda Windows 8.1 como para la Tienda de Windows Phone 8.1. Para obtener más información, consulte [Generación de aplicaciones universales dirigidas a Windows y a Windows Phone](http://msdn.microsoft.com/es-es/library/windows/apps/xaml/dn609832.aspx).

En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación universal de Windows que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

>[WACOM.NOTE]En este tema se muestra cómo utilizar las herramientas de Visual Studio Express 2013 con Update 3 para Windows con la finalidad de conectar un nuevo servicio móvil a una aplicación universal de Windows. Se pueden seguir estos mismos pasos para conectar un servicio móvil a una aplicación de la Tienda Windows o de la Tienda de Windows Phone 8.1. Para conectar un servicio móvil a una aplicación de Windows Phone 8.0 o Windows Phone Silverlight 8.1, consulte [Introducción a los datos para Windows Phone](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de aplicación de la Tienda Windows][Obtener la aplicación de la tienda Windows] 
2. [Crear el servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualizar la aplicación para usar Servicios móviles]
5. [Probar la aplicación en Servicios móviles]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Express 2013 para Windows</a> (Update 2 o una versión posterior). 

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Crear un servicio móvil nuevo desde Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8">
<li><p>En el Explorador de soluciones, vaya a la subcarpeta <strong>services\mobileService\scripts</strong>, abra el archivo de script service.js y observe la nueva variable global, similar al ejemplo siguiente:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.</p>
</li>
<li><p>Abra el archivo de proyecto default.html, busque la referencia al nuevo archivo de script service.js y asegúrese de que la ruta de acceso a la que se hace referencia es similar a la siguiente:</p>
<pre><code>&lt;script src="/services/mobileServices/scripts/todolist.js"&gt;</script></code></pre>
<p>Actualmente, se produce un error en Visual Studio que genera un nombre de carpeta incorrecto en la ruta de acceso.</p></li>
<li><p>Haga clic con el botón secundario en el proyecto de aplicación de Windows Phone y con el botón primario en <strong>Add</strong>, elija <strong>Servicio conectado...</strong>, seleccione el servicio móvil que acaba de crear y haga clic en <strong>Aceptar</strong>. </p>
<p>El mismo archivo de código nuevo se agrega al proyecto de aplicación de la Tienda de Windows Phone. Asegúrese de corregir también en este caso la ruta de acceso de referencia que se agrega al archivo default.html.</p></li>
</ol>

##<a name="add-table"></a>Adición de una tabla nueva al servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[WACOM.NOTE]Las tablas nuevas se crean con las columnas Id, __createdAt, __updatedAt y __version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/es-es/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Actualizar la aplicación para usar el servicio móvil

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-azure-hosted"></a>Prueba del servicio móvil hospedado en Azure

Ya puede probar las dos versiones de la aplicación universal para Windows con el servicio móvil hospedado en Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

<ol start="4">
<li><p>En el <a href="https://manage.windowsazure.com/" target="_blank">Portal de administración</a>, haga clic en <strong>Servicios móviles</strong> y elija su servicio móvil.<p></li>
<li><p>Haga clic en la pestaña <strong>Datos</strong> y después en <strong>Examinar</strong>.</p>
<p>Observe que la tabla <strong>TodoItem</strong> ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.</p></li>
</ol>

![](./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png)
     	
Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial se demostraron los aspectos básicos de la habilitación de una aplicación universal de Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validación y modificación de datos con scripts]
  <br/>Más información sobre el uso de scripts de servidor en servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos se controla en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

* [Introducción a la autenticación]
  <br/>Cómo autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción] 
  <br/>Vea cómo enviar una notificación de inserción muy básica a su aplicación.

* [Documentación de referencia conceptual de Servicios móviles con HTML/JavaScript]
  <br/>Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.

<!-- Anchors. -->

[Obtención de una aplicación de la Tienda Windows]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualizar la aplicación para usar Servicios móviles]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validación y modificación de datos con scripts]: /es-es/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Limitación de consultas con paginación.]: /es-es/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Documentación de referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library/
