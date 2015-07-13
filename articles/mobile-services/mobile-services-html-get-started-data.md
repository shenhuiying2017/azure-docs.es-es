<properties 
	pageTitle="Incorporación de Servicios móviles a una aplicación existente (HTML 5) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo empezar a usar Servicios móviles en una aplicación HTML existente." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="glenga"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Información general 

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación HTML. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

>[AZURE.NOTE]El objeto de este tutorial es profundizar en el uso de Azure con Servicios móviles para almacenar y recuperar datos en una aplicación HTML. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles].

> [AZURE.IMPORTANT]Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2documentation%2Farticles%2Fmobile-services-html-get-started-data).

###Requisitos adicionales

Puede hospedar la aplicación GetStartedWithData en cualquier servidor web. Sin embargo, para mayor comodidad se proporcionan scripts que permiten ejecutar la aplicación en `http://localhost:8000`.
 
+ Para usar localhost, este tutorial requiere que uno de los siguientes servidores web se esté ejecutando en su equipo local:

	+  **En Windows**: IIS Express. IIS Express lo instala el [instalador de plataforma web de Microsoft].   
	+  **En MacOS X**: Python, que ya debería estar instalado.
	+  **En Linux**: Python. Debe instalar la [última versión de Python]. 
	
	Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.

+ Un servidor web compatible con HTML5.

##<a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData], una aplicación HTML5. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1. [Descarga de los archivos del proyecto de la aplicación HTML][GetStartedWithData app].

2. En un editor HTML, abra el proyecto descargado y examine el archivo app.js.

   	Observe que los elementos agregados se almacenan en un objeto **Array** en memoria (**staticItems**). Actualice la página y los datos desaparecerán. No se mantienen.

3. Inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

	+ **launch-windows** (equipos con Windows) 
	+ **launch-mac.command** (equipos con Mac OS X)
	+ **launch-linux.sh** (equipos con Linux)

	> [AZURE.NOTE]En un equipo con Windows, escriba `R` cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.
	
	De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

4. Abra la URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación.

5. En la aplicación, escriba un texto significativo, como _Realice el tutorial_, en **Introducir nueva tarea** y, a continuación, haga clic en **Agregar**.

   	![][0]

   	Observe que el texto guardado se agrega a la matriz **staticItems** y la página se actualiza para mostrar el nuevo elemento.

##<a name="create-service"></a>Creación de un servicio móvil nuevo en el Portal de administración

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

##<a name="add-table"></a>Agregar una tabla nueva al servicio móvil

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2. Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

   	Esto muestra el cuadro de diálogo **Create new table**.

3. En **Nombre de tabla** escriba _TodoItem_ y, a continuación, haga clic en el botón de comprobación.

  	Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos predeterminados definidos. Esto significa que cualquiera que tenga la clave de aplicación, que se distribuye con la aplicación, puede tener acceso a los datos de la tabla y modificarlos. La tabla se crea en un esquema que es específico de un servicio móvil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios móviles utilizan la misma base de datos.

4. Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

	>[AZURE.NOTE]Las tablas nuevas se crean con las columnas Id, __createdAt, __updatedAt y __version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

6. En la pestaña **Configurar**, compruebe que `localhost` ya aparezca en la lista **Permitir solicitudes de nombres de host** en **Compartir recursos entre orígenes (CORS)**. Si no es así, escriba `localhost` en el campo **Nombre de host** y luego haga clic en **Guardar**.


	> [AZURE.IMPORTANT]Si implementa la aplicación de inicio rápido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista **Permitir solicitudes de nombres de host**. Para obtener más información, consulte [Uso compartido de recursos entre orígenes](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

##<a name="update-app"></a>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

3. En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

4. Haga clic en la pestaña **Panel** y anote la **dirección URL del servicio móvil**; a continuación, haga clic en **Administrar claves** y anote la **clave de la aplicación**.

  	Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

1. En el editor web, abra el archivo de proyecto index.html y agregue lo siguiente a las referencias de script de la página:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.5.min.js"></script>

5. En el editor, abra el archivo app.js, quite la marca del comentario del siguiente código que define la variable **MobileServiceClient** y, a continuación, proporcione la dirección URL y la clave de la aplicación desde el servicio móvil en el constructor **MobileServiceClient**, en dicho orden.

	    var MobileServiceClient = WindowsAzure.MobileServiceClient,
			client = new MobileServiceClient('AppUrl', 'AppKey'),   		    

  	De esta forma, se crea una nueva instancia de MobileServiceClient que se usa para obtener acceso al servicio móvil.

6. Convierta en comentario las líneas de código siguientes:

		var itemCount = 0;
		var staticItems = [];

	Los datos se almacenarán en el servicio móvil y no en una matriz en memoria.

6. Quite la marca de comentario de la línea de código siguiente:

        todoItemTable = client.getTable('todoitem');

   	Este código crea un objeto proxy (todoItemTable) para la base de datos SQL **TodoItem**.

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
	   

   Esto envía una consulta al servicio móvil que devuelve todos los elementos. Los resultados se iteran y los datos se muestran en la página.

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

##<a name="test-app"></a>Prueba de la aplicación con su servicio móvil nuevo

4. Recargue la dirección URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación.

    > [AZURE.NOTE]Si es necesario reiniciar el servidor web, repita los pasos de la primera sección.

2. Al igual que anteriormente, escriba texto en **Enter new task** y, a continuación, haga clic en **Add**.

   	Esto envía un elemento nuevo como inserción al servicio móvil.

3. En el [Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4. Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.
  
   	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

5. En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Examinar del portal y haga clic en **Actualizar**.

  	Observe que el valor completo ha cambiado de **false** a **true**.

6. En el archivo de proyecto app.js, busque el método **RefreshTodoItems** y reemplace la línea de código que define `query` por la siguiente:

   		var query = todoItemTable.where({ complete: false });

7. Vuelva a cargar la página y marque otro de los elementos de la lista.

   	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización da lugar a un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación HTML para que funcione con datos en Servicios móviles. A continuación, obtenga información acerca de cómo autenticar a los usuarios de su aplicación, pruebe uno de estos otros tutoriales al finalizar [Agregar autenticación a la aplicación]. Si está trabajando con un proyecto de aplicación Cordova o PhoneGap, obtenga más información acerca de las notificaciones de inserción en [Notificaciones de inserción en las aplicaciones Cordova con Microsoft Azure](https://msdn.microsoft.com/magazine/dn879353.aspx).

<!-- Anchors. -->
[Download the HTML app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png

<!-- URLs. -->
[Introducción a los Servicios móviles]: mobile-services-html-get-started.md
[Agregar autenticación a la aplicación]: mobile-services-html-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Portal de administración]: https://manage.windowsazure.com/
[GetStartedWithData app]: http://go.microsoft.com/fwlink/?LinkID=286345
[aplicación GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345

[Mobile Services HTML/JavaScript How-to Conceptual Reference]: mobile-services-html-how-to-use-client-library.md

[Cross-origin resource sharing]: http://msdn.microsoft.com/library/azure/dn155871.aspx

 

<!---HONumber=62-->