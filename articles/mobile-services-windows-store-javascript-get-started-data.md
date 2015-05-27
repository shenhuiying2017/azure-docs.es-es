<properties 
	pageTitle="Incorporación de Servicios móviles a una aplicación existente (Windows Store JavaScript) | Centro de desarrollo móvil" 
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
	ms.date="02/26/2015" 
	ms.author="glenga"/>


# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

#Información general
Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdocumentation%2Farticles%2Fmobile-services-windows-store-javascript-get-started-data%2F).
* Visual Studio 2013, que usará para conectar su proyecto de aplicación de la Tienda Windows con Servicios móviles.

##Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithMobileServices][Developer Code Samples site], que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1. Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores] (en inglés). 

2. En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado, expanda la carpeta **js** y examine el archivo default.js.

   	Observe que los objetos **TodoItem** agregados se almacenan en un objeto **Lista** en memoria.

3. Presione la tecla **F5** para recopilar el proyecto e iniciar la aplicación.

4. En la aplicación, escriba algo de texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	![][0]

   	Observe que el texto guardado se muestra en la segunda columna debajo de **Consultar y actualizar datos**.

##<a name="create-service"></a>Creación de un servicio móvil nuevo desde Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="7">
<li><p>En el Explorador de soluciones, expanda las carpetas **servicios**, **servicios móviles**, **&lt;your_service>**, abra el archivo de script service.js y observe la nueva variable global, que se asemeja al siguiente ejemplo:</p> 

		<pre><code>var todolistClient = new WindowsAzure.MobileServiceClient(
                "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");</code></pre>

	<p>Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.</p>
</li>
</ol>

##Incorporación de una tabla nueva para el almacenamiento de datos

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

#Actualización de la aplicación para utilizar el servicio móvil

[AZURE.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

##Prueba de la aplicación con su servicio móvil nuevo

1. En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2. Al igual que antes, escriba texto en **Insertar TodoItem** y, a continuación, haga clic en **Guardar**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

5. En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Examinar del portal y haga clic en **Actualizar**.

  	Observe que el valor completo ha cambiado de **false** a **true**.

6. En el archivo de proyecto default.js, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7. En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Actualizar**.

   	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la posibilidad de obtener información acerca de uno de estos otros temas:

* [Introducción a la autenticación] <br/>Aprenda a autenticar a los usuarios de su aplicación.

* [Introducción a las notificaciones de inserción] <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

* [Referencia conceptual de Servicios móviles con HTML/JavaScript](mobile-services-html-how-to-use-client-library.md) <br/>Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png

[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png


<!-- URLs. -->
[Validate and modify data with scripts]: mobile-services-windows-store-javascript-validate-modify-data-server-scripts.md
[Refine queries with paging]: mobile-services-windows-store-javascript-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-javascript-get-started.md
[Get started with data]: mobile-services-windows-store-javascript-get-started-data.md
[Introducción a la autenticación]: mobile-services-windows-store-javascript-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[sitio de código de ejemplo para desarrolladores]: http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

<!--HONumber=54-->