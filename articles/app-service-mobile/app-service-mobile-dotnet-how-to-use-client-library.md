<properties
	pageTitle="Biblioteca de cliente administrada de Aplicaciones móviles del Servicio de aplicaciones (Windows | Xamarin) | Microsoft Azure"
	description="Aprenda a usar un cliente .NET para Aplicaciones móviles del Servicio de aplicaciones de Azure con aplicaciones de Windows y Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# Uso del cliente administrado para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Información general

En esta guía se muestra cómo realizar escenarios comunes con la biblioteca de cliente administrada para Aplicaciones móviles del Servicio de aplicaciones de Azure para Windows y Xamarin. Si no tiene experiencia en el uso de Aplicaciones móviles, considere primero la opción de completar el tutorial [Guía de inicio rápido de Aplicaciones móviles de Azure]. En esta guía, nos centramos en el SDK administrado de cliente. Para más información sobre los SDK de servidor de Aplicaciones móviles, consulte la documentación de procedimientos para el [SDK de servidor .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) o el [SDK de servidor Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentación de referencia

La documentación de referencia para el SDK de cliente se encuentra aquí: [Referencia de cliente de .NET para aplicaciones móviles de Azure]. También encontrará varios ejemplos de cliente en el [repositorio de GitHub de ejemplos de Azure].

##<a name="setup"></a>Configuración y requisitos previos

Se supone que ya ha creado y publicado el proyecto de back-end de la aplicación móvil, que incluye al menos una tabla. En el código usado en este tema, el nombre de la tabla es `TodoItem` y dispondrá de las siguientes columnas: `Id`, `Text` y `Complete`. Se trata de la misma tabla que se crea cuando se completa la [Guía de inicio rápido de Aplicaciones móviles de Azure].

El tipo del cliente con tipo correspondiente en C# es el siguiente:

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Tenga en cuenta que [JsonPropertyAttribute] se usa para definir la asignación *PropertyName* entre el tipo de cliente y la tabla.

Para obtener información sobre cómo crear tablas nuevas en el back-end de Aplicaciones móviles, consulte la información de los [Procedimientos del SDK de servidor .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) o los [Procedimientos del SDK de servidor Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-dynamicschema). Si creó el back-end de la Aplicación móvil en el Portal de Azure mediante la guía de inicio rápido, también puede usar la opción **Tablas fáciles** en el [Portal de Azure].

###<a name="symbolsource"></a>Trabajo con símbolos de depuración en Visual Studio

Los símbolos del espacio de nombres Microsoft.Azure.Mobile están disponibles en [SymbolSource]. Consulte las [Instrucciones de SymbolSource] para integrar SymbolSource con Visual Studio.

##<a name="create-client"></a>Creación del cliente de Aplicación móvil

El código siguiente crea el objeto [MobileServiceClient] que se usa para obtener acceso al back-end de la Aplicación móvil.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

En el código anterior, reemplace `MOBILE_APP_URL` por la dirección URL del back-end de la aplicación móvil, que se encuentra en la hoja de la aplicación móvil en el [Portal de Azure]. Es normal y se recomienda que la instancia de cliente sea un singleton.

## Trabajo con tablas

La siguiente sección describe cómo buscar y recuperar registros y modificar los datos de la tabla. Se tratan los siguientes temas:

* [referencia de tabla](#instantiating)
* [Datos de consulta](#querying)
* [Filtro de datos devueltos](#filtering)
* [Ordenar datos devueltos](#sorting)
* [Devolver datos en páginas](#paging)
* [Seleccionar columnas específicas](#selecting)
* [Buscar un registro por identificador](#lookingup)
* [Trabajar con consultas sin tipo](#untypedqueries)
* [Insertar datos](#inserting)
* [Actualizar datos](#updating)
* [Eliminar datos](#deleting)
* [Resolución de conflictos y simultaneidad optimista](#optimisticconcurrency)
* [Enlace a una interfaz de usuario de Windows](#binding)
* [Cambio del tamaño de página](#pagesize)

###<a name="instantiating"></a>Creación de una referencia de tabla

Todo el código que obtiene acceso a datos o los modifica en una tabla de back-end llama a las funciones del objeto `MobileServiceTable`. Obtenga una referencia a la tabla llamando al método [GetTable] en una instancia de `MobileServiceClient` del modo indicado a continuación:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

Este es el modelo de serialización con tipo. También se admite un modelo de serialización sin tipo. El siguiente código [crea una referencia a una tabla sin tipo]\:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

En las consultas sin tipo, debe especificar la cadena de consulta de OData subyacente.

###<a name="querying"></a>Consulta de datos desde la aplicación móvil

En esta sección se describe cómo generar consultas al back-end de la aplicación móvil, lo cual incluye la siguiente funcionalidad:

- [Filtro de datos devueltos]
- [Ordenar datos devueltos]
- [Devolver datos en páginas]
- [Seleccionar columnas específicas]
- [Búsqueda de datos por identificador]

>[AZURE.NOTE] Se aplica el tamaño de página del servidor para evitar que se devuelvan todas las filas. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio. Para devolver más de 50 filas, use el método `Take` como se describe en [Devolución de datos en páginas].

###<a name="filtering"></a>Filtro de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `Where` en una consulta. Devuelve todos los elementos de `todoTable` cuya propiedad `Complete` es igual a `false`. La función [Where] aplica un predicado de filtrado de filas a la consulta en relación con la tabla.

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Puede ver el identificador URI de la solicitud que se ha enviado al back-end mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler]. Si consulta el URI de solicitud siguiente, tenga en cuenta que se ha modificado la cadena de consulta:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

El SDK de servidor traduce esta solicitud de OData a una consulta SQL similar a la siguiente:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

La función que se pasa al método `Where` puede disponer de un número arbitrario de condiciones. Por ejemplo, la siguiente línea:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
	   .ToListAsync();

Será traducida por el SDK de servidor a una consulta SQL similar a la siguiente:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

La instrucción `WHERE` anterior buscará los elementos con el estado `Complete` establecido en false con no nulo `Text`.

Esta consulta también se puede dividir en varias cláusulas:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Los dos métodos son equivalentes y pueden usarse indistintamente. La opción anterior (de concatenación de varios predicados en una consulta) es más compacta y es la que se recomienda.

La cláusula `Where` admite las operaciones que pueden traducirse en el subconjunto OData. Incluye operadores relacionales (==, !=, <, <=, >, >=), operadores aritméticos (+, -, /, *, %), precisión numérica (Math.Floor, Math.Ceiling), funciones de cadena (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propiedades de fecha (Year, Month, Day, Hour, Minute, Second), propiedades de acceso de un objeto y expresiones que combinan todas las opciones anteriores. Al tener en cuenta lo que admite el SDK de servidor, puede consultar la [documentación de OData v3].

###<a name="sorting"></a>Clasificación de datos devueltos

El siguiente código muestra cómo ordenar datos incluyendo una función [OrderBy] o [OrderByDescending] en la consulta. Devuelve los elementos de `todoTable` ordenados de manera ascendente por el campo `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Devolución de datos en páginas

De manera predeterminada, el back-end devuelve solo las primeras 50 filas. Aumente el número de filas devueltas llamando al método [Take]. Use `Take` junto con el método [Skip] para solicitar una "página" específica del conjunto de datos total devuelto por la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

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

También puede usar el método [IncludeTotalCount] para asegurarse de que la consulta obtendrá el recuento total de <i>todos</i> los registros que deberían devolverse, ignorando cualquier cláusula de limitación/paginación especificada:

	query = query.IncludeTotalCount();

Este es un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos `Take` y `Skip`. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores.

>[AZURE.NOTE]Para reemplazar el límite de 50 filas de un back-end de la aplicación móvil, también debe aplicar [EnableQueryAttribute] al método público GET y especificar el comportamiento de paginación. Cuando se aplica al método, lo siguiente establece el máximo de filas devueltas a 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Selección de columnas específicas

Puede especificar qué conjunto de propiedades incluir en los resultados agregando una cláusula [Select] a su consulta. Por ejemplo, el siguiente código muestra cómo seleccionar solo un campo y también cómo seleccionar varios campos y darles formato:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Todas las funciones descritas en adelante son adicionales, por lo que es posible seguir llamándolas y cada vez irá afectando más a la consulta. A continuación se muestra un ejemplo más:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Búsqueda de datos por identificador

La función [LookupAsync] puede usarse para buscar objetos desde la base de datos con un identificador determinado.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Ejecución de consultas sin tipo

Al ejecutar una consulta mediante un objeto de tabla sin tipo, debe especificar explícitamente la cadena de consulta de OData llamando a [ReadAsync], como en el ejemplo siguiente:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vuelva a obtener valores JSON que puede usar como un contenedor de propiedades. Para obtener más información sobre JToken y Newtonsoft Json.NET, consulte el sitio de [Json.NET].

### <a name="inserting"></a>Inserción de datos en el back-end de una aplicación móvil

Todos los tipos de cliente deben incluir un miembro llamado **Id**, que es una cadena de forma predeterminada. Este elemento **Id** es necesario para realizar operaciones CRUD y para trabajar sin conexión. El siguiente código muestra cómo usar el método [InsertAsync] para insertar filas nuevas en una tabla. El parámetro contiene los datos que se van a insertar como un objeto .NET.

	await todoTable.InsertAsync(todoItem);

Si no se incluye un valor de identificador personalizado exclusivo en `todoItem` pasado a la llamada `todoTable.InsertAsync`, el servidor establecido en el objeto `todoItem` devuelto al cliente generará un valor para el identificador.

Para insertar datos sin tipo, puede aprovechar Json.NET como se muestra a continuación.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

A continuación, se muestra un ejemplo en el que se usa una dirección de correo electrónico como un identificador de cadena exclusivo.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

###Trabajar con valores de Id.

El servicio Aplicaciones móviles admite valores de cadena personalizados únicos para la columna **id** de la tabla. Esto permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para el Id.

Los identificadores de cadena proporcionan las siguientes ventajas:

* Se generan identificadores sin realizar una vuelta a la base de datos.
* Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
* Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

Cuando no se establece un valor de identificador de cadena en un registro insertado, el back-end de la aplicación móvil genera un valor único para el identificador. Puede usar el método [Guid.NewGuid] para generar sus propios valores de identificador, ya sea en el cliente o en el back-end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Modificación de datos en el back-end de una aplicación móvil

El siguiente código muestra cómo usar el método [UpdateAsync] para actualizar un registro existente con el mismo identificador con nueva información. El parámetro contiene los datos que se van a actualizar como un objeto .NET.

	await todoTable.UpdateAsync(todoItem);

Para insertar datos sin tipo, puede utilizar [Json.NET] de la siguiente manera:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Debe especificarse un campo `id` al realizar una actualización. Así es cómo el back-end identifica qué instancia actualizar. El campo `id` puede obtenerse a partir del resultado de la llamada `InsertAsync`. Se genera una excepción `ArgumentException` cuando trata de actualizar un elemento sin proporcionar el valor `id`.

###<a name="deleting"></a>Eliminación de datos del back-end de una aplicación móvil

El siguiente código muestra cómo usar el método [DeleteAsync] para eliminar una instancia existente. La instancia se identifica mediante el campo `id` establecido en `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Para eliminar datos sin tipo, puede aprovechar Json.NET de la siguiente manera:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Tenga en cuenta que al realizar una solicitud de eliminación, debe especificarse un identificador. Otras propiedades no se pasan al servicio o se omiten en este. El resultado de una llamada `DeleteAsync` normalmente es `null`. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`. Se produce una excepción `MobileServiceInvalidOperationException` cuando se trata de eliminar un elemento sin especificar el campo `id`.

###<a name="optimisticconcurrency"></a>Uso de la simultaneidad optimista para la resolución de conflictos

Dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. El *control de simultaneidad optimista* asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha.

El servicio Aplicaciones móviles es compatible con el control de simultaneidad optimista gracias al seguimiento de cambios en cada elemento mediante la columna de propiedades del sistema `version` que se definió en cada tabla en el back-end de la aplicación móvil. Cada vez que se actualiza un registro, el servicio Aplicaciones móviles establece la propiedad `version` de ese registro en un nuevo valor. Durante cada solicitud de actualización, la propiedad `\version` del registro incluido con la solicitud se compara con la misma propiedad del registro en el servidor. Si la versión que pasa con la solicitud no coincide con el back-end, la biblioteca de cliente genera una excepción `MobileServicePreconditionFailedException<T>`. El tipo incluido con la excepción es el registro del back-end que contiene la versión del registro del servidor. A continuación, la aplicación puede usar esta información para decidir si ejecutar la solicitud de actualización de nuevo con el valor `version` correcto del back-end para confirmar los cambios.

Define una columna en la clase de tabla para la propiedad del sistema `version` para habilitar la simultaneidad optimista. Por ejemplo:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


Las aplicaciones con tablas sin tipo permiten la simultaneidad optimista mediante el establecimiento de la marca `Version` en `SystemProperties` de la tabla de la siguiente forma.

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Además de habilitar la simultaneidad optimista, se debe detectar la excepción `MobileServicePreconditionFailedException<T>` en el código al llamar a [UpdateAsync]. Resuelva el conflicto aplicando la `version` correcta al registro actualizado y llame a [UpdateAsync] con el registro resuelto. El siguiente código muestra cómo resolver un conflicto de escritura detectado:

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Para obtener más información, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

###<a name="binding"></a>Enlace de datos de Aplicaciones móviles a una interfaz de usuario de Windows

En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario en una aplicación Windows. Puede ejecutar el siguiente código de muestra para enlazar el origen de la lista con una consulta de elementos incompletos en `todoTable` y visualizarlos en una lista muy sencilla. [MobileServiceCollection] crea una colección de enlaces para Aplicaciones móviles.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Algunos controles en tiempo de ejecución administrado admiten una interfaz denominada [ISupportIncrementalLoading]. Esta interfaz permite a los controles solicitar datos adicionales cuando el usuario se desplaza. Las aplicaciones universales de Windows integran compatibilidad para esta interfaz mediante [MobileServiceIncrementalLoadingCollection], que administra automáticamente las llamadas desde los controles. Para usar `MobileServiceIncrementalLoadingCollection` en las aplicaciones de Windows, realice las siguientes acciones:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Para usar la nueva colección en aplicaciones de Windows Phone 8 y "Silverlight", use los métodos de extensión `ToCollection` en `IMobileServiceTableQuery<T>` y `IMobileServiceTable<T>`. Para cargar datos realmente, llame a `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Cuando use la colección creada mediante la llamada a `ToCollectionAsync` o `ToCollection`, obtendrá una colección que puede enlazarse a los controles de la interfaz de usuario. Esta colección es para la paginación, es decir, un control puede solicitar a la colección cargar más elementos y la colección lo hará para el control. En este momento, no hay ningún código de usuario implicado y el control iniciará el flujo. Sin embargo, puesto que la colección está cargando datos desde la red, cabe esperar que a veces se produzca un error en la carga. Para gestionar esos errores, puede reemplazar el método `OnException` de `MobileServiceIncrementalLoadingCollection` para gestionar excepciones resultantes de las llamadas a `LoadMoreItemsAsync` que han realizado los controles.

Para finalizar, imagine que la tabla contiene muchos campos, pero solo desea que se muestren algunos en el control. Puede usar la guía de la sección "[Seleccionar columnas específicas](#selecting)" anterior para seleccionar las columnas concretas que se mostrarán en la interfaz de usuario.

###<a name="pagesize"></a>Cambio del tamaño de página

Aplicaciones móviles de Azure devuelve un máximo de 50 elementos por cada solicitud de forma predeterminada. Puede cambiar esto aumentando el tamaño máximo de página en el servidor y el tamaño de página solicitado en el lado cliente. Para aumentar el tamaño de página solicitado, use una sobrecarga de `PullAsync` que permite especificar `PullOptions`:

    PullOptions pullOptions = new PullOptions
		{
			MaxPageSize = 100
		};

Suponiendo que ha establecido el valor de PageSize igual o mayor que 100 en el servidor, se devolverán hasta 100 elementos en cada solicitud.

##<a name="#customapi"></a>Trabajo con una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

Puede llamar a una API personalizada al llamar a una de las sobrecargas del método [InvokeApiAsync] en el cliente. Por ejemplo, la siguiente línea de código envía una solicitud POST a la API **completeAll** en el back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Tenga en cuenta que se trata de una llamada de método con tipo, lo que requiere la definición del tipo de valor devuelto **MarkAllResult**. Se admiten métodos con y sin tipos.


##<a name="authentication"></a>Autenticación de usuarios

Aplicaciones móviles es compatible con la autenticación y la autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externas: Facebook, Google, cuenta de Microsoft, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación].

Se admiten dos flujos de autenticación: un _server flow_ y un _client flow_. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

En ambos casos, debe registrar la aplicación con el proveedor de identidades. El proveedor de identidades le proporcionará un identificador de cliente y un secreto de cliente. A continuación, debe configurar la autenticación y la autorización del Servicio de aplicaciones de Azure con el identificador de cliente y el secreto de cliente proporcionados por el proveedor de identidades. Para obtener más información, siga las instrucciones detalladas del tutorial [Incorporación de la autenticación a la aplicación de Windows].

###<a name="serverflow"></a>Flujo de servidor
Una vez registrado el proveedor de identidades, llame al método MobileServiceClient.\[LoginAsync] con el valor [MobileServiceAuthenticationProvider] del proveedor. Por ejemplo, el siguiente código activa un inicio de sesión de flujo de servidor mediante Facebook.

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

En un flujo de servidor, el Servicio de aplicaciones de Azure administra el flujo de autenticación de OAuth 2.0. Para ello, muestra la página de inicio de sesión del proveedor seleccionado y genera un token de autenticación del Servicio de aplicaciones después de que se realice un inicio de sesión correcto con el proveedor de identidades. El [método LoginAsync] devuelve [MobileServiceUser], que proporciona el valor [UserId] del usuario autenticado y el valor [MobileServiceAuthenticationToken] como un token de web JSON (JWT). El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación](#caching).

###<a name="client-flow"></a>Flujo de cliente

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y proporcionar el token devuelto al Servicio de aplicaciones para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

####Inicio de sesión único mediante un token de Facebook o Google

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
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = string.Format("You are now logged in - {0}", user.UserId);
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

####Inicio de sesión único mediante Cuenta Microsoft con el SDK de Live

Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de la cuenta de Microsoft. A continuación, debe conectar este registro con el back-end de Aplicaciones móviles. Complete los pasos de [Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de la cuenta Microsoft] con el fin de crear un registro de cuenta Microsoft y conectarlo al back-end de la aplicación móvil. Si dispone de ambas versiones de la aplicación, Tienda Windows y Windows Phone 8/Silverlight, registre primero la versión de Tienda Windows.

El siguiente código se autentica mediante el SDK de Live y usa el token devuelto para iniciar sesión en el back-end de Aplicaciones móviles.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Consulte la documentación para obtener más información sobre el [SDK de Windows Live].

###<a name="caching"></a>Almacenamiento en caché del token de autenticación
En algunos casos, la llamada al método de inicio de sesión puede evitarse después de la primera vez que el usuario se autentique. Puede usar [PasswordVault] en las aplicaciones de la Tienda Windows para almacenar en caché la identidad del usuario actual la primera vez que se inicie sesión en ellas y las veces posteriores que compruebe si ya dispone de la identidad de usuario en la memoria caché. Si la memoria caché está vacía, tendrá que enviar el usuario a través del proceso de inicio de sesión.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
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
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


En las aplicaciones de Windows Phone, puede almacenar en caché los datos y cifrarlos mediante la clase [ProtectedData], y almacenar información confidencial en un almacén aislado.

-->

### <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. Con frecuencia, esta opción es preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia UX más nativa y permite personalizaciones adicionales.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory]. Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa.

2. En Visual Studio o Xamarin Studio, abra el proyecto y agregue una referencia al paquete NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Al buscar, incluya las versiones preliminares.

3. Agregue el siguiente código a la aplicación, según la plataforma que utilice. En cada caso, realice las sustituciones siguientes:

* Reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de Azure clásico].

* Reemplace **INSERT-RESOURCE-ID-HERE** por el id. de cliente del back-end de la aplicación móvil. Puede obtenerlo en la pestaña **Avanzadas**, que se encuentra en la opción **Configuración de Azure Active Directory** del portal.

* Reemplace **INSERT-CLIENT-ID-HERE** por el id. de cliente que copió de la aplicación cliente nativa.

* Reemplace **INSERT-REDIRECT-URI-HERE** por el punto de conexión _/.auth/login/done_ del sitio, mediante el esquema HTTPS. Este valor debería ser similar a \__https://contoso.azurewebsites.net/.auth/login/done_.

El código necesario para cada plataforma es el siguiente:

**Windows:**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
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

**Xamarin.iOS**

    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }

**Xamarin.Android**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }

##<a name="pushnotifications">Notificaciones de inserción

Los siguientes temas tratan sobre las notificaciones push:

* [Registro de notificaciones push](#register-for-push)
* [Obtención del SID de un paquete de la Tienda Windows](#package-sid)
* [Registro con plantillas multiplataforma](#register-xplat)

###<a name="register-for-push"></a>Cómo registrarse para recibir notificaciones push

El cliente de Aplicaciones móviles permite registrar las notificaciones push con Centros de notificaciones de Azure. Al registrar, se obtiene un identificador del servicio de notificaciones push (PNS) específico de la plataforma. A continuación, proporcione este valor junto con las etiquetas cuando se cree el registro. El código siguiente registra la aplicación de Windows para las notificaciones push en el Servicio de notificaciones de Windows.(WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Si va a insertar en WNS, debe obtener el SID de un paquete de la Tienda Windows (ver a continuación). Tenga en cuenta que en este ejemplo se incluyen dos etiquetas en el registro. Para más información sobre las aplicaciones de Windows, incluyendo cómo registrarse para los registros de plantillas, vea [Agregar notificaciones de inserción a la aplicación].

Tenga en cuenta que no se admite la solicitud de etiquetas del cliente. Las solicitudes de etiquetas se quitan del registro en modo silencioso. Si desea registrar el dispositivo con etiquetas, crear una API personalizada que use la API de los Centros de notificaciones para realizar el registro en su nombre. [Llame a la API personalizada](#customapi) en lugar de al método `RegisterNativeAsync()`.

###<a name="package-sid"></a>Obtención del SID de un paquete de la Tienda Windows.

Se necesita un SID de paquete para habilitar las notificaciones push en aplicaciones de la Tienda Windows. El SID de paquete también se usa para otras operaciones (como el inicio de sesión único de Windows). Debe registrar la aplicación en la Tienda Windows para recibir un SID de paquete.

Para obtener este valor:

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación de la Tienda Windows y luego haga clic en **Tienda** > **Asociar aplicación con la Tienda...**.
2. En el asistente, haga clic en **Siguiente**, inicie sesión con su cuenta Microsoft, escriba un nombre para la aplicación en **Reserve un nuevo nombre de aplicación** y luego haga clic en **Reservar**.
3. Después de crear correctamente el registro de la aplicación, seleccione el nuevo nombre de la aplicación, haga clic en **Siguiente** y después en **Asociar**. Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.
4. Inicie sesión en el [Centro de desarrollo de Windows] con su cuenta Microsoft. En **Mis aplicaciones**, haga clic en el registro de la aplicación que acaba de crear.
5. Haga clic en **Administración de aplicaciones** > **Identidad de aplicación**, y después desplácese hacia abajo hasta encontrar el **SID del paquete**.

Muchos usos del SID del paquete lo tratan como un URI, en cuyo caso debe usar _ms-app://_ como el esquema. Tome nota de la versión del SID del paquete que se forma concatenando este valor como prefijo.

Las aplicaciones de Xamarin requieren código adicional para poder registrar una aplicación que se ejecute en la aplicación iOS o Android con el Servicio de notificaciones push de Apple (APNS) y el Servicio de mensajería en la nube de Google (GCM), respectivamente. Para más información, consulte el tema sobre su plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Procedimiento: Registro de plantillas push para enviar notificaciones entre plataformas

Para registrar plantillas, use el método `RegisterAsync()` con ellas tal y como se indica a continuación:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Las plantillas serán de tipo JObject y pueden contener varias plantillas con el formato JSON siguiente:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

El método **RegisterAsync()** también acepta iconos secundarios:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Tenga en cuenta que se eliminará todas las etiquetas por seguridad. Para agregar etiquetas a las instalaciones o las plantillas dentro de las instalaciones, vea [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure].

Para enviar notificaciones mediante estas plantillas registradas, consulte las [API de Centros de notificaciones].

##<a name="misc"></a>Temas variados

###<a name="errors"></a>Gestión de errores

Cuando se produce un error en el back-end, el SDK de cliente provocará una excepción `MobileServiceInvalidOperationException`. En el ejemplo siguiente se muestra cómo controlar una excepción devuelta por el back-end:

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
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

Encontrará otro ejemplo sobre cómo trabajar con condiciones de error en esta página de [ejemplos de archivos de aplicaciones móviles]. Aquí, el ejemplo [LoggingHandler] proporciona un controlador delegado de registro (ver a continuación) para registrar las solicitudes realizadas al back-end. Esto proporciona una manera más fácil de depurar aplicaciones de Xamarin, en lugar de depender de Fiddler.

###<a name="headers"></a>Personalización de encabezados de solicitud

Para admitir su escenario de aplicación específico, deberá personalizar la comunicación con el back-end de la aplicación móvil. Por ejemplo, es posible que desee agregar un encabezado personalizado a cada solicitud saliente o cambiar los códigos de estado de las respuestas. Para ello, proporcione un valor [DelegatingHandler] personalizado, como en el ejemplo siguiente:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[Filtro de datos devueltos]: #filtering
[Ordenar datos devueltos]: #sorting
[Devolución de datos en páginas]: #paging
[Devolver datos en páginas]: #paging
[Seleccionar columnas específicas]: #selecting
[Búsqueda de datos por identificador]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[Guía de inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-windows-store-dotnet-get-started.md
[Incorporación de la autenticación a la aplicación de Windows]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Incorporación de la autenticación a su aplicación]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md
[Agregar notificaciones de inserción a la aplicación]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de la cuenta Microsoft]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[Referencia de cliente de .NET para aplicaciones móviles de Azure]: https://msdn.microsoft.com/es-ES/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/es-ES/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/es-ES/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/es-ES/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/es-ES/library/azure/jj554275(v=azure.10).aspx
[crea una referencia a una tabla sin tipo]: https://msdn.microsoft.com/es-ES/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/es-ES/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/es-ES/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/es-ES/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/es-ES/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/es-ES/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/es-ES/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/es-ES/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/es-ES/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/es-ES/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/es-ES/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/es-ES/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/es-ES/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/es-ES/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/es-ES/library/azure/dn250579(v=azure.10).aspx
[Portal de Azure]: https://portal.azure.com/
[Portal de Azure clásico]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/es-ES/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro de desarrollo de Windows]: https://dev.windows.com/es-ES/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[SDK de Windows Live]: https://msdn.microsoft.com/es-ES/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API de Centros de notificaciones]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[ejemplos de archivos de aplicaciones móviles]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[repositorio de GitHub de ejemplos de Azure]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[documentación de OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[Instrucciones de SymbolSource]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0525_2016-->