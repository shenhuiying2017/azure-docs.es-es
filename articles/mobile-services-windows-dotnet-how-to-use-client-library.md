<properties linkid="obile-services-how-to-dotnet-client" urlDisplayName=".NET Client Library" pageTitle="Working with the Mobile Services .NET Client Library" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to use an .NET client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

Uso de un cliente .NET para Servicios móviles de Azure
======================================================

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/en-us/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework</a>
  	<a href="/en-us/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/en-us/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

Esta guía le muestra cómo realizar algunas tareas comunes a través del cliente .NET de Servicios móviles de Azure, en las aplicaciones de la Tienda Windows y las aplicaciones de Windows Phone. Entre las tareas incluidas se encuentran la consulta, inserción, actualización y eliminación de datos, la autenticación de usuarios y la administración de errores. Si no tiene experiencia en Servicios móviles, intente primero completar el tutorial de inicio rápido de Servicios móviles ([Tutorial de inicio rápido de Servicios móviles](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started/)/[Tutorial de inicio rápido de Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-wp8/)) y el tutorial de inicio rápido con datos en .NET ([Tutorial de datos de la Tienda Windows](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)/[Tutorial de datos de Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-wp8/)). El tutorial de inicio rápido requiere el [SDK de Servicios móviles](http://nuget.org/packages/WindowsAzure.MobileServices/) y le ayuda a configurar su cuenta y crear su primer servicio móvil.

Tabla de contenido
------------------

-   [Qué es Servicios móviles](#what-is)
-   [Conceptos](#concepts)
-   [Creación del cliente de Servicios móviles](#create-client)
-   [Creación de una referencia de tabla](#instantiating)
-   [Consulta de datos desde un servicio móvil](#querying)
    -   [Filtrado de datos devueltos](#filtering)
    -   [Ordenación de datos devueltos](#sorting)
    -   [Devolución de datos en páginas](#paging)
    -   [Selección de columnas específicas](#selecting)
    -   [Búsqueda de datos por identificador](#lookingup)
-   [Inserción de datos en un servicio móvil](#inserting)
-   [Modificación de datos en un servicio móvil](#modifying)
-   [Eliminación de datos en un servicio móvil](#deleting)
-   [Uso de simultaneidad optimista](#optimisticconcurrency)
-   [Enlace de datos a la interfaz de usuario en un servicio móvil](#binding)
-   [Autenticación de usuarios](#authentication)
-   [Gestión de errores](#errors)
-   [Uso de datos sin tipo](#untyped)
-   [Diseńo de pruebas unitarias](#unit-testing)
-   [Personalización del cliente](#customizing)
    -   [Personalización de encabezados de solicitud](#headers)
    -   [Personalización de serialización](#serialization)
-   [Pasos siguientes](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<a name="setup"></a>
ConfiguraciónConfiguración y requisitos previos
-----------------------------------------------

Se asume que ha creado un servicio móvil y una tabla. Para obtener más información, consulte [Crear una tabla](http://go.microsoft.com/fwlink/?LinkId=298592). En el código usado en este tema, el nombre de la tabla es `TodoItem` y dispondrá de las siguientes columnas: `Id`, `Text` y `Complete`.

El tipo .NET del cliente con tipo correspondiente es el siguiente:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

Cuando está habilitado el esquema dinámico, Servicios móviles de Azure genera automáticamente columnas nuevas basadas en el objeto en las solicitudes de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://go.microsoft.com/fwlink/?LinkId=296271).

<a name="create-client"></a>
Creación del cliente de Servicios móvilesCreación del cliente de Servicios móviles
----------------------------------------------------------------------------------

El código siguiente crea el objeto `MobileServiceClient` que se usa para obtener acceso al servicio móvil.

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

En el código anterior, reemplace `AppUrl` y `AppKey` por la dirección URL y la clave de aplicación del servicio móvil, en ese orden. Estos datos están disponibles en el Portal de administración de Azure si selecciona el servicio móvil y, a continuación, hace clic en Panel.

<a name="instantiating"></a>
Creación de una referencia de tablaCreación de una referencia de tabla
----------------------------------------------------------------------

Todo el código que obtiene acceso o modifica los datos de la tabla de Servicios móviles llama a las funciones del objeto `MobileServiceTable`. Obtenga una referencia a la tabla llamando a la función [GetTable](http://msdn.microsoft.com/en-us/library/windowsazure/jj554275.aspx) en una instancia de `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

Este es el modelo de serialización con tipo; consulte la descripción del [modelo de serialización sin tipo](#untyped) a continuación.

<a name="querying"></a>
Consulta de datosConsulta de datos desde un servicio móvil
----------------------------------------------------------

En esta sección se describe cómo generar consultas al servicio móvil. Los subapartados describen distintos aspectos, como la ordenación, el filtrado o la paginación.

> [WACOM.NOTE] Se utiliza el tamańo de página del servidor de forma predeterminada para evitar que se devuelvan todas las filas. De esta forma, se evita que las consultas predeterminadas de los conjuntos de datos de gran tamańo incidan negativamente en el servicio. Para devolver más de 50 filas, use el método `Take` como se describe en [Devolución de datos en páginas](#paging).

<a name="filtering"></a>
### Filtrado de datos devueltos

El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `Where` en una consulta. Devuelve todos los elementos de `todoTable` cuya propiedad `Complete` es igual a `false`. La función `Where` aplica un predicado de filtrado de filas a la consulta en relación con la tabla.

    // Esta consulta filtra TodoItems completados y 
    // los elementos sin una marca de tiempo. 
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

    // Esta consulta filtra TodoItems completado cuando Text no es nulo.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false
           && todoItem.Text != null)
       .ToListAsync();

Se traduciría aproximadamente (para la misma solicitud mostrada anteriormente) de la siguiente forma:

    SELECT * 
    FROM TodoItem 
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

La instrucción `where` anterior buscará los elementos con el estado `Complete` establecido en false con `Text` no nulo.

También podría haberse escrito en varias líneas:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Los dos métodos son equivalentes y pueden usarse indistintamente. La opción anterior, de concatenación de varios predicados en una consulta, es más compacta y es la que se recomienda.

La cláusula `where` es compatible con las operaciones que pueden traducirse en el subconjunto OData de Servicios móviles. Incluye operadores relacionales (==, !=, &lt;, &lt;=, \>, \>=), operadores aritméticos (+, -, /, \*, %), precisión numérica (Math.Floor, Math.Ceiling), funciones de cadena (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), propiedades de fecha (Year, Month, Day, Hour, Minute, Second), propiedades de acceso de un objeto y expresiones que combinan todas las opciones anteriores.

<a name="sorting"></a>
### Ordenación de datos devueltos

El siguiente código muestra cómo ordenar datos incluyendo una función `OrderBy` o `OrderByDescending` en la consulta. Devuelve los elementos de `todoTable` ordenados de manera ascendente por el campo `Text`.

    // Ordenar elementos en orden ascendente por campo Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

    // Ordenar elementos en orden descendente por campo Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

<a name="paging"></a>
### Devolución de datos en páginas

De forma predeterminada, el servidor devuelve solo las primeras 50 filas. Aumente el número de filas devueltas llamando al método [Take](http://msdn.microsoft.com/en-us/library/windowsazure/dn250574.aspx). Use `Take` junto con el método [Skip](http://msdn.microsoft.com/en-us/library/windowsazure/dn250573.aspx) para solicitar una página específica del conjunto de datos total devuelto por la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

    // Definir una consulta filtrada que devuelva los tres primeros elementos.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

La siguiente consulta revisada omite los tres primeros resultados y devuelve los tres siguientes. Esa es realmente la segunda página de datos en la que el tamańo de página cuenta con tres elementos.

    // Definir una consulta filtrada que omita los tres primeros elementos y devuelta los tres siguientes.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

También puede usar el método [IncludeTotalCount](http://msdn.microsoft.com/en-us/library/windowsazure/dn250560.aspx) para asegurarse de que la consulta obtendrá el recuento total de *todos* los registros que deberían devolverse, ignorando cualquier cláusula de limitación/paginación especificada.

    query = query.IncludeTotalCount();

Este es un escenario simplificado para pasar valores de paginación codificados de forma rígida a los métodos `Take` y `Skip`. En una aplicación en tiempo real, puede usar consultas similares a las anteriores con un control de paginación o interfaz de usuario comparable para permitir a los usuarios desplazarse a las páginas anteriores y posteriores.

<a name="selecting"></a>
### Selección de columnas específicas

Puede especificar qué conjunto de propiedades incluir en los resultados agregando una cláusula `Select` a su consulta. Por ejemplo, el siguiente código muestra cómo seleccionar solo un campo y también cómo seleccionar varios campos y darle formato:

    // Seleccionar un campo, solo Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Seleccionar varios campos: información de Complete y Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete "Ahora completo" : "Incompleto"));
    List<string> items = await query.ToListAsync();

Todas las funciones descritas en adelante son adicionales, por lo que es posible seguir llamándolas y cada vez irá afectando más a la consulta. A continuación se muestra un ejemplo más:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

<a name="lookingup"></a>
### Búsqueda de datos por identificador

La función `LookupAsync` puede usarse para buscar objetos desde la base de datos con un identificador determinado.

    // Esta consulta filtra el elemento con el identificador 37BBF396-11F0-4B39-85C8-B319C729AF6D.
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

<a name="inserting"></a>
Inserción de datosInserción de datos en un servicio móvil
---------------------------------------------------------

<div class="dev-callout"><b>Nota:</b>

<p>si desea realizar operaciones de inserción, búsqueda, eliminación o actualización en un tipo, tiene que crear un miembro denominado <b>Id</b>. Este es el motivo por el que la clase de ejemplo <b>TodoItem</b> cuenta con un miembro de <b>Id</b de nombre. Debe haber un valor válido siempre presente en las operaciones de actualización y eliminación.</p>
</div>

El siguiente código muestra cómo insertar filas nuevas en una tabla. El parámetro contiene los datos que se van a insertar como un objeto .NET.

    await todoTable.InsertAsync(todoItem);

Si no se incluye un valor de identificador personalizado exclusivo en `todoItem` pasado a la llamada `todoTable.InsertAsync`, el servidor establecido en el objeto `todoItem` devuelto al cliente generará un valor para el identificador.

Servicios móviles es compatible con valores de cadena personalizados y exclusivos para el identificador de tabla. Esto permite a las aplicaciones usar valores personalizados como nombres de usuario o direcciones de correo electrónico para la columna de identificador de una tabla de Servicios móviles. Si no se proporciona un valor de identificador de cadena cuando se insertan nuevos registros en una tabla, Servicios móviles generará un valor exclusivo para el identificador.

La compatibilidad de los identificadores de cadena ofrece las siguientes ventajas a los desarrolladores:

-   Los identificadores pueden generarse sin realizar un recorrido de ida y vuelta en la base de datos.
-   Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
-   Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

También puede usar scripts de servidor para configurar valores de identificador. En el ejemplo de script siguiente se genera un GUID personalizado y se le asigna un nuevo identificador de registro. Este es similar al valor del identificador que generaría Servicios móviles si no hubiera pasado un valor para el identificador del registro.

    //Ejemplo de generación de un identificador. Esto no es obligatorio, ya que Servicios móviles
    //generará un identificador si no se pasa uno.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Si una aplicación proporciona un valor para un identificador, Servicios móviles lo guardará como está. Esto incluye los espacios en blanco al principio o al final. Los espacios en blanco no se eliminarán del valor.

El valor `id` debe ser exclusivo y no debe incluir caracteres de los siguientes conjuntos:

-   Caracteres de control: [0x0000-0x001F] y [0x007F-0x009F]. Para obtener más información, consulte [Códigos de control ASCII C0 y C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set).
-   Caracteres imprimibles: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Los identificadores "." y ".."

También puede usar identificadores de números enteros para las tablas. Para usar un identificador de números enteros, debe crear la tabla con el comando `mobile table create` mediante la opción `--integerId`. Este comando se usa con la interfaz de la línea de comandos (CLI) de Azure. Para obtener más información sobre el uso de la CLI, consulte [CLI para administrar tablas de Servicios móviles](http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables).

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

<a name="modifying"></a>
Modificación de datosModificación de datos en un servicio móvil
---------------------------------------------------------------

El siguiente código muestra cómo actualizar una instancia existente con el mismo identificador con nueva información. El parámetro contiene los datos que se van a actualizar como un objeto .NET.

    await todoTable.UpdateAsync(todoItem);

Para insertar datos sin tipo, puede aprovechar Json.NET. Tenga en cuenta que cuando realice una actualización, debe especificarse un identificador, ya que de esa forma el servicio móvil identifica qué instancia actualizar. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Si intenta actualizar un elemento sin proporcionar un valor "Id", no hay forma de que el servicio indique qué instancia actualizar, por lo que el SDK de Servicios móviles iniciará `ArgumentException`.

<a name="deleting"></a>
Eliminación de datosEliminación de datos en un servicio móvil
-------------------------------------------------------------

El siguiente código muestra cómo eliminar una instancia existente. La instancia se identifica mediante el campo "Id" establecido en `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Para eliminar datos sin tipo, puede aprovechar Json.NET. Tenga en cuenta que cuando realice una solicitud de eliminación, debe especificarse un identificador, ya que de esa forma el servicio móvil identifica qué instancia eliminar. Una solicitud de eliminación precisa solo el identificador; las demás propiedades no se pasan al servicio, y si se pasa alguna, se ignoran en el servicio. El resultado de una llamada `DeleteAsync` es normalmente también `null`. El identificador para pasar puede obtenerse a partir del resultado de la llamada `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Si intenta eliminar un elemento sin el campo "Id" ya establecido, no hay forma de que el servicio indique qué instancia eliminar, por lo que volverá a obtener `MobileServiceInvalidOperationException` del servicio. De forma parecida, si intenta eliminar un elemento sin tipo sin el campo "Id" ya establecido, volverá a obtener `MobileServiceInvalidOperationException` del servicio.

<a name="optimisticconcurrency"></a>
Simultaneidad optimistaUso de simultaneidad optimista
-----------------------------------------------------

En algunos casos, dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior incluso si no fuese el resultado deseado. El control de simultaneidad optimista asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos. Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha.

Servicios móviles es compatible con el control de simultaneidad optimista mediante el seguimiento de cambios en cada elemento mediante la columna de propiedades del sistema `__version` que se definió en cada tabla creada por Servicios móviles. Cada vez que se actualiza un registro, Servicios móviles establece la propiedad `__version` para ese registro en un nuevo valor. Durante cada solicitud de actualización, la propiedad `__version` del registro incluido con la solicitud se compara con la misma propiedad del registro en el servidor. Si la versión que pasa con la solicitud no coincide con el servidor, la biblioteca cliente de .NET de Servicios móviles inicia `MobileServicePreconditionFailedException<T>`. El tipo incluido con la excepción es el registro del servidor que contiene la versión del registro del servidor. A continuación, la aplicación puede usar esta información para decidir si ejecutar la solicitud de actualización de nuevo con el valor `__version` correcto del servidor para confirmar los cambios.

Para habilitar la simultaneidad optimista, la aplicación define una columna en la clase de tabla para la propiedad del sistema `__version`. La siguiente definición proporciona un ejemplo.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Habilitar la simultaneidad optimista *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }

Las aplicaciones con tablas sin tipo permiten la simultaneidad optimista mediante el establecimiento de la marca `Version` en `SystemProperties` de la tabla de la siguiente forma.

    //Habilitar la simultaneidad optimista mediante retrieving __version 
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

El siguiente código muestra cómo resolver un conflicto de escritura detectado. El valor `__version` correcto debe incluirse en la llamada `UpdateAsync()` para confirmar una resolución.

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;         
        
        try
        {
            //actualizar en la tabla remota
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }
        
        if (exception != null)
        {
            // Conflicto detectado, el elemento ha cambiado desde la última consulta
            // Resolver el conflicto entre el elemento local y del servidor
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Solicitar al usuario que seleccione la resolución entre versiones
        MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
                                                    serverItem.Text, localItem.Text), 
                                                    "CONFLICTO DETECTADO: seleccionar una resolución:");

        UICommand localBtn = new UICommand("Confirmar texto local");
        UICommand ServerBtn = new UICommand("Dejar texto del servidor");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);          

        localBtn.Invoked = async (IUICommand command) =>
        {
            // Para resolver el conflicto, actualice la versión del 
            // elemento que se va a confirmar. De lo contrario, seguirá
            // almacenando en caché MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;             

            // Actualización recursiva aquí en caso de que 
            // se produzca otro cambio cuando el usuario está tomando una decisión.
            UpdateToDoItem(localItem);
        };          
        
        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };          
        
        await msgDialog.ShowAsync();
    }

Para obtener un ejemplo más completo sobre el uso de la simultaneidad optimista para Servicios móviles, consulte [Tutorial de simultaneidad optimista](http://www.windowsazure.com/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/).

<a name="binding"></a>
Visualización de datosEnlace de datos a la interfaz de usuario en un servicio móvil
-----------------------------------------------------------------------------------

En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario. Para consultar elementos incompletos en `todoTable` y visualizarlos en una lista muy sencilla, puede ejecutar el siguiente código de ejemplo para enlazar el origen de la lista con una consulta. El uso de `MobileServiceCollection` crea una colección de enlaces para los servicios móviles.

    // Esta consulta filtra TodoItems completados. 
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl es un elemento IEnumerable que puede enlazarse a un control de lista de la interfaz de usuario
    IEnumerable itemsControl  = items;            
            
    // Enlazar esto a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Algunos controles de Windows en tiempo de ejecución son compatibles con una interfaz denominada [ISupportIncrementalLoading](http://msdn.microsoft.com/en-us/library/windows/apps/Hh701916). Esta interfaz permite a los controles solicitar datos adicionales cuando el usuario se desplaza. Existe compatibilidad integrada de esta interfaz para las aplicaciones de la Tienda Windows a través de `MobileServiceIncrementalLoadingCollection`, que administra automáticamente las llamadas desde los controles. Para usar `MobileServiceIncrementalLoadingCollection` en las aplicaciones de la Tienda Windows, realice las siguientes acciones:

         MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
        items =  todoTable.Where(todoItem => todoItem.Complete == false)
                    .ToIncrementalLoadingCollection();

        ListBox lb = new ListBox();
        lb.ItemsSource = items;

Para usar la nueva colección en Windows Phone, utilice los métodos de extensión `ToCollection` en `IMobileServiceTableQuery<T>` y `IMobileServiceTable<T>`. Para cargar datos realmente, llame a `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection(); 
	await items.LoadMoreItemsAsync();         

Cuando use la colección creada mediante la llamada a `ToCollectionAsync` o `ToCollection`, obtendrá una colección que puede enlazarse a los controles de la interfaz de usuario. Esta colección es para la paginación, es decir, un control puede solicitar a la colección cargar más elementos y la colección lo hará para el control. En este momento, no hay ningún código de usuario implicado y el control iniciará el flujo. Sin embargo, puesto que la colección está cargando datos desde la red, cabe esperar que a veces se produzca un error en la carga. Para gestionar esos errores, puede reemplazar el método `OnException` de `MobileServiceIncrementalLoadingCollection` para gestionar excepciones resultantes de las llamadas a `LoadMoreItemsAsync` que han realizado los controles.

Para finalizar, imagine que la tabla contiene muchos campos, pero solo desea que se muestren algunos en el control. Puede usar la guía de la sección [Selección de columnas específicas](#selecting) anterior para seleccionar las columnas específicas que mostrar en la interfaz de usuario.

<a name="authentication"></a>
AutenticaciónAutenticación de usuarios
--------------------------------------

Servicios móviles es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante una serie de proveedores de identidades externas: Facebook, Google, cuenta Microsoft, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial "Introducción a la autenticación" ([Tienda Windows](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/)).

Se admiten dos flujos de autenticación: un *flujo de servidor* y un *flujo de cliente*. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

### Flujo de servidor

Para que Servicios móviles administre el proceso de autenticación en la aplicación de la Tienda Windows o Windows Phone, debe registrar la aplicación con el proveedor de identidades. A continuación, en el servicio móvil, tendrá que configurar el identificador y el secreto de la aplicación proporcionados por el proveedor. Para obtener más información, consulte el tutorial "Introducción a la autenticación" ([Tienda Windows](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/)).

Una vez que haya registrado el proveedor de identidades, simplemente llame al método [LoginAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) con el valor [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) del proveedor. Por ejemplo, el siguiente código activa un inicio de sesión de flujo de servidor mediante Facebook.

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

Si está usando un proveedor de identidades diferente al de Facebook, cambie el valor de [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) anterior por el valor de su proveedor.

En este caso, Servicios móviles administra el flujo de autenticación de OAuth 2.0 mostrando la página de inicio de sesión del proveedor seleccionado y generando un token de autenticación de Servicios móviles después de que se realice un inicio de sesión correcto con el proveedor de identidades. El método [LoginAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) devuelve [MobileServiceUser](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx), que proporciona [userId](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx) del usuario autenticado y [MobileServiceAuthenticationToken](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx) como un token de web JSON (JWT). El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación](#caching).

<div class="dev-callout"><b>Aplicación de la Tienda Windows</b>

<p>Cuando use el proveedor de inicio de sesión de la cuenta de Microsoft para autenticar a los usuarios de la aplicación de la Tienda Windows, también debe registrar el paquete de la aplicación con Servicios móviles. Cuando registre la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, el cliente podrá volver a usar las credenciales de inicio de sesión de la cuenta de Microsoft para conseguir una experiencia de inicio de sesión único. Si no realiza este procedimiento, los usuarios de inicio de sesión de la cuenta de Microsoft visualizarán una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión. Para saber cómo registrar el paquete de la aplicación de la Tienda Windows, consulte <a href="/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Registro del paquete de la aplicación de la Tienda Windows para la autenticación de Microsoft</a>. Una vez que se registre la información del paquete con Servicios móviles, llame al método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> proporcionando un valor <b>true</b> para el parámetro <em>useSingleSignOn</em> con el fin de volver a usar las credenciales.</p>
</div>

### Flujo de cliente

La aplicación también puede ponerse en contacto de manera independiente con el proveedor de identidades y, a continuación, proporcionar el token devuelto a Servicios móviles para la autenticación. Este flujo de cliente le permite proporcionar una experiencia de inicio de sesión único para los usuarios o recuperar datos de usuario adicionales del proveedor de identidades.

En la forma más simplificada, puede usar el flujo de cliente como se muestra en este fragmento para Facebook o Google.

    var token = new JObject();
    // Reemplazar ccess_token_value por el valor real del token de acceso obtenido 
    // mediante el SDK de Google o Facebook.
    token.Add("access_token", "access_token_value");
            
    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Cambiar MobileServiceAuthenticationProvider.Facebook 
                // por MobileServiceAuthenticationProvider.Google si usa la autenticación de Google.
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

Si usa una cuenta Microsoft, el inicio de sesión tendrá la siguiente forma:

    // Reemplazar authentication_token_value por el valor real del token de autenticación de Microsoft obtenido a través del SDK de Live
    user = await client
        .LoginWithMicrosoftAccountAsync(authentication_token_value);

Para obtener un ejemplo sobre cómo usar una cuenta Microsoft para proporcionar una experiencia de inicio de sesión único, consulte el tutorial "Autenticación de la aplicación con inicio de sesión único" ([Tienda Windows](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

<a name="errors"></a>
### Almacenamiento en caché del token de autenticación

En algunos casos, la llamada al método de inicio de sesión puede evitarse después de la primera vez que el usuario se autentique. Puede usar [PasswordVault](http://msdn.microsoft.com/en-us/library/windows/apps/windows.security.credentials.passwordvault.aspx) en las aplicaciones de la Tienda Windows para almacenar en caché la identidad del usuario actual la primera vez que se inicie sesión en ellas y las veces posteriores que compruebe si ya dispone de la identidad de usuario en la memoria caché. Si la memoria caché está vacía, tendrá que enviar el usuario a través del proceso de inicio de sesión.

    // Después de iniciar sesión
    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

    // Iniciar sesión 
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        user = new MobileServiceUser(creds.UserName);
        user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Flujo de inicio de sesión normal
        user = new MobileServiceuser( await client
            .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
        var token = new JObject();
        // Reemplazar access_token_value por el valor real del token de acceso
        token.Add("access_token", "access_token_value");
    }
            
     // Cerrar sesión
    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", user.UserId));

En las aplicaciones de Windows Phone, puede almacenar en caché los datos y cifrarlos mediante la clase [ProtectedData](http://msdn.microsoft.com/en-us/library/system.security.cryptography.protecteddata%28VS.95%29.aspx), y almacenar información confidencial en un almacén aislado.


Gestión de erroresGestión de errores
------------------------------------

Existen varias formas de detectar, validar y solucionar errores en Servicios móviles.

Por ejemplo, los scripts de servidor se registran en un servicio móvil y pueden usarse para realizar una amplia variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. Imagine que define y registra un script de servidor que valida y modifica datos:

    function insert(item, user, request) 
    {
       if (item.text.length > 10) {
          request.respond(statusCodes.BAD_REQUEST, { error: "El texto no puede exceder los 10 caracteres" });
       } else {
          request.execute();
       }
    }

Este script del servidor valida la longitud de los datos de cadena enviados al servicio móvil y rechaza las cadenas que son muy largas, en este caso superiores a 10 caracteres.

Ahora que el servicio móvil está validando datos y enviando respuestas de error en el servidor, puede actualizar la aplicación .NET para poder gestionar las respuestas de error a partir de la validación.

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // Este código inserta un nuevo elemento TodoItem en la base de datos. Cuando la operación se completa
        // y Servicios móviles dispone de un identificador, el elemento se agrega a CollectionView.
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Gestionar error
        }
    }

<a name="untyped"></a>
Uso de datos sin tipoUso de datos sin tipo
------------------------------------------

El cliente .NET se ha creado para escenarios fuertemente tipados. Sin embargo, es recomendable una experiencia menos tipada; por ejemplo, cuando se trata con objetos con un esquema abierto. El escenario se habilita de la forma siguiente. En las consultas, prescinda de LINQ y use el formato.

    // Obtener una referencia de tabla sin tipo
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");         

    // Buscar datos sin tipo con OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vuelva a obtener valores JSON que puede usar como un contenedor de propiedades. Para obtener más información sobre JToken y Json.NET, consulte [Json.NET](http://json.codeplex.com/).

<a name="unit-testing"></a>
Diseńo de pruebasDiseńo de pruebas unitarias
--------------------------------------------

El valor devuelto por `MobileServiceClient.GetTable` y las consultas son interfaces. Esto hace que puedan crearse bocetos fácilmente para propósitos de prueba, por lo que puede crear `MyMockTable : IMobileServiceTable<TodoItem>` que implemente la lógica de prueba.

<a name="customizing"></a>
Personalización del clientePersonalización del cliente
------------------------------------------------------

<a name="headers"></a>
### Personalización de encabezados de solicitud

Es posible que desee acoplar un encabezado personalizado a cada solicitud saliente o cambiar los códigos de estado de las respuestas. Puede llevar a cabo esto configurando DelegatingHandler como aparece a continuación:

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient( 
            "AppUrl", 
            "AppKey" ,
            new MyHandler()
            ); 
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await table.InsertAsync(newItem);
    }
             
    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            request.Headers.Add("x-my-header", "my value");
            var response = awaitbase.SendAsync(request, cancellationToken);
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

<a name="serialization"></a>
### Personalización de serialización

La clase [MobileServiceClient](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) expone una propiedad `SerializerSettings` de tipo [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm).

Puede establecer propiedades Json.NET (existen varias) con esta propiedad. Por ejemplo, puede incluir una para convertir todas las propiedades en minúscula:

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

<a name="nextsteps"></a>
Pasos siguientes
----------------

Ahora que ha completado este tema de referencia conceptual, conozca cómo realizar tareas importantes en Servicios móviles de forma detallada:

-   [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started)
    Conozca los aspectos básicos de cómo usar Servicios móviles.

-   [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    Aprenda a autenticar a los usuarios de su aplicación con un proveedor de identidades.

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

-   [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    Conozca cómo usar el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles.


