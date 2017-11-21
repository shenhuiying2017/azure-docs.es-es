<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Administrar entidades de tabla

Ya tenemos la tabla. Pasemos a ver ahora cómo administrar las entidades (o filas) de esa tabla. 

Una entidad puede tener hasta 255 propiedades, incluyendo 3 propiedades de sistema: **PartitionKey**, **RowKey** y **Timestamp**. Usted será el que deba encargarse de insertar y actualizar los valores de **PartitionKey** y **RowKey**. El servidor se encargará de administrar el valor **Timestamp**, así que no podrá modificarlo. Tanto **PartitionKey** como **RowKey** identifican de forma exclusiva todas las entidades de una tabla.

* **PartitionKey**: determina la partición en la que se almacena la entidad.
* **RowKey**: identifica de forma única la entidad dentro de la partición.

Puede definir hasta 252 propiedades personalizadas para una entidad. 

### <a name="add-table-entities"></a>Agregar entidades de tabla

Agregue entidades a una tabla con **Add-StorageTableRow**. En estos ejemplos se usan claves de partición con los valores "partition1" y "partition2" y las claves de fila equivalen a abreviaturas de estados americanos. Las propiedades de cada entidad son el nombre y el identificador de usuario. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Consultar las entidades de tabla

Existen diversas formas de consultar las entidades de una tabla.

#### <a name="retrieve-all-entities"></a>Recuperar todas las entidades

Use **Get-AzureStorageTableRowAll** para recuperar todas las entidades.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Este comando devuelve un valor similar a la siguiente tabla:

| userid | nombre de usuario | partición | clave de fila |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Recuperar entidades de una partición específica

Use **Get-AzureStorageTableRowByPartitionKey** para recuperar todas las entidades de una partición concreta.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
El resultado es similar a la siguiente tabla:

| userid | nombre de usuario | partición | clave de fila |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Recuperar entidades con un valor específico en una columna específica

Use **AzureStorageTableRowByColumnName Get** para recuperar las entidades en las que el valor de una columna específica es igual a un valor particular.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Con esta consulta se recupera un único registro.

|campo|value|
|----|----|
| userid | 1 |
| nombre de usuario | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Recuperar entidades con un filtro personalizado 

Use **Get-AzureStorageTableRowByCustomFilter** para recuperar entidades con un filtro personalizado.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq '1')"
```

Con esta consulta se recupera un único registro.

|campo|value|
|----|----|
| userid | 1 |
| nombre de usuario | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Actualización de entidades 

La actualización de las entidades es un proceso de tres pasos: primero se recupera la entidad que se va a cambiar, después se realiza el cambio y, finalmente, se confirma el cambio con **Update-AzureStorageTableRow**.

Actualice la entidad con el nombre de usuario = "Jessie" para que refleje el nombre de usuario = "Jessie2". En este ejemplo se indica también otra forma de crear un filtro personalizado con tipos de .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

En los resultados aparece el registro Jessie2.

|campo|value|
|----|----|
| userid | 2 |
| nombre de usuario | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Eliminar entidades de tabla

Se puede eliminar una o todas las entidades de la tabla.

#### <a name="deleting-one-entity"></a>Eliminar una entidad

Para eliminar solo una entidad, obtenga una referencia a esa entidad y canalícela en **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Eliminar todas las entidades de la tabla 

Para eliminar todas las entidades de la tabla, hay que recuperarlas y, después, canalizar los resultados en el cmdlet de eliminación. 

```powershell
# Get all rows and pipe it into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```