<properties 
	pageTitle="Introducción a los datos (JavaScript de la Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación JavaScript de la Tienda Windows." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>	

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="glenga"/>


# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET backend">Back-end de .NET</a> | 
	<a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript backend" class="current">Back-end de JavaScript</a>
</div>


Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

>[AZURE.NOTE]En este tema se muestra cómo puede usar Visual Studio 2013 para agregar Servicios móviles de Azure a un proyecto de la Tienda Windows. Puede agregar el mismo servicio móvil de back-end de JavaScript a un proyecto de aplicación universal de Windows. Para obtener más información, consulte la [versión de la aplicación universal de Windows](mobile-services-javascript-backend-windows-universal-javascript-get-started-data.md) de este tutorial.

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de aplicación de la Tienda Windows][Obtener la aplicación de la Tienda Windows] 
2. [Crear el servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualización de la aplicación para usar Servicios móviles]
5. [Probar la aplicación en Servicios móviles]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure.](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F)
* Visual Studio 2013, con lo que es más fácil conectar su aplicación de la Tienda Windows con Servicios móviles. Para completar el mismo procedimiento básico con Visual Studio 2012, siga los pasos en el tema <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data-vs2012">Introducción a los datos en Servicios móviles con Visual Studio 2012</a>. 

<h2><a name="download-app"></a>Descarga del proyecto GetStartedWithData</h2>

Este tutorial se basa en la [aplicación GetStartedWithMobileServices], que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.  

1. Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores] (en inglés). 

2. En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado, expanda la carpeta **js** y examine el archivo default.js.

   	Observe que los objetos **TodoItem** agregados se almacenan en un objeto **Lista** en memoria.

3. Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4. En la aplicación, escriba algo de texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	![][0]  

   	Observe que el texto guardado se muestra en la segunda columna debajo de **Consultar y actualizar datos**.

##<a name="create-service"></a>Creación de un servicio móvil nuevo desde Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>En el Explorador de soluciones, expanda las carpetas **servicios**, **servicios móviles**, **&<su_servicio>;**, abra el archivo de script service.js y observe la nueva variable global, que se asemeja al siguiente ejemplo:</p> 

		<pre><code>var todolistClient = new MicrosoftAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.</p>
</li>
</ol>

##<a name="add-table"></a>Incorporación de una tabla nueva para el almacenamiento de datos

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

>[AZURE.NOTE]Las tablas nuevas se crean con las columnas Id, __createdAt, __updatedAt y __version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

#<a name="update-app"></a>Actualización de la aplicación para utilizar el servicio móvil

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##<a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2. Al igual que antes, escriba texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

5. En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Examinar del portal y haga clic en **Actualizar**. 

  	Observe que el valor completo ha cambiado de **false** a **true**.

6. En el archivo de proyecto default.js, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed ite
		ms. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listIte
		ms.winControl.itemDataSource = todoIte
		ms.dataSource;
               });            
        };

7. En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Actualizar**.

   	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos gestionados en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

* [Introducción a la autenticación]
  <br/>Aprenda la manera de autenticar a los usuarios de la aplicación.

* [Introducción a las notificaciones de inserción]
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con HTML/JavaScript]
  <br/>Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.

<!-- Anchors. -->

[Obtener la aplicación de la Tienda Windows]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualización de la aplicación para usar Servicios móviles]: #update-app
[aplicación GetStartedWithMobileServices]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validar y modificar datos con scripts]: /es-es/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
[Limitación de consultas con paginación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started/
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[SDK de servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=257545
[Sitio de ejemplos de código para desarrolladores]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library/


<!--HONumber=42-->
