<properties 
	pageTitle="Uso del cliente de componente Xamarin - Guía de características de los Servicios móviles de Azure" 
	description="Obtenga información para usar el cliente del componente Xamarin para Servicios móviles de Azure." 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="lindydonna"/>

# Uso del cliente del componente Xamarin para servicios móviles de Azure
[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Esta guía le muestra cómo realizar tareas comunes con el cliente del componente Xamarin para Servicios móviles de Azure, en aplicaciones Xamarin para iOS y Android. Entre las tareas incluidas se encuentran la consulta, inserción, actualización y eliminación de datos, la autenticación de usuarios y la administración de errores. Si no tiene experiencia con Servicios móviles, primero debería considerar la posibilidad de realizar los tutoriales “Inicio rápido de Servicios móviles” ([Xamarin.iOS][Xamarin.iOS quickstart tutorial]/[Xamarin.Android][Xamarin.Android quickstart tutorial]) e “Introducción a los datos en .NET” ([Xamarin.iOS][Xamarin.iOS data tutorial]/[Xamarin.Android][Xamarin.Android data tutorial]). El tutorial de inicio rápido requiere [Xamarin][Xamarin download] y el [SDK de Servicios móviles], y le ayuda a configurar su cuenta y a crear su primer servicio móvil.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

## <a name="setup"></a>Configuración y requisitos previos

Se asume que ha creado un servicio móvil y una tabla. Para obtener más información, consulte [Crear una tabla](http://go.microsoft.com/fwlink/?LinkId=298592). En el código usado en este tema, el nombre de la tabla es `TodoItem` y dispondrá de las siguientes columnas: `id`, `Text` y `Complete`.

El tipo .NET del cliente con tipo correspondiente es el siguiente:


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
Cuando está habilitado el esquema dinámico, Servicios móviles de Azure genera automáticamente columnas nuevas basadas en el objeto en las solicitudes de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://go.microsoft.com/fwlink/?LinkId=296271).

## <a name="create-client"></a>Creación del cliente de Servicios móviles

El código siguiente crea el objeto `MobileServiceClient` que se usa para obtener acceso al servicio móvil.
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

En el código anterior, reemplace `AppUrl` y `AppKey` por la URL y la clave de aplicación del servicio móvil, en ese orden. Estos datos están disponibles en el Portal de administración de Azure si selecciona el servicio móvil y, a continuación, hace clic en Panel.

## <a name="instantiating"></a>Creación de una referencia de tabla

Todo el código que obtiene acceso o modifica los datos de la tabla de Servicios móviles llama a las funciones del objeto `MobileServiceTable`. Obtenga una referencia a la tabla llamando a la función [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) en una instancia de `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

Se trata del modelo de serialización con tipo; consulte a continuación la descripción del <a href="#untyped">modelo de serialización sin tipo</a>.
			
## <a name="querying"></a>Consulta de datos desde un servicio móvil 

En esta sección se describe cómo generar consultas al servicio móvil. Los subapartados describen distintos aspectos, como la ordenación, el filtrado y la paginación.
			
### <a name="filtering"></a>Filtro de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `Where` en una consulta. Devuelve todos los elementos de `todoTable` cuya propiedad `Complete` es igual a `false`. La función `Where` aplica un predicado de filtrado de filas a la consulta en la tabla.
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Puede ver el URI de la solicitud que se ha enviado al servicio móvil mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o Fiddler. Si consulta el URI de solicitud siguiente, tenga en cuenta que se está modificando la propia cadena de consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
Normalmente, esta solicitud se traduciría aproximadamente en la siguiente consulta SQL en el servidor:
			
	SELECT * 
	FROM TodoItem 			
	WHERE ISNULL(complete, 0) = 0
			
La función que se pasa al método `Where` puede disponer de un número arbitrario de condiciones. Por ejemplo, la siguiente línea:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Se traduciría aproximadamente (para la misma solicitud mostrada anteriormente) de la siguiente forma:
			
	SELECT * 
	FROM TodoItem 
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

La instrucción `where` anterior buscará los elementos con el estado `Complete` establecido en false con no nulo `Text`.

También podría haberse escrito en varias líneas:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Los dos métodos son equivalentes y pueden usarse indistintamente. La opción anterior, de concatenación de varios predicados en una consulta, es más compacta y es la que se recomienda.

La cláusula `where` es compatible con las operaciones que pueden traducirse en el subconjunto OData de Servicios móviles. Incluye operadores relacionales (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisión numérica (Math.Floor, Math.Ceiling), funciones de cadena (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propiedades de fecha (Year, Month, Day, Hour, Minute, Second), propiedades de acceso de un objeto y expresiones que combinan todas las opciones anteriores.

### <a name="sorting"></a>Clasificación de datos devueltos

El siguiente código muestra cómo ordenar datos incluyendo una función `OrderBy` u `OrderByDescending` en la consulta. Devuelve los elementos de `todoTable` ordenados de manera ascendente por el campo `Text`. De forma predeterminada, el servidor devuelve solo los primeros 50 elementos.

> [AZURE.NOTE]se utiliza el tamaño de página del servidor de forma predeterminada para evitar que se devuelvan todos los elementos. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio.

Puede aumentar el número de elementos que se devuelven si llama a `Take`, tal como se describe en la sección siguiente.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>Devolución de datos en páginas

El siguiente código muestra cómo implementar la paginación en los datos devueltos mediante las cláusulas `Take` y `Skip` en la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();

La siguiente consulta revisada omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();
			
También puede usar el método [IncludeTotalCount](http://msdn.microsoft.com/library/windowsazure/jj730933.aspx) para asegurarse de que la consulta obtendrá el recuento total de <i>todos</i> los registros que deberían devolverse, ignorando cualquier cláusula de limitación/paginación especificada:

	query = query.IncludeTotalCount();

Este es un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos `Take` y `Skip`. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores.

### <a name="selecting"></a>Selección de columnas específicas

Puede especificar qué conjunto de propiedades se incluirá en los resultados agregando una cláusula `Select` a su consulta. Por ejemplo, el siguiente código muestra cómo seleccionar solo un campo y también cómo seleccionar varios campos y darles formato:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();
	
	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();
			
Todas las funciones descritas en adelante son adicionales, por lo que es posible seguir llamándolas y cada vez irá afectando más a la consulta. A continuación se muestra un ejemplo más:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();
	
### <a name="lookingup"></a>Búsqueda de datos por identificador

La función `LookupAsync` puede usarse para buscar objetos desde la base de datos con un identificador determinado.

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

## <a name="inserting"></a>Inserción de datos en un servicio móvil

> [AZURE.NOTE]Si desea realizar operaciones de inserción, búsqueda, eliminación o actualización en un tipo, tiene que crear un miembro denominado **Id** (independientemente del caso). Este es el motivo por el que la clase de ejemplo **TodoItem** cuenta con un miembro de nombre **Id**. No se debe definir un valor de identificador en un valor distinto al predeterminado durante las operaciones de inserción; por el contrario, el valor de identificador siempre se debe definir en un valor no predeterminado y debe existir en las operaciones de actualización y eliminación.

El siguiente código muestra cómo insertar filas nuevas en una tabla. El parámetro contiene los datos que se van a insertar como un objeto .NET.

	await todoTable.InsertAsync(todoItem);

Cuando se devuelve la llamada de `todoTable.InsertAsync` de espera, el identificador del objeto en el servidor se rellena en el objeto `todoItem` en el cliente.

Para insertar datos sin tipo, puede aprovechar Json.NET como se muestra a continuación. Vuelva a observar que no se debe especificar un identificador cuando se inserta un objeto.

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Si intenta insertar un elemento con el campo "Id" ya establecido, recibirá una `MobileServiceInvalidOperationException` del servicio.

## <a name="modifying"></a>Modificación de datos en un servicio móvil

El siguiente código muestra cómo actualizar una instancia existente con el mismo identificador con nueva información. El parámetro contiene los datos que se van a actualizar como un objeto .NET.

	await todoTable.UpdateAsync(todoItem);


Para insertar datos sin tipo, puede aprovechar Json.NET. Tenga en cuenta que cuando realice una actualización, debe especificarse un identificador, ya que de esa forma el servicio móvil identifica qué instancia actualizar. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
Si intenta actualizar un elemento sin el campo "Id" ya establecido, no hay forma de que el servicio indique qué instancia se debe actualizar, por lo que recibirá una `MobileServiceInvalidOperationException` del servicio. De forma parecida, si intenta actualizar un elemento sin tipo sin el campo "Id" ya establecido, recibirá una `MobileServiceInvalidOperationException` del servicio.
			
			
## <a name="deleting"></a>Eliminación de datos en un servicio móvil

El siguiente código muestra cómo eliminar una instancia existente. La instancia se identifica mediante el campo "Id" establecido en `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para eliminar datos sin tipo, puede aprovechar Json.NET. Tenga en cuenta que cuando realice una solicitud de eliminación, debe especificarse un identificador, ya que de esa forma el servicio móvil identifica qué instancia eliminar. Una solicitud de eliminación precisa solo el identificador; las demás propiedades no se pasan al servicio, y si se pasa alguna, se ignoran en el servicio. El resultado de una llamada `DeleteAsync` es normalmente `null` también. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
Si intenta eliminar un elemento sin el campo "Id" ya establecido, no hay forma de que el servicio indique qué instancia eliminar, por lo que volverá a obtener `MobileServiceInvalidOperationException` del servicio. De forma parecida, si intenta eliminar un elemento sin tipo sin el campo "Id" ya establecido, recibirá una `MobileServiceInvalidOperationException` del servicio.
		


## <a name="authentication"></a>Autenticación de usuarios

Servicios móviles es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externas: Facebook, Google, Microsoft Account, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Si desea obtener más información, consulte el tutorial "Introducción a la autenticación" ([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication]).

Se admiten dos flujos de autenticación: un _server flow_ y un _client flow_. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

### Flujo de servidor
Para que Servicios móviles administre el proceso de autenticación en la aplicación de la Tienda Windows o Windows Phone, debe registrar la aplicación con el proveedor de identidades. A continuación, en el servicio móvil, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Si desea obtener más información, consulte el tutorial "Introducción a la autenticación" ([Xamarin.iOS][Xamarin.iOS authentication]/[Xamarin.Android][Xamarin.Android authentication]).

Una vez que haya registrado el proveedor de identidades, simplemente llame al método [LoginAsync] con el valor [MobileServiceAuthenticationProvider] del proveedor. Por ejemplo, el siguiente código activa un inicio de sesión de flujo de servidor mediante Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de [MobileServiceAuthenticationProvider] anterior por el valor de su proveedor.

En este caso, Servicios móviles administra el flujo de autenticación de OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación de Servicios móviles después de que se realice un inicio de sesión correcto con el proveedor de identidades. El [método LoginAsync] devuelve [MobileServiceUser], que proporciona [userId] del usuario autenticado y [MobileServiceAuthenticationToken] como un token de web JSON (JWT). El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación].

### Flujo de cliente

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y, a continuación, proporcionar el token devuelto a Servicios móviles para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

En la forma más simplificada, puede usar el flujo de cliente como se muestra en este fragmento para Facebook o Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained 
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");
			
	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook 
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

### <a name="caching"></a>Almacenamiento en caché del token de autenticación
En algunos casos, la llamada al método de inicio de sesión puede evitarse después de la primera vez que el usuario se autentique. Puede usar un almacén local seguro (como [Xamarin.Auth][Xamarin.Auth component]) para almacenar en caché la identidad del usuario actual la primera vez que inicie sesión y, las veces siguientes, compruebe que la identidad del usuario ya se encuentra en la memoria caché. Si la memoria caché está vacía, tendrá que enviar el usuario a través del proceso de inicio de sesión.

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}
			
	 // Log out
	client.Logout();
	accountStore.Delete(account, "Facebook");


## <a name="errors"></a>Gestión de errores

Existen varias formas de detectar, validar y solucionar errores en Servicios móviles.

Por ejemplo, los scripts de servidor se registran en un servicio móvil y pueden usarse para realizar una amplia variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. Imagine que define y registra un script de servidor que valida y modifica datos:

	function insert(item, user, request) 
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Este script del servidor valida la longitud de los datos de cadena enviados al servicio móvil y rechaza las cadenas que son muy largas, en este caso superiores a 10 caracteres.

Ahora que el servicio móvil está validando datos y enviando respuestas de error en el servidor, puede actualizar la aplicación .NET para poder gestionar las respuestas de error a partir de la validación.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

## <a name="untyped"></a>Trabajo con datos sin tipo

El cliente del componente Xamarin se ha creado para escenarios fuertemente tipados. Sin embargo, es recomendable una experiencia menos tipada; por ejemplo, cuando se trata con objetos con un esquema abierto. El escenario se habilita de la forma siguiente. En las consultas, prescinda de LINQ y use el formato.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vuelva a obtener valores JSON que puede usar como un contenedor de propiedades. Para obtener más información sobre JToken y [Json.NET](http://json.codeplex.com/), consulte Json.NET.

## <a name="unit-testing"></a>Diseño de pruebas unitarias

El valor devuelto por `MobileServiceClient.GetTable` y las consultas son interfaces. Esto hace que "puedan simularse" fácilmente con fines de prueba, por lo que se puede crear una `MyMockTable : IMobileServiceTable<TodoItem>` que implemente la lógica de prueba.

## <a name="nextsteps"></a>Pasos siguientes

Ahora que ha completado este tema de referencia conceptual, conozca cómo realizar tareas importantes en Servicios móviles de forma detallada:

* Introducción a los Servicios móviles ([Xamarin.iOS][Get started with Mobile Services iOS]/[Xamarin.Android][Get started with Mobile Services Android]) <br/>Conozca los aspectos básicos sobre cómo usar Servicios móviles.

* Introducción a los datos ([Xamarin.iOS][Get started with data iOS]/[Xamarin.Android][Get started with data Android]) <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* Introducción a la autenticación ([Xamarin.iOS][Get started with authentication iOS]/[Xamarin.Android][Get started with authentication Android]) <br/>Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

* Validación y modificación de datos con scripts ([Xamarin.iOS][Validate and modify data with scripts ios]/[Xamarin.Android][Validate and modify data with scripts android]) <br/>Obtenga más información sobre uso de scripts de servidor en Servicios móviles para validar y cambiar los datos enviados desde su aplicación.

* Limitación de consultas con paginación ([Xamarin.iOS][Refine queries with paging iOS]/[Xamarin.Android][Refine queries with paging Android]) <br/>Aprenda a usar la paginación en consultas para controlar la cantidad de datos que se gestionan en una única solicitud.

* Autorización de usuarios con scripts ([Xamarin.iOS][Authorize users with scripts iOS]/[Xamarin.Android][Authorize users with scripts Android]) <br/>Aprenda a usar el valor de identificador de usuario proporcionado por Servicios móviles según el usuario autenticado para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Almacenamiento en caché del token de autenticación]: #caching

<!-- URLs. -->
[Get started with Mobile Services iOS]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with Mobile Services Android]: /develop/mobile/tutorials/get-started-xamarin-android
[Xamarin download]: http://xamarin.com/download/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.iOS quickstart tutorial]: /develop/mobile/tutorials/get-started-xamarin-ios/
[Xamarin.Android quickstart tutorial]: /develop/mobile/tutorials/get-started-xamarin-android/
[Xamarin.iOS data tutorial]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Xamarin.Android data tutorial]: /develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Xamarin.iOS authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Xamarin.Android authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-android/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.Auth component]: https://components.xamarin.com/view/xamarin.auth

[SDK de Servicios móviles]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Get started with data iOS]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with data Android]: /develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication iOS]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with authentication Android]: /develop/mobile/tutorials/get-started-with-users-xamarin-android
[Validate and modify data with scripts ios]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Validate and modify data with scripts android]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Refine queries with paging iOS]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Refine queries with paging Android]: /develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Authorize users with scripts iOS]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Authorize users with scripts Android]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[método LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
 

<!---HONumber=July15_HO4-->