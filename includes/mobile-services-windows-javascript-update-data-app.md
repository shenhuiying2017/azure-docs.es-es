

1. En el archivo de script default.js que hay debajo de la línea de código que define la lista todoItems, agregue la siguiente definición de función:
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	Esto define una función de filtro que agrega el encabezado  `If-Modified-Since` para que no se almacene nada en caché en el cliente.
 
2. Luego, quite la marca de comentario o agregue la siguiente línea de código y reemplace `<yourClient>` por la variable agregada al archivo service.js cuando conectó su proyecto al servicio móvil.

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	This code creates a proxy object (**todoTable**) for the new database table, using the caching filter. 

3. Replace the **InsertTodoItem** function with the following code:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Este código inserta un elemento nuevo en la tabla.

3. Reemplace la función **RefreshTodoItems** por el siguiente código:

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Esto define el enlace a la colección de elementos que hay en todoTable, que contiene todos los objetos **TodoItem** devueltos por el servicio móvil. 

4. Reemplace la función **UpdateCheckedTodoItem** por el siguiente código:
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.


<!--HONumber=42-->
