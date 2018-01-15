---
title: Biblioteca de cliente administrada de App Service Mobile Apps (Windows) | Microsoft Azure
description: Aprenda a usar un cliente .NET para Azure App Service Mobile Apps con aplicaciones de Windows y Xamarin.
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2017
ms.author: crdun
ms.openlocfilehash: c80265432f4ee3120e3125b45712dc0e7a434708
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Uso del cliente administrado para Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Información general
En esta guía se muestra cómo realizar escenarios comunes con la biblioteca de cliente administrada para Azure App Service Mobile Apps para Windows y Xamarin. Si no tiene experiencia en el uso de Mobile Apps, considere primero la opción de completar el tutorial [Guía de inicio rápido de Azure Mobile Apps][1]. En esta guía, nos centramos en el SDK administrado de cliente. Para obtener más información sobre los SDK para Mobile Apps del lado servidor, consulte la documentación de [SDK de servidor .NET][2] o de [SDK de servidor Node.js][3].

## <a name="reference-documentation"></a>Documentación de referencia
La documentación de referencia del SDK de cliente se encuentra aquí: [Referencia del cliente de .NET de Azure Mobile Apps][4].
También encontrará varios ejemplos de cliente en el [repositorio de GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Plataformas compatibles
La plataforma de .NET es compatible con las siguientes plataformas:

* Versiones de Xamarin Android para niveles de API 19-24 (de KitKat a Nougat)
* Versiones de Xamarin iOS para iOS 8.0 y posterior
* Plataforma universal de Windows
* Windows Phone 8,1
* Windows Phone 8.0, salvo para aplicaciones de Silverlight

La autenticación de flujo de servidor utiliza una vista web para la interfaz de usuario presentada.  Si el dispositivo no puede presentar una interfaz de usuario de vista web, hay que utilizar otros métodos de autenticación.  Por tanto, este SDK no es adecuado para dispositivos de tipo reloj o con restricciones similares.

## <a name="setup"></a>Configuración y requisitos previos
Se supone que ya ha creado y publicado el proyecto de back-end de la aplicación móvil, que incluye al menos una tabla.  En el código usado en este tema, el nombre de la tabla es `TodoItem` y dispondrá de las siguientes columnas: `Id`, `Text` y `Complete`. Esta tabla es la misma que se crea cuando se completa la [Guía de inicio rápido de Azure Mobile Apps][1].

El tipo del cliente con tipo correspondiente en C# es la siguiente clase:

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

Tenga en cuenta que [JsonPropertyAttribute][6] se usa para definir la asignación *PropertyName* entre el campo de cliente y de tabla.

Para aprender a crear tablas en el back-end de Mobile Apps, consulte el [tema del SDK de servidor .NET][7] o el tema del [SDK de servidor Node.js][8]. Si creó el back-end de aplicación móvil en Azure Portal mediante la guía de inicio rápido, también puede usar la opción **Tablas fáciles** en [Azure Portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Instalación del paquete del SDK de cliente administrado
Utilice uno de los métodos siguientes para instalar el paquete del SDK de cliente administrado para Mobile Apps desde [NuGet][9]:

* **Visual Studio** Haga clic con el botón derecho en el proyecto, haga clic en **Administrar paquetes NuGet**, busque el paquete `Microsoft.Azure.Mobile.Client` y haga clic en **Instalar**.
* **Xamarin Studio** Haga clic con el botón derecho en el proyecto, haga clic en **Add** (Agregar) >**Add NuGet Packages** (Agregar paquetes NuGet), busque el paquete `Microsoft.Azure.Mobile.Client ` y haga clic en **Add Package** (Agregar paquete).

En el archivo de la actividad principal, no olvide agregar la siguiente instrucción **using** :

```
using Microsoft.WindowsAzure.MobileServices;
```

### <a name="symbolsource"></a>Trabajo con símbolos de depuración en Visual Studio
Los símbolos del espacio de nombres Microsoft.Azure.Mobile están disponibles en [SymbolSource][10].  Consulte las [instrucciones de SymbolSource][11] para integrar SymbolSource con Visual Studio.

## <a name="create-client"></a>Creación del cliente de Mobile Apps
El código siguiente crea el objeto [MobileServiceClient][12] que se usa para obtener acceso al back-end de la aplicación móvil.

```
var client = new MobileServiceClient("MOBILE_APP_URL");
```

En el código anterior, reemplace `MOBILE_APP_URL` por la dirección URL del back-end de aplicación móvil, que se encuentra en la hoja de la aplicación móvil en [Azure Portal]. El objeto MobileServiceClient debe ser un singleton.

## <a name="work-with-tables"></a>Trabajo con tablas
La siguiente sección describe cómo buscar y recuperar registros y modificar los datos de la tabla.  Se tratan los siguientes temas:

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

### <a name="instantiating"></a>Creación de una referencia de tabla
Todo el código que obtiene acceso a datos o los modifica en una tabla de back-end llama a las funciones del objeto `MobileServiceTable` . Obtenga una referencia a la tabla llamando al método [GetTable] del modo indicado a continuación:

```
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

El objeto devuelto usa el modelo de serialización con tipo. También se admite un modelo de serialización sin tipo. El siguiente ejemplo [crea una referencia a una tabla sin tipo]:

```
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

En las consultas sin tipo, debe especificar la cadena de consulta de OData subyacente.

### <a name="querying"></a>Consulta de datos desde la aplicación móvil
En esta sección se describe cómo generar consultas al back-end de la aplicación móvil, lo cual incluye la siguiente funcionalidad:

* [Filtrar datos devueltos](#filtering)
* [Ordenar datos devueltos](#sorting)
* [Devolver datos en páginas](#paging)
* [Seleccionar columnas específicas](#selecting)
* [Buscar datos por identificador](#lookingup)

> [!NOTE]
> Se aplica el tamaño de página del servidor para evitar que se devuelvan todas las filas.  La paginación evita que las solicitudes predeterminadas de los conjuntos de datos de gran tamaño incidan negativamente en el servicio.  Para devolver más de 50 filas, use los métodos `Skip` y `Take`, como se describe en [Devolución de datos en páginas](#paging).

### <a name="filtering"></a>Filtro de datos devueltos
El siguiente código muestra cómo filtrar los datos incluyendo una cláusula `Where` en una consulta. Devuelve todos los elementos de `todoTable` cuya propiedad `Complete` es igual a `false`. La función [Where] aplica un predicado de filtrado de filas a la consulta en relación con la tabla.

```
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Puede ver el identificador URI de la solicitud que se ha enviado al back-end mediante el software de inspección de mensajes, como las herramientas para desarrolladores del explorador o [Fiddler]. Si consulta el URI de solicitud, tenga en cuenta que se ha modificado la cadena de consulta:

```
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

El SDK de servidor traduce esta solicitud de OData a una consulta SQL:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

La función que se pasa al método `Where` puede disponer de un número arbitrario de condiciones.

```
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

El SDK de servidor traduciría este ejemplo a una consulta SQL:

```
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Esta consulta también se puede dividir en varias cláusulas:

```
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Los dos métodos son equivalentes y pueden usarse indistintamente.  La opción anterior&mdash;de concatenación de varios predicados en una consulta&mdash;es más compacta y es la que se recomienda.

La cláusula `Where` admite las operaciones que pueden traducirse en el subconjunto OData. Estas son algunas de las operaciones:

* Operadores relacionales (==, !=, <, <=, >, >=)
* Operadores aritméticos (+, -, /, *, %)
* Precisión numérica (Math.Floor, Math.Ceiling)
* Funciones de cadena (Length, Substring, Replace, IndexOf, StartsWith, EndsWith)
* Propiedades de fecha (Year, Month, Day, Hour, Minute, Second)
* Propiedades de acceso de un objeto
* Expresiones combinando cualquiera de estas operaciones

Al tener en cuenta lo que admite el SDK de servidor, puede consultar la [documentación de OData v3].

### <a name="sorting"></a>Clasificación de datos devueltos
El siguiente código muestra cómo ordenar datos incluyendo una función [OrderBy] u [OrderByDescending] en la consulta. Devuelve los elementos de `todoTable` ordenados de manera ascendente por el campo `Text`.

```
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Devolución de datos en páginas
De manera predeterminada, el back-end devuelve solo las primeras 50 filas. Aumente el número de filas devueltas llamando al método [Take] . Use `Take` junto con el método [Skip] para solicitar una "página" específica del conjunto de datos total devuelto por la consulta. Cuando se ejecuta la siguiente consulta, se devuelven los tres primeros elementos de la tabla.

```
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

La siguiente consulta revisada omite los tres primeros resultados y devuelve los tres siguientes. Esta consulta genera la segunda página de datos en la que el tamaño de página cuenta con tres elementos.

```
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

El método [IncludeTotalCount] solicita el recuento total de *todos* los registros que habría que devolver, con lo que se omite cualquier cláusula de limitación/paginación especificada:

```
query = query.IncludeTotalCount();
```

En una aplicación real, puede usar consultas similares a las anteriores con un control de paginación o una interfaz de usuario comparable para permitir a los usuarios desplazarse entre las páginas.

> [!NOTE]
> Para reemplazar el límite de 50 filas de un back-end de aplicación móvil, también debe aplicar [EnableQueryAttribute] al método público GET y especificar el comportamiento de paginación. Cuando se aplica al método, lo siguiente establece el máximo de filas devueltas a 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Selección de columnas específicas
Puede especificar qué conjunto de propiedades incluir en los resultados agregando una cláusula [Select] a su consulta. Por ejemplo, el siguiente código muestra cómo seleccionar solo un campo y también cómo seleccionar varios campos y darle formato:

```
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
```

Todas las funciones descritas hasta ahora son aditivas, por lo que podemos mantener el encadenamiento. Cada llamada encadenada afecta a más elementos aparte de la consulta. A continuación se muestra un ejemplo más:

```
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Búsqueda de datos por identificador
La función [LookupAsync] puede usarse para buscar objetos desde la base de datos con un identificador determinado.

```
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Ejecución de consultas sin tipo
Al ejecutar una consulta mediante un objeto de tabla sin tipo, debe especificar explícitamente la cadena de consulta de OData llamando a [ReadAsync], como en el ejemplo siguiente:

```
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Vuelva a obtener valores JSON que puede usar como un contenedor de propiedades. Para obtener más información sobre JToken y Newtonsoft Json.NET, visite el sitio de [Json.NET] .

### <a name="inserting"></a>Inserción de datos en el back-end de una aplicación móvil
Todos los tipos de cliente deben incluir un miembro llamado **Id**, que es una cadena de forma predeterminada. Este elemento **Id** es necesario para realizar operaciones CRUD y para trabajar sin conexión. El siguiente código muestra cómo usar el método [InsertAsync] para insertar filas nuevas en una tabla. El parámetro contiene los datos que se van a insertar como un objeto .NET.

```
await todoTable.InsertAsync(todoItem);
```

Si no se incluye un valor de identificador personalizado exclusivo en `todoItem` durante una inserción, el servidor genera un GUID.
Puede recuperar el identificador generado inspeccionando el objeto después de que se devuelva la llamada.

Para insertar datos sin tipo, puede utilizar Json.NET:

```
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

A continuación, se muestra un ejemplo en el que se usa una dirección de correo electrónico como identificador de cadena exclusivo:

```
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Trabajar con valores de Id.
El servicio Mobile Apps admite valores de cadena personalizados únicos para la columna **id** de la tabla. Esto permite a las aplicaciones usar valores personalizados como direcciones de correo electrónico o nombres de usuario para el id.  Los identificadores de cadena proporcionan las siguientes ventajas:

* Se generan identificadores sin realizar una vuelta a la base de datos.
* Los registros son más fáciles de fusionar desde diferentes tablas o bases de datos.
* Los valores de los identificadores pueden integrarse mejor con una lógica de aplicación.

Cuando no se establece un valor de identificador de cadena en un registro insertado, el back-end de la aplicación móvil genera un valor único para el identificador. Puede usar el método [Guid.NewGuid] para generar sus propios valores de identificador, ya sea en el cliente o en el back-end.

```
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Modificación de datos en el back-end de una aplicación móvil
El siguiente código muestra cómo usar el método [UpdateAsync] para actualizar un registro existente con el mismo identificador con nueva información. El parámetro contiene los datos que se van a actualizar como un objeto .NET.

```
await todoTable.UpdateAsync(todoItem);
```

Para actualizar datos sin tipo, puede usar [Json.NET] de la siguiente manera:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Debe especificarse un campo `id` al realizar una actualización. El back-end utiliza el campo `id` para identificar qué fila actualizar. El campo `id` puede obtenerse a partir del resultado de la llamada a `InsertAsync`. Se genera una excepción `ArgumentException` cuando trata de actualizar un elemento sin proporcionar el valor `id`.

### <a name="deleting"></a>Eliminación de datos del back-end de una aplicación móvil
El siguiente código muestra cómo usar el método [DeleteAsync] para eliminar una instancia existente. La instancia se identifica mediante el campo `id` establecido en `todoItem`.

```
await todoTable.DeleteAsync(todoItem);
```

Para eliminar datos sin tipo, puede aprovechar Json.NET de la siguiente manera:

```
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Al realizar una solicitud de eliminación, debe especificarse un identificador. Otras propiedades no se pasan al servicio o se omiten en este. El resultado de una llamada `DeleteAsync` normalmente es `null`. El identificador puede obtenerse a partir del resultado de la llamada `InsertAsync` . Se produce una excepción `MobileServiceInvalidOperationException` cuando se trata de eliminar un elemento sin especificar el campo `id`.

### <a name="optimisticconcurrency"></a>Uso de la simultaneidad optimista para resolver conflictos
Dos o más clientes pueden escribir cambios en el mismo elemento y al mismo tiempo. Si no se produjera la detección de conflictos, la última escritura sobrescribiría cualquier actualización anterior. **control de simultaneidad optimista** asume que cada transacción puede confirmarse y, por lo tanto, no usa ningún bloqueo de recursos.  Antes de confirmar una transacción, el control de simultaneidad optimista comprueba que ninguna otra transacción haya modificado los datos. Si los datos se han modificado, la transacción de confirmación se desecha.

El servicio Mobile Apps es compatible con el control de simultaneidad optimista gracias al seguimiento de cambios en cada elemento mediante la columna de propiedades del sistema `version` que se definió en cada tabla en el back-end de la aplicación móvil. Cada vez que se actualiza un registro, el servicio Mobile Apps establece la propiedad `version` de ese registro en un nuevo valor. Durante cada solicitud de actualización, la propiedad `version` del registro incluido con la solicitud se compara con la misma propiedad del registro en el servidor. Si la versión que pasa con la solicitud no coincide con el back-end, la biblioteca de cliente genera una excepción `MobileServicePreconditionFailedException<T>` . El tipo incluido con la excepción es el registro del back-end que contiene la versión del registro del servidor. A continuación, la aplicación puede usar esta información para decidir si ejecutar la solicitud de actualización de nuevo con el valor `version` correcto del back-end para confirmar los cambios.

Defina una columna en la clase de tabla para la propiedad del sistema `version` con el fin de habilitar la simultaneidad optimista. Por ejemplo: 

```
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Las aplicaciones con tablas sin tipo permiten la simultaneidad optimista mediante el establecimiento de la marca `Version` en `SystemProperties` de la tabla de la siguiente forma.

```
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Además de habilitar la simultaneidad optimista, se debe detectar la excepción `MobileServicePreconditionFailedException<T>` en el código al llamar a [UpdateAsync].  Resuelva el conflicto aplicando el valor `version` correcto al registro actualizado y llame a [UpdateAsync] con el registro resuelto. El siguiente código muestra cómo resolver un conflicto de escritura detectado:

```
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
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
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
```

Para obtener más información, consulte el tema [Sincronización de datos sin conexión en Azure Mobile Apps] .

### <a name="binding"></a>Enlace de datos de Mobile Apps a una interfaz de usuario de Windows
En esta sección se describe cómo mostrar objetos de datos devueltos mediante elementos de la interfaz de usuario en una aplicación Windows.  El ejemplo de código siguiente se enlaza al origen de la lista con una consulta de elementos incompletos. [MobileServiceCollection] crea una colección de enlaces compatible con Mobile Apps.

```
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Algunos controles en tiempo de ejecución administrado admiten una interfaz denominada [ISupportIncrementalLoading]. Esta interfaz permite a los controles solicitar datos adicionales cuando el usuario se desplaza. Las aplicaciones universales de Windows integran compatibilidad con esta interfaz mediante [MobileServiceIncrementalLoadingCollection], que administra automáticamente las llamadas desde los controles. Use `MobileServiceIncrementalLoadingCollection` en aplicaciones Windows de la siguiente manera:

```
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Para usar la nueva colección en aplicaciones de Windows Phone 8 y "Silverlight", use los métodos de extensión `ToCollection` en `IMobileServiceTableQuery<T>` y `IMobileServiceTable<T>`. Para cargar datos, llame a `LoadMoreItemsAsync()`.

```
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Cuando use la colección creada mediante la llamada a `ToCollectionAsync` o `ToCollection`, obtendrá una colección que puede enlazarse a los controles de la interfaz de usuario.  Esta colección es para la paginación.  Como la colección está cargando datos desde la red, a veces, pueden producirse errores en este proceso. Para gestionar esos errores, puede reemplazar el método `OnException` de `MobileServiceIncrementalLoadingCollection` con el fin de controlar las excepciones resultantes de las llamadas a `LoadMoreItemsAsync`.

Imagine que la tabla contiene muchos campos, pero solo quiere que se muestren algunos en el control. Puede usar la guía de la sección anterior[Selección de columnas específicas](#selecting)con el fin de elegir las columnas específicas que se mostrarán en la interfaz de usuario.

### <a name="pagesize"></a>Cambio del tamaño de página
Mobile Apps de Azure devuelve un máximo de 50 elementos por cada solicitud de forma predeterminada.  Puede cambiar el tamaño de paginación si aumenta el de página máximo en el cliente y el servidor.  Para aumentar el tamaño de página solicitado, especifique `PullOptions` al usar `PullAsync()`:

```
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Suponiendo que ha establecido el valor de `PageSize` igual o mayor que 100 en el servidor, se devolverán hasta 100 elementos en cada solicitud.

## <a name="#offlinesync"></a>Trabajo con tablas sin conexión
Las tablas sin conexión utilizan un almacén SQLite local para almacenar datos para usarlos cuando estén sin conexión.  Todas las operaciones de las tablas se realizan en el almacén SQLite local, en lugar del almacén del servidor remoto.  Para crear una tabla sin conexión, prepare primero el proyecto:

1. En Visual Studio, haga clic con el botón derecho en la solución > **Administrar paquetes NuGet para la solución...** y, después, busque e instale el paquete NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** en todos los proyectos de la solución.
2. (Opcional) Para admitir dispositivos de Windows, instale uno de los siguientes paquetes en el sistema de tiempo de ejecución de SQLite:

   * **Runtime de Windows 8.1:** instale [SQLite para Windows 8.1][3].
   * **Windows Phone 8.1:** instale [SQLite para Windows Phone 8.1][4].
   * **Plataforma universal de Windows** Instale [SQLite para la plataforma universal de Windows][5].
3. (Opcional). En el caso de los dispositivos Windows, haga clic en **Referencias** > **Agregar referencia...**, expanda la carpeta **Windows** > **Extensiones** y habilite el SDK de **SQLite para Windows** apropiado, junto con el SDK de **Runtime de Visual C++ 2013 para Windows**.
    Los nombres de SDK de SQLite varían ligeramente con cada plataforma de Windows.

Para poder crear una referencia de tabla, debe prepararse el almacén local:

```
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Normalmente, la inicialización del almacén se realiza inmediatamente después de que se crea el cliente.  **OfflineDbPath** debe ser un nombre de archivo adecuado para usarlo en todas las plataformas compatibles.  Si la ruta de acceso es completa (es decir, comienza con una barra diagonal), se utiliza dicha ruta.  Si la ruta de acceso no es completa, el archivo se coloca en una ubicación específica de la plataforma.

* En los dispositivos iOS y Android, la ruta de acceso predeterminada es la carpeta "Personal Files".
* En los dispositivos de Windows, la ruta de acceso predeterminada es la carpeta "AppData" específica de la aplicación.

Un referencia de tabla se puede obtener mediante el método `GetSyncTable<>`:

```
var table = client.GetSyncTable<TodoItem>();
```

No es necesario autenticarse para usar una tabla sin conexión.  Solo es preciso hacerlo cuando se establece comunicación con el servicio back-end.

### <a name="syncoffline"></a>Sincronización de una tabla sin conexión
Las tablas sin conexión no se sincronizan con el back-end de manera predeterminada.  La sincronización se divide en dos partes.  Mediante la descarga de elementos nuevos puede insertar los cambios por separado.  Éste es un método de sincronización típico:

```
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Si el primer argumento para `PullAsync` es nulo, no se usa la sincronización incremental.  Todas las operaciones de sincronización recuperan todos los registros.

El SDK realiza una tarea `PushAsync()` implícita antes de extraer registros.

El control de los conflictos se realiza en un método `PullAsync()`.  Los conflictos se pueden tratar de la misma manera que las tablas en línea.  El conflicto se produce cuando se llama a `PullAsync()`, en lugar de durante la inserción, actualización o eliminación. Si se producen varios conflictos, se agrupan en una sola clase MobileServicePushFailedException.  Trate cada error por separado.

## <a name="#customapi"></a>Trabajo con una API personalizada
Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asigna a una operación de inserción, actualización, eliminación o lectura. Al usar una API personalizada, puede tener más control sobre la mensajería, incluida la lectura y el establecimiento de encabezados de mensajes HTTP y la definición del formato del cuerpo de un mensaje diferente de JSON.

Puede llamar a una API personalizada al realizar una llamada a una de las sobrecargas del método [InvokeApiAsync] en el cliente. Por ejemplo, la siguiente línea de código envía una solicitud POST a la API **completeAll** en el back-end:

```
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Se trata de una llamada de método con tipo que requiere que se defina el tipo de devolución de **MarkAllResult** . Se admiten métodos con y sin tipos.

El método InvokeApiAsync() antepone "/api/" a la API a la que desea llamar, a menos que la API comience por "/".
Por ejemplo: 

* `InvokeApiAsync("completeAll",...)`llama a /api/completeAll en el back-end
* `InvokeApiAsync("/.auth/me",...)`llama a /.auth/me en el back-end

Puede usar InvokeApiAsync para llamar a cualquier WebAPI, incluidas las que no están definidas en Azure Mobile Apps.  Cuando usa InvokeApiAsync(), se envían los encabezados correspondientes, incluidos los encabezados de autenticación, con la solicitud.

## <a name="authentication"></a>Autenticación de usuarios
Mobile Apps es compatible con la autenticación y la autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externas: Facebook, Google, cuenta de Microsoft, Twitter y Azure Active Directory. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Incorporación de la autenticación a su aplicación].

Se admiten dos flujos de autenticación: *administrado por cliente* y *administrado por servidor*. Este último ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo administrado por cliente permite una mayor integración con funcionalidades específicas del dispositivo, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

> [!NOTE]
> En las aplicaciones de producción se recomienda usar un flujo administrado por el cliente.

Para configurar la autenticación, debe registrar la aplicación en uno o varios proveedores de identidades.  El proveedor de identidades generará un identificador y un secreto de cliente para la aplicación.  Estos valores se establecen en el back-end para habilitar la autenticación y autorización de Azure App Service.  Para obtener más información, siga las instrucciones detalladas del tutorial [Incorporación de la autenticación a su aplicación].

En esta sección se tratan los siguientes temas:

* [Autenticación administrada por el cliente](#clientflow)
* [Autenticación administrada por el servidor](#serverflow)
* [Almacenamiento en caché del token de autenticación](#caching)

### <a name="clientflow"></a>Autenticación administrada por el cliente
La aplicación puede ponerse en contacto de manera independiente con el proveedor de identidades y proporcionar el token devuelto en el inicio de sesión junto con el back-end. Este flujo de cliente permite proporcionar una experiencia de inicio de sesión único a los usuarios o recuperar datos de usuario adicionales del proveedor de identidades. Se prefiere la autenticación de flujo de cliente al uso de una de flujo de servidor, ya que el SDK de proveedor de identidades proporciona una experiencia UX más nativa y permite realizar más personalizaciones.

Se proporcionan ejemplos de los siguientes patrones de autenticación de flujo de cliente:

* [Biblioteca de autenticación de Active Directory](#adal)
* [Facebook o Google](#client-facebook)
* [SDK de Live](#client-livesdk)

#### <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory
La biblioteca de autenticación de Active Directory (ADAL) se puede usar para iniciar la autenticación de usuarios desde el cliente con la autenticación de Azure Active Directory.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de App Service para usar el inicio de sesión de Azure Active Directory] . Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa.
2. En Visual Studio o Xamarin Studio, abra el proyecto y agregue una referencia al paquete NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory` . Al buscar, incluya las versiones preliminares.
3. Agregue el siguiente código a la aplicación, según la plataforma que utilice. En cada caso, realice las sustituciones siguientes:

   * Reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación. El formato debería ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en [Azure Portal].
   * Reemplace **INSERT-RESOURCE-ID-HERE** por el Id. de cliente del back-end de la aplicación móvil. El Id. de cliente en la pestaña **Opciones avanzadas** de **Configuración de Azure Active Directory** en el portal.
   * Reemplace **INSERT-CLIENT-ID-HERE** por el Id. de cliente que copió de la aplicación cliente nativa.
   * Reemplace **INSERT-REDIRECT-URI-HERE** por el punto de conexión */.auth/login/done* del sitio, mediante el esquema HTTPS. Este valor debe ser similar a *https://contoso.azurewebsites.net/.auth/login/done*.

     El código necesario para cada plataforma es el siguiente:

     **Windows:**

    ```
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
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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
    ```

     **Xamarin.iOS**

    ```
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
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

     **Xamarin.Android**

    ```
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
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
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
    ```

#### <a name="client-facebook"></a>Inicio de sesión único mediante un token de Facebook o Google
Puede usar el flujo de cliente como se muestra en este fragmento para Facebook o Google.

```
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
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
```

#### <a name="client-livesdk"></a>Inicio de sesión único mediante una cuenta Microsoft con el SDK de Live
Para autenticar usuarios, debe registrar la aplicación en el Centro para desarrolladores de la cuenta Microsoft. Configure los detalles del registro en su back-end de aplicación móvil. Complete los pasos del artículo sobre cómo [registrar la aplicación para usar el inicio de sesión de la cuenta Microsoft]para crear un registro de la cuenta Microsoft y conectarlo al back-end de aplicación móvil. Si dispone de ambas versiones de la aplicación, Tienda Windows y Windows Phone 8/Silverlight, registre primero la versión de Tienda Windows.

El siguiente código se autentica mediante el SDK de Live y usa el token devuelto para iniciar sesión en el back-end de Mobile Apps.

```
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
```

Para obtener más información, consulte la documentación del [SDK de Windows Live] .

### <a name="serverflow"></a>Autenticación administrada por el servidor
Una vez que haya registrado el proveedor de identidades, llame al método [LoginAsync] de [MobileServiceClient] con el valor [MobileServiceAuthenticationProvider] del proveedor. Por ejemplo, el siguiente código activa un inicio de sesión de flujo de servidor mediante Facebook.

```
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
```

Si usa un proveedor de identidades que no sea Facebook, cambie el valor de [MobileServiceAuthenticationProvider] al valor de su proveedor.

En un flujo de servidor, Azure App Service administra el flujo de autenticación OAuth mostrando la página de inicio de sesión del proveedor seleccionado.  Cuando se devuelve el proveedor de identidades, Azure App Service genera un token de autenticación de este servicio. El método [LoginAsync] devuelve [MobileServiceUser], que proporciona el elemento [UserId] del usuario autenticado y [MobileServiceAuthenticationToken] como JSON Web Token (JWT). El token puede almacenarse en caché y volver a usarse hasta que expire. Para obtener más información, consulte [Almacenamiento en caché del token de autenticación](#caching).

### <a name="caching"></a>Almacenamiento en caché del token de autenticación
En algunos casos, la llamada al método de inicio de sesión se puede evitar tras la primera autenticación correcta. Para ello, es preciso almacenar el token de autenticación del proveedor.  Las aplicaciones de Tienda Windows y UWP pueden usar [PasswordVault] para almacenar en caché el token de autenticación actual después de un inicio de sesión correcto, como se indica a continuación:

```
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

El valor de UserId se almacena como el nombre de usuario de la credencial y el token se almacena como la contraseña. En los inicios posteriores, puede comprobar si **PasswordVault** tiene credenciales almacenadas en caché. En el ejemplo siguiente se utilizan credenciales almacenadas en la caché cuando se encuentran y también se intenta volver a realizar la autenticación con el back-end:

```
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Cuando se cierre la sesión de un usuario, también es preciso quitar la credencial almacenada, tal y como se muestra a continuación:

```
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Las aplicaciones de Xamarin utilizan las API de [Xamarin.Auth] para almacenar de forma segura las credenciales en un objeto **Account**. Para ver un ejemplo de cómo usar estas API, consulte el archivo de código [AuthStore.cs] en el [ejemplo de uso compartido de fotografías ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Si utiliza la autenticación administrada por el cliente, también puede almacenar en caché el token de acceso obtenido del proveedor, como Facebook o Twitter. Este token se puede especificar al solicitar un nuevo token de autenticación del back-end, tal como se muestra a continuación:

```
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Notificaciones push
Los siguientes temas tratan sobre las notificaciones push:

* [Registro de notificaciones push](#register-for-push)
* [Obtención del SID de un paquete de la Tienda Windows](#package-sid)
* [Registro con plantillas multiplataforma](#register-xplat)

### <a name="register-for-push"></a>Cómo registrarse para recibir notificaciones push
El cliente de Mobile Apps permite registrar las notificaciones push con Azure Notification Hubs. Al registrar, se obtiene un identificador del servicio de notificaciones push (PNS) específico de la plataforma. A continuación, proporcione este valor junto con las etiquetas cuando se cree el registro. El código siguiente registra la aplicación de Windows para las notificaciones push en el Servicio de notificaciones de Windows.(WNS):

```
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Si va a insertar en WNS, DEBE [obtener un SID del paquete de Tienda Windows](#package-sid).  Para más información sobre las aplicaciones de Windows, incluyendo cómo registrarse para los registros de plantillas, vea [Agregar notificaciones de inserción a la aplicación].

Tenga en cuenta que no se admite la solicitud de etiquetas del cliente.  Las solicitudes de etiquetas se quitan del registro en modo silencioso.
Si desea registrar el dispositivo con etiquetas, crear una API personalizada que use la API de Notification Hubs para realizar el registro en su nombre.  [Llame a la API personalizada](#customapi), en lugar de al método `RegisterNativeAsync()`.

### <a name="package-sid"></a>Obtención del SID de un paquete de la Tienda Windows.
Se necesita un SID de paquete para habilitar las notificaciones push en aplicaciones de la Tienda Windows.  Para recibir un SID del paquete. registre la aplicación en la Tienda Windows.

Para obtener este valor:

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación de la Tienda Windows y haga clic en **Tienda** > **Asociar aplicación con la Tienda...**.
2. En el asistente, haga clic en **Siguiente**, inicie sesión con su cuenta Microsoft, escriba un nombre para la aplicación en **Reserve un nuevo nombre de aplicación** y haga clic en **Reservar**.
3. Después de que el registro de la aplicación se cree correctamente, seleccione el nombre de la aplicación, haga clic en **Siguiente** y, después, en **Asociar**.
4. Inicie sesión en el [Centro de desarrollo de Windows] con su cuenta Microsoft. En **Mis aplicaciones**, haga clic en el registro de la aplicación que ha creado.
5. Haga clic en **Administración de la aplicación** > **Identidad de la aplicación** y, después, desplácese hacia abajo para buscar el **SID del paquete**.

Muchos usos del SID del paquete lo tratan como un URI, en cuyo caso debe usar *ms-app://* como esquema. Tome nota de la versión del SID del paquete que se forma concatenando este valor como prefijo.

Las aplicaciones de Xamarin requieren más código para poder registrar una aplicación que se ejecute en las plataformas Android o iOS. Para obtener más información, consulte el tema sobre su plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Procedimiento: Registro de plantillas push para enviar notificaciones entre plataformas
Para registrar plantillas, use el método `RegisterAsync()` con ellas, tal y como se indica a continuación:

```
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Las plantillas serán de tipo `JObject` y pueden contener varias plantillas con el formato JSON siguiente:

```
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

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
```

El método **RegisterAsync()** también acepta iconos secundarios:

```
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Por seguridad, todas las etiquetas se eliminan durante el registro. Para agregar etiquetas a las instalaciones o plantillas dentro de las instalaciones, consulte [Trabajar con el SDK del servidor back-end de .NET para Azure Mobile Apps].

Para enviar notificaciones mediante estas plantillas registradas, consulte [API de Notification Hubs].

## <a name="misc"></a>Temas variados
### <a name="errors"></a>Gestión de errores
Si se produce un error en el back-end, el SDK de cliente generará una excepción `MobileServiceInvalidOperationException`.  En el ejemplo siguiente se muestra cómo controlar una excepción devuelta por el back-end:

```
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
```

Puede encontrar otro ejemplo de cómo tratar las condiciones de error en el [ejemplo de archivos de Mobile Apps]. El ejemplo de [LoggingHandler] proporciona un controlador delegado de registro para registrar las solicitudes realizadas al back-end.

### <a name="headers"></a>Personalización de encabezados de solicitud
Para admitir su escenario de aplicación específico, deberá personalizar la comunicación con el back-end de la aplicación móvil. Por ejemplo, es posible que desee agregar un encabezado personalizado a cada solicitud saliente o cambiar los códigos de estado de las respuestas. Puede hacer esto proporcionando un elemento [DelegatingHandler]personalizado, como en el ejemplo siguiente:

```
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
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
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Incorporación de la autenticación a su aplicación]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronización de datos sin conexión en Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Agregar notificaciones de inserción a la aplicación]: app-service-mobile-windows-store-dotnet-get-started-push.md
[registrar la aplicación para usar el inicio de sesión de la cuenta Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Configuración de App Service para usar el inicio de sesión de Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[crea una referencia a una tabla sin tipo]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro de desarrollo de Windows]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[SDK de Windows Live]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API de Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[ejemplo de archivos de Mobile Apps]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[documentación de OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
