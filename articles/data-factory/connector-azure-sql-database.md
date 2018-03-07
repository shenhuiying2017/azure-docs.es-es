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
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: a4d2ccb4b4ba27983537f26e66b5c279f427d466
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copia de datos con una instancia de Azure SQL Database como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-azure-sql-connector.md)
> * [Versión 2: versión preliminar](connector-azure-sql-database.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con una instancia de Azure SQL Database como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector de Azure SQL Database](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar desde Azure SQL Database a cualquier almacén de datos receptor compatible, o viceversa. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Azure SQL Database admite las siguientes funcionalidades:

- Copiar datos mediante la **autenticación de SQL** y la **autenticación de token de aplicación de Azure Active Directory** con la entidad de servicio o la Identidad de servicio administrada (MSI).
- Como origen, recuperación de datos mediante consultas SQL o un procedimiento almacenado.
- Como receptor, anexar datos a la tabla de destino o invocar un procedimiento almacenado con lógica personalizada durante la copia.

> [!IMPORTANT]
> Si copia los datos usando Azure Integration Runtime, configure el [firewall de Azure SQL Server ](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para [permitir que los servicios de Azure accedan al servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Si copia los datos usando Integration Runtime autohospedado, configure el firewall de Azure SQL Server para permitir el intervalo de direcciones IP apropiado, incluida la IP de la máquina que se usa para conectarse a Azure SQL Database.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Azure SQL Database.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Azure SQL Database:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **AzureSqlDatabase** | Sí |
| connectionString |Especifique la información necesaria para conectarse a la instancia de Azure SQL Database para la propiedad connectionString. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). |Sí |
| servicePrincipalId | Especifique el id. de cliente de la aplicación. | Sí, al utilizar la autenticación de AAD con la entidad de servicio. |
| servicePrincipalKey | Especifique la clave de la aplicación. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí, al utilizar la autenticación de AAD con la entidad de servicio. |
| tenant | Especifique la información del inquilino (nombre de dominio o identificador de inquilino) en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí, al utilizar la autenticación de AAD con la entidad de servicio. |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |Sin  |

Para los distintos tipos de autenticación, consulte las secciones siguientes sobre requisitos previos y ejemplos de JSON, respectivamente:

- [Uso de la autenticación de SQL](#using-sql-authentication)
- [Uso de la autenticación mediante tokens de aplicación de AAD: entidad de servicio](#using-service-principal-authentication)
- [Uso de la autenticación mediante tokens de aplicación de AAD: identidad de servicio administrada](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Uso de la autenticación de SQL

**Ejemplo de servicio vinculado que usa la autenticación de SQL:**

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

### <a name="using-service-principal-authentication"></a>Uso de la autenticación de entidad de servicio

Para usar la autenticación de token de aplicación de AAD basada en la entidad de servicio, siga estos pasos:

1. **[Cree una aplicación de Azure Active Directory desde Azure Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Anote el nombre de la aplicación y los siguientes valores; los usará para definir el servicio vinculado:

    - Identificador de aplicación
    - Clave de la aplicación
    - Id. de inquilino

2. **[Aprovisione un administrador de Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** para el servidor Azure SQL Server mediante Azure Portal, si aún no lo ha hecho. El administrador de AAD debe ser un usuario de AAD o un grupo de AAD, pero no puede ser una entidad de servicio. Este paso se realiza para que, en el paso posterior, pueda usar una identidad de AAD para crear un usuario de base de datos independiente para el servicio principal.

3. **Cree un usuario de base de datos independiente para la entidad de servicio** conectándolo a la base de datos en la que desee copiar los datos, o de la que quiera copiarlos, con herramientas como SSMS, con una identidad de AAD que tenga, al menos, un permiso ALTER ANY USER y que ejecute el siguiente comando T-SQL. Obtenga más información sobre el usuario de base de datos de contenedor [aquí](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceder a la entidad de servicio los permisos necesarios** como lo haría normalmente para los usuarios de SQL, por ejemplo, ejecutando lo siguiente:

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. En ADF, configure un servicio vinculado a Azure SQL Database.


**Ejemplo de servicio vinculado que usa la autenticación de entidad de servicio**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Uso de la autenticación de identidades de servicio administradas

Una factoría de datos puede asociarse a una [identidad de servicio administrada (MSI)](data-factory-service-identity.md), que representa esta factoría de datos específica. Puede usar esta identidad de servicio para la autenticación de Azure SQL Database, que permite que esta factoría designada acceda o copie datos de la base de datos o a la base de datos, respectivamente.

Para usar la autenticación de token de aplicación de AAD basada en MSI, siga estos pasos:

1. **Cree un grupo en Azure AD y haga que la identidad MSI de la factoría sea miembro del grupo**.

    a. Busque la identidad de servicio de la factoría de datos en Azure Portal. Vaya a la factoría de datos -> Propiedades -> copie el **IDENTIFICADOR DE IDENTIDAD DE SERVICIO**.

    b. Instale el módulo [PowerShell de Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), inicie sesión usando el comando `Connect-AzureAD` y ejecute los siguientes comandos para crear un grupo y agregar la identidad MSI de la factoría de datos como miembro.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Aprovisione un administrador de Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** para el servidor Azure SQL Server mediante Azure Portal, si aún no lo ha hecho. El administrador de AAD puede ser un usuario o un grupo de AAD. Si concede al grupo con MSI un rol de administrador, omita el paso 3 y el 4, ya que el administrador tendría acceso completo a la base de datos.

3. **Cree un usuario de base de datos independiente para el grupo de AAD** conectándolo a la base de datos en la que desee copiar los datos, o de la que quiera copiarlos, con herramientas como SSMS, con una identidad de AAD que tenga, al menos, un permiso ALTER ANY USER y que ejecute el siguiente comando T-SQL. Obtenga más información sobre el usuario de base de datos de contenedor [aquí](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Conceda al grupo de AAD los permisos necesarios** como lo haría normalmente para los usuarios de SQL, por ejemplo, ejecutando lo siguiente:

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. En ADF, configure un servicio vinculado a Azure SQL Database.

**Ejemplo de servicio vinculado que usa la autenticación de MSI:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **AzureSqlTable**. | Sí |
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

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **SqlSource** | Sí |
| SqlReaderQuery |Use la consulta SQL personalizada para leer los datos. Ejemplo: `select * from MyTable`. |Sin  |
| sqlReaderStoredProcedureName |Nombre del procedimiento almacenado que lee datos de la tabla de origen. La última instrucción SQL debe ser una instrucción SELECT del procedimiento almacenado. |Sin  |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |Sin  |

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

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **SqlSink**. | Sí |
| writeBatchSize |Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout |Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:30:00" (30 minutos). |Sin  |
| preCopyScript |Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en Azure SQL Database. Solo se invocará una vez por cada copia que se ejecute. Puede usar esta propiedad para limpiar los datos cargados previamente. |Sin  |
| sqlWriterStoredProcedureName |Nombre del procedimiento almacenado que define cómo aplicar datos de origen en la tabla de destino, por ejemplo, cómo realizar operaciones Upsert o transformaciones con su propia lógica de negocios. <br/><br/>Tenga en cuenta que este procedimiento almacenado se **invocará por lote**. Si desea realizar una operación que solo se ejecuta una vez y que no tiene nada que ver con los datos de origen, como por ejemplo, eliminar o truncar, use la propiedad `preCopyScript`. |Sin  |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son: pares nombre-valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. |Sin  |
| sqlWriterTableType |Especifique el nombre del tipo de tabla que se usará en el procedimiento almacenado anterior. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |Sin  |

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

Al copiar datos con Azure SQL Database como origen o destino, se utilizan las siguientes asignaciones de tipos de datos de Azure SQL Database en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de Azure SQL Database | Tipo de datos provisionales de Data Factory |
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