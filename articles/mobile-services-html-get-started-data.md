<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introducción a los datos en Servicios móviles
=============================================

[C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/es-es/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/es-es/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/es-es/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación HTML. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación HTML. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started-html).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación HTML](#download-app)
2.  [Creación del servicio móvil](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento](#add-table)
4.  [Actualización de la aplicación para usar Servicios móviles](#update-app)
5.  [Prueba de la aplicación en Servicios móviles](#test-app)

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F).

### Requisitos adicionales

Puede hospedar la aplicación GetStartedWithData en cualquier servidor web. Sin embargo, para mayor comodidad se proporcionan scripts que permiten ejecutar la aplicación en `http://localhost:8000`.

-   Para usar localhost, este tutorial requiere que uno de los siguientes servidores web se esté ejecutando en su equipo local:

    -   **En Windows**: IIS Express. IIS Express lo instala el [instalador de plataforma web de Microsoft].
    -   **En MacOS X**: Python, que ya debería estar instalado.
    -   **En Linux**: Python. Debe instalar la [última versión de Python].

    Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.

-   Un servidor web compatible con HTML5.

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se basa en la [aplicación GetStartedWithData](http://go.microsoft.com/fwlink/?LinkID=286345), una aplicación HTML5. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  [Descarga de los archivos del proyecto de la aplicación HTML](http://go.microsoft.com/fwlink/?LinkID=286345).

2.  En un editor HTML, abra el proyecto descargado y examine el archivo app.js.

	Observe que los elementos agregados se almacenan en un objeto **Array** en memoria (**staticItems**). Actualice la página y los datos desaparecerán. No se mantienen.

3.  Inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

    **Nota:**

    En un equipo con Windows, escriba "R" cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

    De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

4.  Abra la URL <http://localhost:8000/> en un explorador web para iniciar la aplicación.

5.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*, en **Enter new task** y, a continuación, haga clic en **Add**.

	![][0]  

	Observe que el texto guardado se agrega a la matriz **staticItems** y la página se actualiza para mostrar el nuevo elemento.

Creación de un servicio móvilCreación de un servicio móvil en el Portal de administración
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil
----------------------------------------------------------------------------------

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

	![][5]

	Se muestra el cuadro de diálogo **Create new table**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic en el botón de comprobación.

	![](./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png)

	Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos predeterminados definidos. Esto significa que cualquiera que tenga la clave de aplicación, que se distribuye con la aplicación, puede tener acceso a los datos de la tabla y modificarlos.

    <div class="dev-callout"> 
    <b>Nota</b> 
    <p>El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.</p> 
    </div>

1.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

2.  Haga clic en la pestaña **Columns**. Verifique que las siguientes columnas predeterminadas se hayan creado automáticamente:

    <table  border="1" cellpadding="10">
     	<tr>
     	<th>Nombre de columna</th>
    
     	<th>Tipo</th>
    
     	<th>Índice</th>
    
     	</tr>
    
     	<tr>
     	<td>id</td>
    
     	<td>cadena</td>
    
     	<td>Indizada</td>
    
     	</tr>
    
     	<tr>
     	<td>__createdAt</td>
    
     	<td>fecha</td>
    
     	<td>Indizada</td>
    
     	</tr>
    
     	<tr>
     	<td>__updatedAt</td>
    
     	<td>fecha</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
    
     	<tr>
     	<td>__version</td>
    
     	<td>marca de tiempo (MSSQL)</td>
    
     	<td><font  color="transparent">-</font>
    </td>
    
     	</tr>
     	
     	</table>

	Este es el requisito mínimo para una tabla en Servicios móviles.

    <div class="dev-callout"><b>Nota:</b>
    <p>Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.</p>
    </div>

1.  En la pestaña **Configure**, compruebe que `localhost` ya esté incluido en la lista **Allow requests from host names** bajo **Cross-Origin Resource Sharing (CORS)**. Si no lo está, escriba `localhost` en el campo **Host name** y, a continuación, haga clic en **Save**.

	![](./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png)

    <div class="dev-callout"><b>Nota:</b>
        <p>Si implementa la aplicación de inicio rápido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista <strong>Allow requests from host names</strong>. Para obtener más información, consulte <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx" target="_blank">Uso compartido de recursos entre orígenes</a>.</p>
    </div>

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

Actualización de la aplicaciónActualización de la aplicación para usar el servicio móvil para el acceso a datos
---------------------------------------------------------------------------------------------------------------

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Panel** y tome nota de la dirección que aparece en **Site URL**; a continuación, haga clic en **Manage keys** y tome nota de la clave indicada en **Application key**.

	![][8]

Necesitará estos valores al obtener acceso al servicio móvil desde el código de la aplicación.

1.  En el editor web, abra el archivo de proyecto index.html y agregue lo siguiente a las referencias de script de la página:

		<script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

2.  En el editor, abra el archivo app.js, quite la marca del comentario del siguiente código que define la variable **MobileServiceClient** y, a continuación, proporcione la dirección URL y la clave de la aplicación desde el servicio móvil en el constructor **MobileServiceClient**, en dicho orden.

		var MobileServiceClient = WindowsAzure.MobileServiceClient,
		client = new MobileServiceClient('AppUrl', 'AppKey'),              

De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil.

1.  Convierta en comentario las líneas de código siguientes:

         var itemCount = 0;
         var staticItems = [];

    Los datos se almacenarán en el servicio móvil y no en una matriz en memoria.

2.  Quite la marca de comentario de la línea de código siguiente:

         todoItemTable = client.getTable('todoitem');

	Este código crea un objeto proxy (**todoItemTable**) para la base de datos SQL **TodoItem**. 

3.  Reemplace el controlador de eventos **\$('\#add-item').submit** por el código siguiente:

         $('#add-item').submit(function(evt) {
             var textbox = $('#new-item-text'),
                 itemText = textbox.val();
             if (itemText !== '') {
                 todoItemTable.insert({ text: itemText, complete: false })
                     .then(refreshTodoItems);
             }
             textbox.val('').focus();
             evt.preventDefault();
         });

Este código inserta un elemento nuevo en la tabla.

1.  Reemplace el método **refreshTodoItems** por el código siguiente:

         function refreshTodoItems() {

             var query = todoItemTable;

             query.read().then(function (todoItems) {
                 listItems = $.map(todoItems, function(item) {
                     return $('<li>')
                         .attr('data-todoitem-id', item.id)
                         .append($('<button class="item-delete">Delete</button>'))
                         .append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
                         .append($('<div>').append($('<input class="item-text">').val(item.text)));
                 });
                           
                 $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                 $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
             });
         }

    Esto envía una consulta al servicio móvil que devuelve todos los elementos. Los resultados se recorren en iteración y los datos se muestran en la página.

2.  Reemplace los controladores de eventos **\$(document.body).on('change', '.item-text')** y **\$(document.body).on('change', '.item-complete')** por el código siguiente:

         $(document.body).on('change', '.item-text', function() {
             var newText = $(this).val();
             todoItemTable.update({ id: getTodoItemId(this), text: newText });
         });

         $(document.body).on('change', '.item-complete', function() {
             var isComplete = $(this).prop('checked');
             todoItemTable.update({ id: getTodoItemId(this), complete: isComplete })
                 .then(refreshTodoItems);
         });
         
        De este modo se envía una actualización del elemento al servicio móvil cuando se cambia el texto o se marca la casilla.

3.  Reemplace el controlador de eventos **\$(document.body).on('click', '.item-delete')** por el código siguiente:

        $(document.body).on('click', '.item-delete', function () {
            todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
        });

    De esta forma se envía una eliminación al servicio móvil al hacer clic en el botón **Delete**.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  Vuelva a cargar la dirección URL <http://localhost:8000/> en un explorador web para iniciar la aplicación.

    **Nota:**

    Si es necesario reiniciar el servidor web, repita los pasos de la primera sección.

2.  Al igual que anteriormente, escriba texto en **Enter new task** y, a continuación, haga clic en **Add**.

	Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

	![][9]
          
	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

	Observe que el valor completo ha cambiado de **false** a **true**.

1.  En el archivo de proyecto app.js, busque el método **RefreshTodoItems** y reemplace la línea de código que define `query` por la siguiente:

         var query = todoItemTable.where({ complete: false });

2.  Vuelva a cargar la página y marque otro de los elementos de la lista.

	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

Pasos siguientes
----------------

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación HTML para que funcione con los datos de los Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/es-es/develop/mobile/tutorials/add-paging-to-data-html)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos y aprendido a autenticar a los usuarios de su aplicación, pruebe uno de estos otros tutoriales al finalizar [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-html).

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Validate and modify data with scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Refine queries with paging]: /es-es/develop/mobile/tutorials/add-paging-to-data-html
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started
[Get started with authentication]: /es-es/develop/mobile/tutorials/get-started-with-users-html

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[GetStartedWithData app]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client

[Cross-origin resource sharing]: http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx