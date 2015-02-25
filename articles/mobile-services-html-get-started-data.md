<properties pageTitle="Introducción a los datos (HTML 5) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo empezar a usar Servicios móviles para aprovechar datos en su aplicación HTML." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación HTML. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

>[AZURE.NOTE]Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación HTML. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa Servicios móviles, considere la posibilidad de completar antes el tutorial <a href="/es-es/develop/mobile/tutorials/get-started-html">Introducción a los Servicios móviles</a>.

Este tutorial le guiará a través de estos pasos básicos:

1. [Descarga del proyecto de la aplicación HTML]
2. [Crear el servicio móvil]
3. [Agregar una tabla de datos para almacenamiento]
4. [Actualización de la aplicación para usar Servicios móviles]
5. [Probar la aplicación en Servicios móviles]

> [AZURE.IMPORTANT] para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F"%20target="_blank).

###Requisitos adicionales

Puede hospedar la aplicación GetStartedWithData en cualquier servidor web. Sin embargo, para mayor comodidad se proporcionan scripts que permiten ejecutar la aplicación en `http://localhost:8000`.
 
+ Para usar localhost, este tutorial requiere que uno de los siguientes servidores web se esté ejecutando en su equipo local:

	+  **En Windows**: IIS Express. IIS Express lo instala el [instalador de plataforma web de Microsoft].   
	+  **En MacOS X**: Python, que ya debería estar instalado.
	+  **En Linux**: Python. Debe instalar la [última versión de Python] (información en inglés). 
	
	Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.  

+ Un servidor web compatible con HTML5.

<h2><a name="download-app"></a>Descarga del proyecto GetStartedWithData</h2>

Este tutorial se basa en la [aplicación GetStartedWithData], una aplicación HTML5. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria. 

1. [Descarga de los archivos del proyecto de la aplicación HTML][Aplicación GetStartedWithData].

2. En un editor HTML, abra el proyecto descargado y examine el archivo app.js.

   	Observe que los elementos agregados se almacenan en un objeto **Array** en memoria (**staticItems**). Actualice la página y los datos desaparecerán. No se mantienen.

3. Inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

	+ **launch-windows**  (equipos Windows)
	+ **launch-mac.command** (equipos Mac OS X)
	+ **launch-linux.sh** (equipos Linux)

	> [AZURE.NOTE] En un equipo con Windows, escriba "R" cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.
	
	De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

4. Abra la URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación.

5. En la aplicación, escriba texto significativo, como _Complete the tutorial_, en **Introducir nueva tarea** y, a continuación, haga clic en **Agregar**.

   	![][0]  

   	Observe que el texto guardado se agrega a la matriz **staticItems** y la página se actualiza para mostrar el nuevo elemento.

<h2><a name="create-service"></a>Creación de un servicio móvil en el Portal de administración</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Incorporación de una tabla nueva al servicio móvil</h2>

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

   	![][5]

   	Esto muestra el cuadro de diálogo **Create new table**.

3. En **Nombre de tabla** escriba _TodoItem_ y, a continuación, haga clic en el botón de comprobación.

  	![][6]

  	Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos predeterminados definidos. Esto significa que cualquiera que tenga la clave de aplicación, que se distribuye con la aplicación, puede tener acceso a los datos de la tabla y modificarlos.

    > [AZURE.NOTE] El mismo nombre de tabla se utiliza en la guía de inicio rápido de Servicios móviles. Sin embargo, cada tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.

4. Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

5. Haga clic en la pestaña **Columnas**. Verifique que las siguientes columnas predeterminadas se hayan creado automáticamente: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Nombre de columna</th>
 	<th>Tipo</th>
 	<th>Índice</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>string</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>date</td>
 	<td>Indexed</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>date</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 

  	Este es el requisito mínimo para una tabla en Servicios móviles. 

    > [AZURE.NOTE] Cuando está habilitado el esquema dinámico en su servicio móvil, se crean columnas automáticamente cuando se envían objetos JSON al servicio móvil mediante una operación de inserción o de actualización.

6. En la pestaña **Configurar**, compruebe que  `localhost` ya aparece en la lista **Permitir solicitudes de nombres de host** en **Compartir recursos entre orígenes (CORS)**. Si no es así, escriba  `localhost` en el campo **Nombre de host** y, a continuación, haga clic en **Guardar**.

  	![][11]

	> [AZURE.IMPORTANT] Si implementa la aplicación de inicio rápido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista **Permitir solicitudes de nombres de host**. Para obtener más información, consulte [Uso compartido de recursos entre orígenes](http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx"%20target="_blank).

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

<h2><a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos</h2>

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local. 

3. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

4. Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación, haga clic en **Administrar claves** y anote la **clave de la aplicación**.

   	![][8]

  	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

1. En el editor web, abra el archivo de proyecto index.html y agregue lo siguiente a las referencias de script de la página:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

5. En el editor, abra el archivo app.js, quite la marca de comentario del siguiente código que define la variable **MobileServiceClient** y, a continuación, proporcione la dirección URL y la clave de la aplicación desde el servicio móvil en el constructor **MobileServiceClient**, en dicho orden.

	    var MobileServiceClient = MicrosoftAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil.

6. Convierta en comentario las líneas de código siguientes:

		var itemCount = 0;
		var staticItems = [];

	Los datos se almacenarán en el servicio móvil y no en una matriz en memoria.

6. Quite la marca de comentario de la línea de código siguiente:

        todoItemTable = client.getTable('todoitem');

   	Este código crea un objeto proxy (**todoItemTable**) para la Base de datos SQL **TodoItem**. 

7. Reemplace el controlador de eventos **$('#add-item').submit** por el código siguiente:

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

8. Reemplace el método **refreshTodoItems** por el código siguiente:

		function refreshTodoItems() {

			var query = todoItemTable;

			query.read().then(function(todoItems) {
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

9. Reemplace los controladores de eventos **$(document.body).on('change', '.item-text')** y **$(document.body).on('change', '.item-complete')** por el código siguiente:
        
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

10. Reemplace el controlador de eventos **$(document.body).on('click', '.item-delete')** por el código siguiente:

		$(document.body).on('click', '.item-delete', function () {
			todoItemTable.del({ id: getTodoItemId(this) }).then(refreshTodoItems);
		});

	De esta forma se envía una eliminación al servicio móvil al hacer clic en el botón **Delete**.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

<h2><a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo</h2>

4. Vuelva a cargar la dirección URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación.

    > [AZURE.NOTE] Si es necesario reiniciar el servidor web, repita los pasos de la primera sección.

2. Al igual que anteriormente, escriba texto en **Enter new task** y, a continuación, haga clic en **Add**. 

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

   	![][9]
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

5. En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Examinar del portal y haga clic en **Actualizar**. 

  	Observe que el valor completo ha cambiado de **false** a **true**.

6. En el archivo de proyecto app.js, busque el método **RefreshTodoItems** y reemplace la línea de código que define  `query` por la siguiente:

   		var query = todoItemTable.where({ complete: false });

7. Vuelva a cargar la página y marque otro de los elementos de la lista.

   	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación HTML para que funcione con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

* [Validar y modificar datos con scripts]
  <br/>Obtenga más información sobre el uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* [Limitación de consultas con paginación]
  <br/>Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos controlada en una única solicitud.
 
Una vez que haya completado la serie de datos y aprendido a autenticar a los usuarios de su aplicación, pruebe uno de estos otros tutoriales al finalizar [Introducción a la autenticación].

<!-- Anchors. -->
[Descarga del proyecto de la aplicación HTML]: #download-app
[Crear el servicio móvil]: #create-service
[Agregar una tabla de datos para almacenamiento]: #add-table
[Actualización de la aplicación para usar Servicios móviles]: #update-app
[Probar la aplicación en Servicios móviles]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png




[5]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
[6]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png

[8]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png

[11]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png

<!-- URLs. -->
[Validar y modificar datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-html
[Introducción a los servicios móviles]: /es-es/develop/mobile/tutorials/get-started
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-html

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[Aplicación GetStartedWithData]:  http://go.microsoft.com/fwlink/?LinkID=286345

[Referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/develop/mobile/how-to-guides/work-with-html-js-client

[Uso compartido de recursos entre orígenes]: http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx



<!--HONumber=42-->
