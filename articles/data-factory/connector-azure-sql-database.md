---
title: "Información sobre cómo copiar datos con una instancia de Azure SQL Database como origen o destino mediante Azure Data Factory | Microsoft Docs"
description: "Con Data Factory, puede copiar datos desde cualquier almacén de datos de origen compatible a Azure SQL Database o desde Azure SQL Database a cualquier almacén de datos receptor compatible."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: 16d03e5235999ec284d5ca622c1ea88151774bfd
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copia de datos con una instancia de Azure SQL Database como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-azure-sql-connector.md)
> * [Versión 2: Versión preliminar](connector-azure-sql-database.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con una instancia de Azure SQL Database como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector de Azure SQL Database](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar desde Azure SQL Database a cualquier almacén de datos receptor compatible, o viceversa. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Azure SQL Database admite las siguientes funcionalidades:

- Copiar datos con la autenticación de SQL.
- Como origen, recuperar datos mediante consultas SQL o un procedimiento almacenado.
- Como receptor, anexar datos a la tabla de destino o invocar un procedimiento almacenado con lógica personalizada durante la copia.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Azure SQL Database.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Azure SQL Database:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureSqlDatabase** | Sí |
| connectionString |Especifique la información necesaria para conectarse a la instancia de Azure SQL Database para la propiedad connectionString. Solo se admite la autenticación básica. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |No |

> [!IMPORTANT]
> Configure el [firewall de Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) y el servidor de bases de datos para [permitir que los servicios de Azure accedan al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Además, si va a copiar datos en Azure SQL Database desde fuera de Azure, por ejemplo, a partir de orígenes de datos locales con el entorno Integration Runtime (autohospedado) de Data Factory, configure el intervalo de direcciones IP adecuado para la máquina que envía datos a Azure SQL Database.

**Ejemplo:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Azure SQL Database.

Para copiar datos con Azure SQL Database como origen o destino, establezca la propiedad type del conjunto de datos en **AzureSqlTable**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **AzureSqlTable**. | Sí |
| tableName |Nombre de la tabla o vista en la instancia de Azure SQL Database a la que hace referencia el servicio vinculado. | Sí |

**Ejemplo:**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el origen y el receptor de Azure SQL Database.

### <a name="azure-sql-database-as-source"></a>Azure SQL Database como origen

Si va a copiar datos desde Azure SQL Database, establezca el tipo de origen de la actividad de copia en **SqlSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **SqlSource** | Sí |
| SqlReaderQuery |Use la consulta SQL personalizada para leer los datos. Ejemplo: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Nombre del procedimiento almacenado que lee datos de la tabla de origen. La última instrucción SQL debe ser una instrucción SELECT del procedimiento almacenado. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |

**Puntos a tener en cuenta:**

- Si se especifica **sqlReaderQuery** para SqlSource, la actividad de copia ejecuta la consulta en el origen de Azure SQL Database para obtener los datos. Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
- Si no especifica "sqlReaderQuery" ni "sqlReaderStoredProcedureName", las columnas definidas en la sección "structure" (estructura) del conjunto de datos JSON se usan para crear una consulta (`select column1, column2 from mytable`) y ejecutarla en Azure SQL Database. Si la definición del conjunto de datos no tiene la estructura, se seleccionan todas las columnas de la tabla.
- Cuando use **sqlReaderStoredProcedureName**, necesitará especificar un valor para la propiedad ficticia **tableName** del JSON del conjunto de datos.

**Ejemplo: con la consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-sink"></a>Azure SQL Database como receptor

Si va a copiar datos en Azure SQL Database, establezca el tipo de receptor de la actividad de copia en **SqlSource**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **SqlSink** | Sí |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:30:00" (30 minutos). |No |
| sqlWriterStoredProcedureName |Nombre del procedimiento almacenado que actualiza e inserta (operación de upsert) datos en la tabla de destino. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |
| sqlWriterTableType |Especifique el nombre del tipo de tabla que se usará en el procedimiento almacenado anterior. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |No |
| preCopyScript |Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en Azure SQL Database en cada ejecución. Puede usar esta propiedad para limpiar los datos cargados previamente. |No |

> [!TIP]
> Cuando se copien datos en Azure SQL Database, la actividad de copia anexa datos a la tabla del receptor de forma predeterminada. Para llevar a cabo una operación UPSERT o una lógica de negocios adicional, use el procedimiento almacenado de SqlSink. Obtenga más información en [Invocación del procedimiento almacenado para el receptor de SQL](#invoking-stored-procedure-for-sql-sink).

**Ejemplo 1: Anexo de datos**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Ejemplo 2: Invocación de un procedimiento almacenado durante la copia para realizar la operación UPSERT**

Obtenga más información en [Invocación del procedimiento almacenado para el receptor de SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Columnas de identidad en la base de datos de destino

En esta sección se proporciona un ejemplo para copiar datos de una tabla de origen sin una columna de identidad en una tabla de destino con una columna de identidad.

**Tabla de origen:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Tabla de destino:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observe que la tabla de destino tiene una columna de identidad.

**Definición de JSON del conjunto de datos de origen**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definición de JSON del conjunto de datos de destino**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Tenga en cuenta que la tabla de origen y de destino tienen un esquema diferente (el destino tiene una columna adicional con identidad). En este escenario, debe especificar la propiedad **structure** de la definición del conjunto de datos de destino, que no incluye la columna de identidad.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Invocación del procedimiento almacenado desde el receptor de SQL

Al copiar datos en Azure SQL Database, se puede configurar e invocar un procedimiento almacenado especificado por el usuario con parámetros adicionales.

Cuando los mecanismos de copia integrada no prestan el servicio, se puede utilizar un procedimiento almacenado. Normalmente, se suele realizar al realizar operaciones UPSERT (actualización e inserción) y procesos adicionales (combinación de columnas, búsqueda de valores adicionales, inserción en varias tablas, etc.) antes de la inserción final de los datos de origen en la tabla de destino.

En el ejemplo siguiente se muestra cómo usar un procedimiento almacenado para realizar una operación UPSERT en una tabla de Azure SQL Database. Supongamos que los datos de entrada y la tabla "Marketing" del receptor tienen tres columnas (ProfileID, State y Category), realice una operación UPSERT en función de la columna "ProfileID" y aplíquela solo a una categoría específica.

**Conjunto de datos de salida**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Defina la sección SqlSink de la actividad de copia como se indica a continuación.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

En la base de datos, defina el procedimiento almacenado con el mismo nombre que SqlWriterStoredProcedureName. De este modo, se administran los datos de entrada desde el origen especificado y se combinan en la tabla de salida. Tenga en cuenta que el nombre del parámetro del procedimiento almacenado tiene que ser el mismo que el de "tableName" definido en el conjunto de datos.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

En la base de datos, defina el tipo de tabla con el mismo nombre que sqlWriterTableType. Tenga en cuenta que el esquema del tipo de tabla debe ser el mismo que el esquema devuelto por los datos de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La característica de procedimiento almacenado aprovecha los [parámetros con valores de tabla](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Asignación de tipos de Azure SQL Database

Al copiar datos con Azure SQL Database como origen o destino, se utilizan las siguientes asignaciones de tipos de datos de Azure SQL Database en los tipos de datos provisionales de Azure Data Factory. Vea el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para obtener información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen en el receptor.

| Tipo de datos de Azure SQL Database | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Booleano |
| char |String, Char[] |
| fecha |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Doble |
| imagen |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| Twitter en tiempo |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.