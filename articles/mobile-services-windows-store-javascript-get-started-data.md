<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a los datos en Servicios móviles
=============================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "Back-end de JavaScript")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial requiere Visual Studio 2013, con lo que es más fácil conectar su aplicación de la Tienda de Windows con Servicios móviles. Para completar el mismo procedimiento básico con Visual Studio 2012, siga los pasos en el tema [Introducción a los datos en Servicios móviles con Visual Studio 2012](/en-us/develop/mobile/tutorials/get-started-with-data-js-vs2012/).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows](#download-app)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

**Nota:**

para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-js%2F).

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se basa en la [aplicación GetStartedWithMobileServices (en inglés)](http://go.microsoft.com/fwlink/p/?LinkId=328660), que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la aplicación generada por el inicio rápido de Servicios móviles, excepto en que los elementos agregados se almacenan de manera local en la memoria.

1.  Descargue la versión de JavaScript de la aplicación de ejemplo GetStartedWithData desde el [sitio de código de ejemplo para desarrolladores](http://go.microsoft.com/fwlink/p/?LinkId=328660) (en inglés).

  ![][10]

2.  En Visual Studio 2012 Express para Windows 8, abra el proyecto descargado, expanda la carpeta **js** y examine el archivo default.js.

  Observe que los objetos **TodoItem** agregados se almacenan en un objeto **List** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

  ![][0] 

  Observe que el texto guardado aparece en la segunda columna bajo **Query and update data**.

Creación del servicio móvilCreación de un servicio móvil desde Visual Studio
----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  En el Explorador de soluciones, expanda las carpetas **servicios**, **servicios móviles**, **&lt;su\_servicio\>**, abra el archivo de script service.js y observe la nueva variable global, que se asemeja al siguiente ejemplo:

    ``` {}
    var todolistClient = new WindowsAzure.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
    ```

    Este código proporciona acceso a su nuevo servicio móvil en su aplicación mediante una variable global. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Como se agregó una referencia a este script en el archivo default.html, esta variable está disponible para todos los archivos de script a los que también se hace referencia desde esta página.

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil y actualización de la aplicación
-------------------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  En el archivo de script default.js, comente la línea que define la colección existente de elementos y, a continuación, quite la marca de comentario o agregue la siguiente línea de código y reemplace `<yourClient>;` por la variable que agregó al archivo service.js cuando conectó el proyecto al servicio móvil:

         var todoTable = <yourClient>.getTable('TodoItem');

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

    **Nota:**

    se crean tablas nuevas con solo una columna de identificador. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

3.  Reemplace la función **RefreshTodoItems** por el siguiente código:

         var refreshTodoItems = function () {
             // Este código actualiza las entradas de la lista al consultar la tabla. 
             todoTable.read().done(function (results) {
                 todoItems = new WinJS.Binding.List(results);
                 listItems.winControl.itemDataSource = todoItems.dataSource;
             });
         };

  Esto define el enlace a la colección de elementos en todoTable, que contiene todos los objetos **TodoItem** devueltos desde el servicio móvil. 

4.  Reemplace la función **UpdateCheckedTodoItem** por el siguiente código:

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

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js)
  <br/>Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-js)
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-js)
  <br/>Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-js)
  <br/>Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
  <br/>Obtenga más información acerca de cómo utilizar los Servicios móviles con HTML y JavaScript.


<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-javascript-get-started-data-vs2013/mobile-data-sample-download-js-vs12.png
