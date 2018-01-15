---
title: Copiar de datos con Azure SQL Data Warehouse como origen o destino mediante Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde cualquier almacén de origen compatible a Azure SQL Data Warehouse o desde SQL Data Warehouse a cualquier almacén de receptores compatible mediante Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jingwang
ms.openlocfilehash: 9360c0ee90f9a4ffdffd7649505699f656833bbe
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copia de datos con Azure SQL Data Warehouse como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versión 2: versión preliminar](connector-azure-sql-data-warehouse.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con Azure SQL Data Warehouse como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector de Azure SQL Data Warehouse](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos con Azure SQL Data Warehouse como origen o destino a cualquier almacén de datos de receptor compatible o desde cualquier almacén de datos de origen a Azure SQL Data Warehouse. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Azure SQL Data Warehouse admite las siguientes funcionalidades:

- Copia de datos con la autenticación de SQL.
- Como origen, recuperación de datos mediante consultas SQL o un procedimiento almacenado.
- Como receptor, carga de datos mediante **PolyBase** o inserción masiva. Se **recomienda** el primero para mejorar el rendimiento de copia.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Azure SQL Data Warehouse:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **AzureSqlDW** | Sí |
| connectionString |Especifique la información necesaria para conectarse a la instancia de Azure SQL Data Warehouse para la propiedad connectionString. Solo se admite la autenticación básica. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |Sin  |


> [!IMPORTANT]
> Configure el [firewall de Azure SQL Data Warehouse](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) y el servidor de bases de datos para [permitir que los servicios de Azure tengan acceso al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Además, si va a copiar datos en Azure SQL Data Warehouse desde fuera de Azure, incluidos orígenes de datos locales con el entorno Integration Runtime autohospedado, configure el intervalo de direcciones IP adecuado para la máquina que envía datos a Azure SQL Data Warehouse.

**Ejemplo:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. Esta sección proporciona una lista de las propiedades que el conjunto de datos de Azure SQL Data Warehouse admite.

Para copiar datos con Azure SQL Data Warehouse como origen o destino, establezca la propiedad type del conjunto de datos en **AzureSqlDWTable**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **AzureSqlDWTable**. | Sí |
| tableName |Nombre de la tabla o vista en la instancia de Azure SQL Data Warehouse a la que hace referencia el servicio vinculado. | Sí |

**Ejemplo:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). Esta sección proporciona una lista de las propiedades que el origen y el receptor de Azure SQL Data Warehouse admiten.

### <a name="azure-sql-data-warehouse-as-source"></a>Azure SQL Data Warehouse como origen

Para copiar datos desde Azure SQL Data Warehouse, establezca el tipo de origen de la actividad de copia en **SqlDWSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **SqlDWSource**. | Sí |
| SqlReaderQuery |Use la consulta SQL personalizada para leer los datos. Ejemplo: `select * from MyTable`. |Sin  |
| sqlReaderStoredProcedureName |Nombre del procedimiento almacenado que lee datos de la tabla de origen. La última instrucción SQL debe ser una instrucción SELECT del procedimiento almacenado. |Sin  |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |Sin  |

**Puntos a tener en cuenta:**

- Si se especifica **sqlReaderQuery** para SqlSource, la actividad de copia ejecuta la consulta en el origen de Almacenamiento de datos SQL de Azure para obtener los datos. Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
- Si no especifica "sqlReaderQuery" ni "sqlReaderStoredProcedureName", las columnas definidas en la sección "structure" del conjunto de datos JSON se usan para crear una consulta (`select column1, column2 from mytable`) y ejecutarla en Azure SQL Data Warehouse. Si la definición del conjunto de datos no tiene la sección "structure", se seleccionan todas las columnas de la tabla.
- Cuando use **sqlReaderStoredProcedureName**, necesitará especificar un valor para la propiedad ficticia **tableName** del JSON del conjunto de datos.

**Ejemplo: con la consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Ejemplo: con el procedimiento almacenado**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Definición del procedimiento almacenado:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure SQL Data Warehouse como receptor

Para copiar datos a Azure SQL Data Warehouse, establezca el tipo de receptor de la actividad de copia en **SqlDWSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **SqlDWSink**. | Sí |
| allowPolyBase |Indica si se usa PolyBase (si procede) en lugar del mecanismo BULKINSERT. <br/><br/> **El uso de PolyBase es el método recomendado para cargar datos en SQL Data Warehouse.** Consulte [Uso de PolyBase para cargar datos en SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver restricciones y más información.<br/><br/>Los valores permitidos son: **True** (valor predeterminado) y **False**.  |Sin  |
| polyBaseSettings |Un grupo de propiedades que se pueden especificar si el valor de la propiedad **allowPolybase** está establecido en **true**. |Sin  |
| rejectValue |Especifica el número o porcentaje de filas que se pueden rechazar antes de que se produzca un error en la consulta.<br/><br/>Más información sobre las opciones de rechazo de PolyBase en la sección **Argumentos** del tema [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) [CREAR UNA TABLA EXTERNA (Transact-SQL)]. <br/><br/>Los valores permitidos son: 0 (valor predeterminado), 1, 2,... |Sin  |
| rejectType |Especifica si se indica la opción rejectValue como un valor literal o un porcentaje.<br/><br/>Los valores permitidos son: **Value** (valor predeterminado) y **Percentage**. |Sin  |
| rejectSampleValue |Determina el número de filas que se van a recuperar antes de que PolyBase vuelva a calcular el porcentaje de filas rechazadas.<br/><br/>Los valores permitidos son: 1, 2,… |Sí, si el valor de **rejectType** es **percentage** |
| useTypeDefault |Especifica cómo administrar los valores que faltan en archivos de texto delimitados cuando PolyBase recupera datos del archivo de texto.<br/><br/>Más información sobre esta propiedad en la sección de argumentos de [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Los valores válidos son **True** y **False** (valor predeterminado). |Sin  |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize. Se aplica únicamente cuando no se usa PolyBase.<br/><br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera. Se aplica únicamente cuando no se usa PolyBase.<br/><br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:30:00" (30 minutos). |Sin  |
| preCopyScript |Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en Azure SQL Data Warehouse en cada ejecución. Puede usar esta propiedad para limpiar los datos cargados previamente. |Sin  |(#repeatability-during-copy). |Una instrucción de consulta. |Sin  |

**Ejemplo:**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

En la siguiente sección obtendrá más información sobre cómo usar PolyBase para cargar en SQL Data Warehouse eficazmente.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Movimiento de datos hacia y desde Azure SQL Data Warehouse mediante Azure Data Factory

Usar **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** es una manera eficaz de cargar grandes cantidades de datos en Azure SQL Data Warehouse con un alto rendimiento. Puede ver una gran mejora en el rendimiento mediante el uso de PolyBase en lugar del mecanismo BULKINSERT predeterminado. Vea la [copia del número de referencia de rendimiento](copy-activity-performance.md#performance-reference) con una comparación detallada. Para un tutorial con un caso de uso, consulte [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](connector-azure-sql-data-warehouse.md) (Carga de 1 TB en Azure SQL Data Warehouse en 15 minutos con Azure Data Factory).

* Si los datos de origen están en **Azure Blob o Azure Data Lake Store**, y el formato es compatible con PolyBase, puede realizar las copias directamente desde Azure SQL Data Warehouse mediante PolyBase. Consulte **[Copias directas con PolyBase](#direct-copy-using-polybase)** para detalles.
* Si el formato y el almacenamiento de datos de origen no es compatible originalmente con PolyBase, puede usar en su lugar la característica **[Copias almacenadas provisionalmente con PolyBase](#staged-copy-using-polybase)**. También proporciona un mejor rendimiento al convertir automáticamente los datos en formato compatible con PolyBase y almacenarlos en Azure Blob Storage. Luego los carga en SQL Data Warehouse.

### <a name="direct-copy-using-polybase"></a>Copias directas con PolyBase

PolyBase de SQL Data Warehouse admite directamente Azure Blob y Azure Data Lake Store (con la entidad de servicio) como origen y con los requisitos de formato de archivo específico. Si el origen de datos cumple los criterios descritos en esta sección, puede realizar las copias directamente desde el almacén de datos de origen a Azure SQL Data Warehouse mediante PolyBase. De lo contrario, puede usar [Copias almacenadas provisionalmente con PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Para copiar datos desde Data Lake Store a SQL Data Warehouse de forma más eficiente, obtenga más información en [Azure Data Factory hace incluso más fácil y cómodo el descubrimiento de información de datos cuando se usa Data Lake Store con SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Si no se cumplen los requisitos, Azure Data Factory comprobará la configuración y volverá automáticamente al mecanismo BULKINSERT para realizar el movimiento de datos.

1. El **servicio de origen vinculado** es de tipo **AzureStorage** o **AzureDataLakeStore**.
2. El **conjunto de datos de entrada** es del tipo **AzureBlob** o **AzureDataLakeStoreFile**, y el tipo de formato de las propiedades `type` es **OrcFormat**, **ParquetFormat** o **TextFormat** con las siguientes configuraciones:

   1. `rowDelimiter` debe ser **\n**.
   2. `nullValue` se establece en **una cadena vacía** ("") o `treatEmptyAsNull` se establece en **true**.
   3. `encodingName` se establece en **utf-8**, que es el valor **predeterminado**.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader` y `skipLineCount` no se especifican.
   5. `compression` puede ser **no compression**, **GZip** o **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. No hay ningún valor `skipHeaderLineCount` en **BlobSource** o **AzureDataLakeStore** para la actividad de copia en la canalización.
4. No hay ningún valor `sliceIdentifierColumnName` en **SqlDWSink** para la actividad de copia en la canalización. (PolyBase garantiza que todos los datos se actualizan o que no se actualiza ninguno en una única ejecución. Para lograr la **capacidad de repetición**, se puede usar `sqlWriterCleanupScript`).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>Copias almacenadas provisionalmente con PolyBase

Si los datos de origen no cumplen los criterios especificados en la sección anterior, puede habilitar la copia de datos a través de un almacenamiento provisional de Azure Blob Storage (no puede ser Premium Storage). En este caso, Azure Data Factory realiza transformaciones automáticamente en los datos para que cumplan con los requisitos de formato de datos de PolyBase, luego usa PolyBase para cargar datos en SQL Data Warehouse y, por último, borra los datos temporales de Blob Storage. Consulte [Copias almacenadas provisionalmente](copy-activity-performance.md#staged-copy) para más información sobre cómo funciona en general la copia de datos por medio de un blob de Azure de almacenamiento provisional.

Para usar esta característica, cree un [servicio vinculado de Azure Storage](connector-azure-blob-storage.md#linked-service-properties) que haga referencia a la cuenta de Azure Storage que tenga el almacenamiento de blobs provisional y, después, especifique las propiedades `enableStaging` y `stagingSettings` de la actividad de copia como se muestra en el siguiente código:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Procedimientos recomendados al usar PolyBase

En las secciones siguientes se proporcionan procedimientos recomendados adicionales a los que se mencionan en [Procedimientos recomendados para Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Permiso de base de datos necesario

El uso de PolyBase requiere que el usuario que se usa para cargar datos en SQL Data Warehouse tenga [permiso "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) en la base de datos de destino. Una manera de conseguirlo es agregar ese usuario como miembro del rol "db_owner". Obtenga información sobre cómo hacerlo siguiendo [esta sección](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Limitación del tipo de datos y del tamaño de fila

Las cargas de PolyBase se limitan a la carga de filas más pequeñas que **1 MB** y no pueden cargar en VARCHR(MAX), NVARCHAR(MAX) ni VARBINARY(MAX). O haga clic [aquí](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Si tiene datos de origen con filas mayores de 1 MB, puede dividir las tablas de origen verticalmente en varias más pequeñas, donde el tamaño de fila mayor de cada una de ellas no supere el límite. Las tablas más pequeñas se pueden cargadas con PolyBase y se combinan en Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Clase de recursos de SQL Data Warehouse

Para obtener el mejor rendimiento posible, considere la posibilidad de asignar una clase SQL Data Warehouse a través de PolyBase. Infórmese acerca de cómo hacerlo siguiendo las instrucciones en [Cambio de ejemplo de clase de recursos de usuario](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName en Azure SQL Data Warehouse

En la tabla siguiente se proporcionan ejemplos sobre cómo especificar la propiedad **tableName** en el conjunto de datos JSON para diversas combinaciones de nombre de tabla y esquema.

| Esquema de base de datos | Nombre de tabla | Propiedad JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable o [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] o [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Si ve el siguiente error, podría deberse a un problema con el valor especificado para la propiedad tableName. Consulte en la tabla la forma correcta de especificar los valores para la propiedad tableName de JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Columnas con valores predeterminados

Actualmente, la característica PolyBase en Data Factory solo acepta el mismo número de columnas que la tabla de destino. Supongamos que tiene una tabla con cuatro columnas y una de ellas está definida con un valor predeterminado. Los datos de entrada deberían seguir conteniendo cuatro columnas. Si se proporciona un conjunto de datos de entrada de tres columnas, se producirá un error parecido al siguiente mensaje:

```
All columns of the table must be specified in the INSERT BULK statement.
```

El valor NULL es una forma especial de valor predeterminado. Si la columna admite valores NULL, los datos de entrada (en el blob) para esa columna pueden estar vacíos (no pueden faltar en el conjunto de datos de entrada). PolyBase insertará valores NULL para ellos en Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Asignación de tipos de datos para Azure SQL Data Warehouse

Al copiar datos con Azure SQL Data Warehouse como origen o destino, se utilizan las siguientes asignaciones de tipos de datos de Azure SQL Data Warehouse en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de Azure SQL Data Warehouse | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |boolean |
| char |String, Char[] |
| fecha |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| DECIMAL |DECIMAL |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Doble |
| imagen |Byte[] |
| int |Int32 |
| money |DECIMAL |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |DECIMAL |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |DECIMAL |
| sql_variant |Object * |
| text |String, Char[] |
| Twitter en tiempo |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.