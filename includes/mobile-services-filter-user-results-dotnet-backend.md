

Ahora que la autenticación es necesaria para obtener acceso a los datos en la tabla TodoItem, puede usar el valor userID asignado por Servicios móviles para filtrar los datos devueltos.

>[WACOM.NOTE]Los métodos siguientes deben tener el atributo **AuthorizeLevel** aplicado al **nivel de autorización** del **usuario**. De esta forma, se restringe el acceso a la tabla solo a usuarios autenticados.

1. En Visual Studio 2013, abra el proyecto de servicio móvil, expanda la carpeta DataObjects y, a continuación, abra el archivo de proyecto TodoItem.cs.

	La clase TodoItem define el objeto de datos y tiene que agregar una propiedad UserId que usar para el filtrado.

2. Agregue la nueva propiedad UserId siguiente a la clase **TodoItem**:

		public string UserId { get; set; }

	>[WACOM.NOTE] Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear siempre que detecte un cambio del modelo de datos en la definición del modelo de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe utilizar Migraciones de Code First. El inicializador predeterminado no se puede usar con una base de datos SQL en Azure. Para obtener más información, consulte [Uso de Migraciones de Code First para actualizar el modelo de datos](/en-us/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations).

3. En el Explorador de soluciones, expanda la carpeta Controladores, abra el archivo de proyecto TodoItemController.cs y agregue la siguiente instrucción **using**:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

	La clase **TodoItemController** implementa el acceso a los datos para la tabla TodoItem. 
 
4. Busque el método **PostTodoItem** y agregue el código siguiente al principio del método:

		// Get the logged-in user.
	    var currentUser = User as ServiceUser;
	
	    // Set the user ID on the item.
	    item.UserId = currentUser.Id;

    Este código agrega un valor UserId al elemento, que es el identificador de usuario del usuario autenticado, antes de que se inserte en la tabla TodoItem. 
	

5. Busque el método **GetAllTodoItems** y reemplace la instrucción **return** siguiente con esta línea de código:

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

   	Esta consulta filtra los objetos TodoItem devueltos de manera que cada usuario solo recibe los elementos que inserte. 

6. Vuelva a publicar el proyecto de servicio móvil en Azure.

<!--HONumber=35_1-->
