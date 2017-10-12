---
title: Copia de datos con SQL Server como origen o destino mediante Azure Data Factory | Microsoft Docs
description: "Aprenda a mover los datos hacia y desde una base de datos SQL Server en un entorno local o en una máquina virtual de Azure mediante Factoría de datos de Azure."
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
ms.date: 09/30/2017
ms.author: jingwang
ms.openlocfilehash: 13297eafd957d926dfd311a14c4ba9351e55e2bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Copia de datos con SQL Server como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: disponibilidad general](v1/data-factory-sqlserver-connector.md)
> * [Versión 2: versión preliminar](connector-sql-server.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con una base de datos SQL Server como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte el artículo sobre [la versión 1 del conector SQL Server](v1/data-factory-sqlserver-connector.md).

## <a name="supported-scenarios"></a>Escenarios admitidos

Puede copiar datos con una base de datos SQL Server como origen o destino a cualquier almacén de datos de recepto compatible o copiar datos desde cualquier almacén de datos de origen compatible a una base de datos SQL Server. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector SQL Server admite las siguientes funcionalidades:

- SQL Server versión 2016, 2014, 2012, 2008 R2, 2008 y 2005
- La copia de datos con autenticación de **SQL** o **Windows**.
- Como origen, recuperar datos mediante consultas SQL o un procedimiento almacenado.
- Como receptor, anexar datos a la tabla de destino o invocar un procedimiento almacenado con lógica personalizada durante la copia.

## <a name="prerequisites"></a>Requisitos previos

Para usar los datos de la copia de una base de datos SQL Server que no es accesible públicamente, debe configurar un entorno Integration Runtime autohospedado. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información. Integration Runtime proporciona un controlador de base de datos SQL Server integrado, por lo tanto, no es necesario instalar manualmente los controladores cuando se copian datos con la base de datos SQL Server como origen o destino.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de base de datos SQL Server.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las propiedades siguientes son compatibles con el servicio vinculado SQL Server:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type se debe establecer en: **SqlServer** | Sí |
| connectionString |Especifique la información de connectionString necesaria para conectarse a la base de datos SQL Server mediante autenticación de SQL o autenticación de Windows. Marque este campo como SecureString. |Sí |
| userName |Especifique el nombre de usuario si usa la autenticación de Windows. Ejemplo: **nombreDeDominio\\nombreDeUsuario**. |No |
| Contraseña |Especifique la contraseña de la cuenta de usuario que se especificó para el nombre de usuario. Marque este campo como SecureString. |No |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo 1: con autenticación de SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo 2: con autenticación de Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
             "userName": "<domain\\username>",
             "password": {
                "type": "SecureString",
                "value": "<password>"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de SQL Server.

Para copiar datos con la base de datos SQL Server como origen o destino, establezca la propiedad type del conjunto de datos en **SqlServerTable**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos se debe establecer en: **SqlServerTable** | Sí |
| tableName |Nombre de la tabla en la instancia de base de datos SQL Server a la que hace referencia el servicio vinculado. | Sí |

**Ejemplo:**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el origen y receptor de SQL Server.

### <a name="sql-server-as-source"></a>SQL Server como origen

Si va a copiar datos desde SQL Server, establezca el tipo de origen de la actividad de copia en **SqlSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **SqlSource** | Sí |
| SqlReaderQuery |Use la consulta SQL personalizada para leer los datos. Ejemplo: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Nombre del procedimiento almacenado que lee datos de la tabla de origen. La última instrucción SQL debe ser una instrucción SELECT del procedimiento almacenado. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |

**Puntos a tener en cuenta:**

- Si se especifica **sqlReaderQuery** para SqlSource, la actividad de copia ejecuta la consulta en el origen de SQL Server para obtener los datos. Como alternativa, puede indicar un procedimiento almacenado mediante la especificación de **sqlReaderStoredProcedureName** y **storedProcedureParameters** (si el procedimiento almacenado toma parámetros).
- Si no especifica "sqlReaderQuery" ni "sqlReaderStoredProcedureName", las columnas que se definen en la sección "structure" del JSON del conjunto de datos se usan para construir una consulta (`select column1, column2 from mytable`) para ejecutarla en SQL Server. Si la definición del conjunto de datos no tiene la sección "structure", se seleccionan todas las columnas de la tabla.
- Cuando use **sqlReaderStoredProcedureName**, necesitará especificar un valor para la propiedad ficticia **tableName** del JSON del conjunto de datos.

**Ejemplo: con la consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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

### <a name="sql-server-as-sink"></a>SQL Server como receptor

Si va a copiar datos en SQL Server, establezca el tipo de receptor de la actividad de copia en **SqlSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **SqlSink** | Sí |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize.<br/>Los valores permitidos son: enteros (número de filas). |No (valor predeterminado = 10000) |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:30:00" (30 minutos). |No |
| sqlWriterStoredProcedureName |Nombre del procedimiento almacenado que actualiza e inserta (operación de upsert) datos en la tabla de destino. |No |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |No |
| sqlWriterTableType |Especifique el nombre del tipo de tabla que se usará en el procedimiento almacenado anterior. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |No |
| preCopyScript |Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en SQL Server en cada ejecución. Puede usar esta propiedad para limpiar los datos cargados previamente. |No |

> [!TIP]
> Cuando se copian datos en SQL Server, la actividad de copia anexa datos a la tabla receptora de forma predeterminada. Para llevar a cabo una operación UPSERT o una lógica de negocios adicional, use el procedimiento almacenado de SqlSink. Obtenga más información en [Invocación del procedimiento almacenado para el receptor de SQL](#invoking-stored-procedure-for-sql-sink).

**Ejemplo 1: Anexo de datos**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
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
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
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
        "type": " SqlServerTable",
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
        "type": "SqlServerTable",
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

Al copiar datos en una base de datos SQL Server, se puede configurar e invocar un procedimiento almacenado especificado por el usuario con parámetros adicionales.

Cuando los mecanismos de copia integrada no prestan el servicio, se puede usar un procedimiento almacenado. Normalmente, se suele realizar al realizar operaciones UPSERT (actualización e inserción) y procesos adicionales (combinación de columnas, buscar valores adicionales, inserción en varias tablas, etc.) antes de la inserción final de datos de origen en la tabla de destino.

En el ejemplo siguiente se muestra cómo usar un procedimiento almacenado para realizar una operación UPSERT en una tabla en la base de datos SQL Server. Suponiendo que los datos de entrada y la tabla "Marketing" del receptor tiene tres columnas (ProfileID, State y Category), realice una operación UPSERT en función de la columna "ProfileID" y aplíquela solo a una categoría específica.

**Conjunto de datos de salida**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
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

## <a name="data-type-mapping-for-sql-server"></a>Asignación de tipos de datos para SQL Server

Al copiar datos con SQL Server como origen o destino, se usan las siguientes asignaciones de tipos de datos de SQL Server a los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipos de datos de SQL Server | Tipo de datos provisionales de Data Factory |
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

## <a name="troubleshooting-connection-issues"></a>Solución de problemas de conexión

1. Configure su SQL Server para que acepte conexiones remotas. Inicie **SQL Server Management Studio**, haga clic con el botón derecho en **Servidor** y después en **Propiedades**. Seleccione **Conexiones** en la lista y active **Permitir conexiones remotas con este servidor**.

    ![Habilitar conexiones remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Consulte los pasos detallados de [Configurar la opción de configuración del servidor Acceso remoto](https://msdn.microsoft.com/library/ms191464.aspx) .

2. Inicie el **Administrador de configuración de SQL Server**. Expanda **Configuración de red de SQL Server** para la instancia que desee y seleccione **Protocols for MSSQLSERVER** (Protocolos para MSSQLSERVER). Debería ver protocolos en el panel derecho. Para habilitar TCP/IP, haga clic con el botón derecho en **TCP/IP** y haga clic en **Habilitar**.

    ![Habilitar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Consulte [Habilitar o deshabilitar un protocolo de red de servidor](https://msdn.microsoft.com/library/ms191294.aspx) para ver información y maneras alternativas de habilitar el protocolo TCP/IP.

3. En la misma ventana, haga doble clic en **TCP/IP** para abrir la ventana **TCP/IP Properties** (Propiedades de TCP/IP).
4. Cambie a la pestaña **Direcciones IP** . Desplácese hacia abajo hasta la sección **IPAll** . Anote el valor de **Puerto TCP** (el valor predeterminado es **1433**).
5. Cree una **regla del Firewall de Windows** en la máquina para permitir el tráfico entrante a través de este puerto.  
6. **Compruebe la conexión**: use SQL Server Management Studio en una máquina diferente para conectarse a SQL Server con el nombre completo. Por ejemplo: `"<machine>.<domain>.corp.<company>.com,1433"`.


## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.