##<a name="create-client"></a>Creación del cliente

Cree una conexión de cliente mediante la generación de un objeto `WindowsAzure.MobileServicesClient`. Sustituya `appUrl` por la dirección URL de la aplicación móvil.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Creación de una referencia de tabla

Para acceder a los datos o actualizarlos, cree una referencia a la tabla de back-end. Reemplace `tableName` por el nombre de la tabla.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Consulta de una referencia de tabla

Una vez que tiene una referencia de tabla, puede utilizarla para consultar datos en el servidor. Las consultas se realizan en un lenguaje "similar a LINQ". Para devolver todos los datos de la tabla, utilice lo siguiente:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Se llama a la función success con los resultados. No use `for (var i in results)` en la función success, dado que se efectuaría una iteración en la información incluida en los resultados al utilizar otras funciones de consulta (como `.includeTotalCount()`).

Para obtener más información sobre la sintaxis de consulta, consulte la [documentación de objetos de consulta].

### Filtrado de datos en el servidor

Puede usar una cláusula `where` en la referencia de tabla:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

También puede utilizar una función que filtre el objeto. En este caso, la variable `this` se asigna al objeto actual que se está filtrando. El siguiente código es funcionalmente equivalente al ejemplo anterior:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Paginación mediante datos

Utilice los métodos take() y skip(). Por ejemplo, si desea dividir la tabla en registros de 100 filas:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

El método `.includeTotalCount()` se utiliza para agregar un campo totalCount al objeto de resultados. El campo totalCount se rellena con el número total de registros que se devolverían si no se utilizara ninguna paginación.

A continuación, puede usar la variable de páginas y algunos botones de la interfaz de usuario para proporcionar una lista de páginas; utilice loadPage() para cargar los nuevos registros de cada página. Debe implementar algún tipo de almacenamiento en caché para acelerar el acceso a los registros que ya se han cargado.


###<a name="sorting-data"></a>Devolución de los datos ordenados

Utilice los métodos de consulta .orderBy() o .orderByDescending():

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Para obtener más información sobre el objeto de consulta, lea la [documentación de objetos de consulta].

##<a name="inserting"></a>Insertar datos

Cree un objeto de JavaScript con la fecha adecuada y llame a table.insert() de manera asincrónica:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Tras la inserción correcta, el elemento insertado se devuelve con los campos adicionales que son necesarios para las operaciones de sincronización. Debe actualizar su propia caché con esta información para actualizaciones posteriores.

Tenga en cuenta que el SDK de servidor de Node.js para Aplicaciones móviles de Azure admite el esquema dinámico con fines de desarrollo. En el caso del esquema dinámico, el esquema de la tabla se actualiza sobre la marcha, lo que permite agregar columnas a la tabla con solo especificarlas en una operación de inserción o de actualización. Se recomienda desactivar el esquema dinámico antes de pasar la aplicación a producción.

##<a name="modifying"></a>Modificación de datos

De forma similar al método .insert(), debe crear un objeto Update y luego llamar a .update(). El objeto Update debe contener el identificador del registro que se va a actualizar; este se obtiene al leer el registro o al llamar a .insert().

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Eliminación de datos

Llame al método .del() para eliminar un registro. Pase el identificador de una referencia de objeto:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

<!----HONumber=AcomDC_0309_2016-->
