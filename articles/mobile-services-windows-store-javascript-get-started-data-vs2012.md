<properties linkid="develop-mobile-tutorials-get-started-with-data-js" urlDisplayName="Get Started with Data" pageTitle="Get started with data (JavaScript) - Mobile Services" metaKeywords="" description="Learn how to get started using data with Azure Mobile Services." metaCanonical="https://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-data-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with data in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Introducción a los datos en Servicios móviles con Visual Studio 2012
====================================================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012 "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-data-js-vs2012 "JavaScript para Tienda Windows")[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/es-es/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/es-es/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial agrega la funcionalidad de Servicios móviles a una aplicación de la Tienda Windows creada en Visual Studio 2012. Visual Studio 2013 incluye nuevas características que facilitan la conexión de su aplicación de la Tienda Windows a Servicios móviles. Para obtener más información, consulte [Introducción a los datos en Servicios móviles](/es-es/develop/mobile/tutorials/get-started-with-data-js/).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows](#download-app)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F).

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se basa en la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/?LinkId=262308), que es una aplicación de la Tienda Windows. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores](http://go.microsoft.com/fwlink/?LinkId=262308) (en inglés).

	![][10]

2.  En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado, expanda la carpeta **js** y examine el archivo default.js.

	Observe que los objetos **TodoItem** agregados se almacenan en un objeto **List** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	![][0]  

	Observe que el texto guardado aparece en la segunda columna bajo **Query and update data**.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el nombre del proyecto y, a continuación, seleccione **Administrar paquetes de NuGet**.

2.  En el panel izquierdo, seleccione la categoría **Online**, busque `WindowsAzure.MobileServices.WinJS`, haga clic en **Install** en el paquete **Servicios móviles de Azure para WinJS** y, a continuación, acepte el contrato de licencia.

	![](./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png)

	De este modo, se agrega la biblioteca de cliente de Servicios móviles al proyecto.

1.  En el archivo de proyecto default.html, agregue la siguiente referencia de script en el encabezado de la página:

         <script type="text/javascript" src="/js/MobileServices.js"></script>

2.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

3.  Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

	![][8]

	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

1.  En Visual Studio, abra el archivo default.js, quite la marca de comentario del siguiente código que define la variable **client** y, a continuación, proporcione la dirección URL y la clave de la aplicación desde el servicio móvil en el constructor **MobileServiceClient**, en dicho orden.

             var client = new WindowsAzure.MobileServiceClient(
                 "AppUrl",
                 "appKey"
             );

	De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil.

1.  Quite la marca de comentario de la línea de código siguiente:

         var todoTable = client.getTable('TodoItem');

        Este código crea un objeto proxy (**todoTable**) para la nueva tabla de base de datos. 

2.  Reemplace la función **InsertTodoItem** por el siguiente código:

         var insertTodoItem = function (todoItem) {
             // Inserta una fila nueva en la base de datos. Cuando la operación se completa
             // y Servicios móviles dispone de un identificador, el elemento se agrega a la lista de enlaces.
             todoTable.insert(todoItem).done(function (item) {
                 todoItems.push(item);
             });
         };

	Este código inserta un elemento nuevo en la tabla.

1.  Reemplace la función **RefreshTodoItems** por el siguiente código:

         var refreshTodoItems = function () {
             // Este código actualiza las entradas de la lista al consultar la tabla TodoItems. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };      

        Esto define el enlace a la colección de elementos en todoTable, que contiene todos los objetos completados devueltos por el servicio móvil. 

2.  Reemplace la función **UpdateCheckedTodoItem** por el siguiente código:

         var updateCheckedTodoItem = function (todoItem) {
             // Este código toma un TodoItem recientemente completado y actualiza la base de datos. 
             todoTable.update(todoItem);
         };

        Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

	![][9]
          
	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

	Observe que el valor completo ha cambiado de **false** a **true**.

1.  En el archivo de proyecto default.js, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

         var refreshTodoItems = function () {                     
             // Consulta más avanzada que filtra los elementos completados. 
             todoTable.where({ complete: false })
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });            
         };

2.  En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Refresh**.

	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/es-es/develop/mobile/tutorials/add-paging-to-data-js)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-js)
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.


<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-quickstart-startup.png






[7]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-add-nuget-package-js.png
[8]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-dashboard-tab.png
[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2012/mobile-data-sample-download-js.png


<!-- URLs. -->
[Validate and modify data with scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Refine queries with paging]: /es-es/develop/mobile/tutorials/add-paging-to-data-js
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012
[C# and XAML]: /es-es/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/?LinkId=262308