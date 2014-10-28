<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introducción a los datos en Servicios móviles con Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-js-vs2012" title="JavaScript para Tienda Windows" class="current">JavaScript para Tienda Windows</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-ios" title="iOS" class="current">iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-html" title="HTML" class="current">HTML</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial agrega la funcionalidad de Servicios m&oacute;viles a una aplicaci&oacute;n de la Tienda Windows creada en Visual Studio 2012. Visual Studio 2013 incluye nuevas caracter&iacute;sticas que facilitan la conexi&oacute;n de su aplicaci&oacute;n de la Tienda Windows a Servicios m&oacute;viles. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="/es-es/develop/mobile/tutorials/get-started-with-data-js/">Introducci&oacute;n a los datos en Servicios m&oacute;viles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows][]
2.  [Creación del servicio móvil][]
3.  [Incorporación de una tabla de datos para almacenamiento][]
4.  [Actualización de la aplicación para usar Servicios móviles][]
5.  [Prueba de la aplicación en Servicios móviles][]

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Descarga del proyecto</span>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][], que es una aplicación de la Tienda Windows. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores][aplicación GetStartedWithData] (en inglés).

    ![][]

2.  En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado, expanda la carpeta **js** y examine el archivo default.js.

    Observe que los objetos **TodoItem** agregados se almacenan en un objeto **List** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    ![][1]

    Observe que el texto guardado se muestra en la segunda columna, bajo **Consultar y actualizar datos**.

## <a name="create-service"></a><span class="short-header">Creación de un servicio móvil</span>Creación de un servicio móvil en el Portal de administración

[WACOM.INCLUDE [mobile-services-create-new-service-data][]]

## <a name="add-table"></a><span class="short-header">Incorporación de una tabla nueva</span>Incorporación de una tabla nueva al servicio móvil

[WACOM.INCLUDE [mobile-services-create-new-service-data-2][]]

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación</span>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2.  En el panel izquierdo, seleccione la categoría **en línea**, busque `WindowsAzure.MobileServices.WinJS`, haga clic en **Instalar** en el paquete **Servicios móviles de Azure para WinJS** y, a continuación, acepte el contrato de licencia.

    ![][2]

    De este modo, se agrega la biblioteca de cliente de Servicios móviles al proyecto.

3.  En el archivo de proyecto default.html, agregue la siguiente referencia de script en el encabezado de la página:

        <script type="text/javascript" src="/js/MobileServices.js"></script>

4.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

5.  Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][3]

    Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

6.  En Visual Studio, abra el archivo default.js, quite la marca de comentario del siguiente código que define la variable **client** y, a continuación, proporcione la dirección URL y la clave de la aplicación desde el servicio móvil en el constructor **MobileServiceClient**, en dicho orden.

            var client = new WindowsAzure.MobileServiceClient(
                "AppUrl",
                "appKey"
            );

    De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil.

7.  Quite la marca de comentario de la línea de código siguiente:

        var todoTable = client.getTable('TodoItem');

    Este código crea un objeto proxy (**todoTable**) para la nueva tabla de base de datos.

8.  Reemplace la función **InsertTodoItem** por el siguiente código:

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            });
        };

    Este código inserta un elemento nuevo en la tabla.

9.  Reemplace la función **RefreshTodoItems** por el siguiente código:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the TodoItems table. 
            todoTable.read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };      

    Esto define el enlace a la colección de elementos en todoTable, que contiene todos los objetos completados devueltos por el servicio móvil.

10. Reemplace la función **UpdateCheckedTodoItem** por el siguiente código:

        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
        };

    Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con su servicio móvil nuevo

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    ![][4]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

    Observe que el valor completo ha cambiado de **false** a **true**.

6.  En el archivo de proyecto default.js, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

        var refreshTodoItems = function () {                     
            // More advanced query that filters out completed items. 
            todoTable.where({ complete: false })
               .read()
               .done(function (results) {
                   todoItems = new WinJS.Binding.List(results);
                   listItems.winControl.itemDataSource = todoItems.dataSource;
               });            
        };

7.  En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Refresh**.

    Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][]
    
	Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][]
    
	Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación][]
    
	Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][]
    
	Aprenda a enviar una notificación de inserción muy básica a la aplicación.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/develop/mobile/tutorials/get-started-with-data-js-vs2012 "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone"
  [iOS]: /es-es/develop/mobile/tutorials/get-started-with-data-ios "iOS"
  [Android]: /es-es/develop/mobile/tutorials/get-started-with-data-android "Android"
  [HTML]: /es-es/develop/mobile/tutorials/get-started-with-data-html "HTML"
  [Xamarin.iOS]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android"
  [Introducción a los datos en Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-with-data-js/
  [Descarga del proyecto de la aplicación de la Tienda Windows]: #download-app
  [Creación del servicio móvil]: #create-service
  [Incorporación de una tabla de datos para almacenamiento]: #add-table
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F
  [aplicación GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkId=262308
  []: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png
  [1]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [2]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
  [Portal de administración]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-js
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-js
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012
