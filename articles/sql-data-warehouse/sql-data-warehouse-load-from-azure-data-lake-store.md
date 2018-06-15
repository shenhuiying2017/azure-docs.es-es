---
title: 'Tutorial: Carga de datos de Azure Data Lake Store a Azure SQL Data Warehouse | Microsoft Docs'
description: Use tablas externas de PolyBase para cargar datos de Azure Data Lake Store en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: c6030d1951c22dddfe6df01225c63cf503a370ac
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188385"
---
# <a name="load-data-from-azure-data-lake-store-to-sql-data-warehouse"></a>Carga de datos de Azure Data Lake Store a  SQL Data Warehouse
Use tablas externas de PolyBase para cargar datos de Azure Data Lake Store en Azure SQL Data Warehouse. Aunque puede ejecutar consultas ad hoc en datos almacenados en ADLS, se recomienda importar los datos en SQL Data Warehouse para obtener un mejor rendimiento.

> [!div class="checklist"]
> * Crear objetos de base de datos necesarios para cargar desde Azure Data Lake Store.
> * Conectarse a un directorio de Azure Data Lake Store
> * Cargar datos en Azure SQL Data Warehouse

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="before-you-begin"></a>Antes de empezar
Antes de completar este tutorial, descargue e instale la versión más reciente de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

Para ejecutar este tutorial, necesitará:

* La aplicación Azure Active Directory, que se usará para la autenticación entre servicios. Para la creación, siga [Autenticación con Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)

>[!NOTE] 
> Se necesitan el identificador del cliente, la clave y el valor del punto de conexión del token de OAuth2.0 de su aplicación Active Directory para conectarse a su instancia de Azure Data Lake desde SQL Data Warehouse. En el vínculo siguiente se detalla cómo obtener estos valores. Para el registro de aplicación de Azure Active Directory, use el identificador de aplicación como identificador de cliente.
> 

* Una instancia de Azure SQL Data Warehouse Consulte [Guía de inicio rápido: Creación de una instancia de Azure SQL Data Warehouse en Azure Portal, y realización de consultas en ella](create-data-warehouse-portal.md).

* Una instancia de Azure Data Lake Store. Consulte [Introducción al uso de Azure Portal por parte de Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md). 

##  <a name="create-a-credential"></a>Creación de una credencial
Para acceder a su instancia de Azure Data Lake Store, debe crear una clave maestra de base de datos para cifrar su secreto de credencial que se usa en el paso siguiente. Después, cree una credencial con ámbito de base de datos, que almacena las credenciales de la entidad de servicio configuradas en AAD. Para aquellos que han usado PolyBase para conectarse a Microsoft Azure Storage Blob, hay que tener en cuenta que la sintaxis de las credenciales es diferente.

Para conectarse a Azure Data Lake Store, **primero** debe crear una aplicación de Azure Active Directory, crear una clave de acceso y conceder acceso a la aplicación al recurso de Azure Data Lake. Para obtener instrucciones, consulte [Autenticarse en Azure Data Lake Store mediante Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Creación del origen de datos externo
Utilice el comando [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) para almacenar la ubicación de los datos. 

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```

## <a name="configure-data-format"></a>Configuración del formato de datos
Para importar los datos de ADLS, es preciso especificar el formato de archivo externo. Este objeto define cómo se escriben los archivos en ADLS.
Para obtener una lista completa, consulte la documentación de T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Creación de las tablas externas
Ahora que ha especificado el origen de datos y el formato de archivo, está listo para crear las tablas externas. Las tablas externas indican cómo se interactúa con los datos externos. El parámetro de ubicación puede especificar un archivo o un directorio. Si especifica un directorio, se cargarán todos los archivos que contenga.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Consideraciones de las tablas externas
Crear una tabla externa es fácil, pero hay algunos matices que deben tratarse.

Las tablas externas están fuertemente tipadas. Esto significa que cada fila de los datos que se van a ingerir debe satisfacer la definición de esquema de tabla.
Si una fila no coincide con la definición de esquema, se rechaza de la carga.

Las opciones REJECT_TYPE y REJECT_VALUE le permiten definir cuántas filas o qué porcentaje de los datos deben estar presentes en la tabla final. Durante la carga, si se alcanza el valor de rechazo, se produce un error en la carga. La causa más común de filas rechazadas es un error de coincidencia de la definición de esquema. Por ejemplo, si una columna especifica incorrectamente el esquema de int cuando los datos del archivo son una cadena, cada fila se producirá un error al cargar.

 Azure Data Lake Store usa el control de acceso basado en rol (RBAC) para controlar el acceso a los datos. Esto significa que la entidad de servicio debe tener permisos de lectura para los directorios que se definen en el parámetro de ubicación y para los elementos secundarios de los archivos y los directorios finales. De esta forma, PolyBase puede autenticar y cargar esos datos. 

## <a name="load-the-data"></a>Carga de los datos
Para cargar los datos de Azure Data Lake Store, use la instrucción [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse). 

CTAS crea una nueva tabla y la rellena con los resultados de una instrucción SELECT. CTAS define la nueva tabla para tener las mismas columnas y tipos de datos que los resultados de la instrucción SELECT. Si selecciona todas las columnas de una tabla externa, la nueva tabla es una réplica de las columnas y los tipos de datos de la tabla externa.

En este ejemplo, vamos a crear una tabla de hash distribuida llamada DimProduct desde nuestra tabla externa DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>Optimización de compresión de almacén de columnas
De forma predeterminada, SQL Data Warehouse almacena la tabla como índice de almacén de columnas agrupado. Una vez completada una carga, puede que algunas de las filas de datos no se compriman en el almacén de columnas.  Existen varios motivos por los que esto puede ocurrir. Para aprender más, consulte el artículo sobre [administración de índices de almacén de columnas](sql-data-warehouse-tables-index.md).

Para optimizar el rendimiento de las consultas y la compresión de almacén de columnas después de una carga, vuelva a crear la tabla para obligar al índice de almacén de columnas a comprimir todas las filas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optimización de estadísticas
Es mejor crear estadísticas de columna única inmediatamente después de una carga. Hay algunas opciones para las estadísticas. Por ejemplo, si crea estadísticas de columna única en cada columna, la recompilación de todas las estadísticas puede llevar mucho tiempo. Si sabe que algunas columnas no van a estar en predicados de consulta, puede omitir la creación de estadísticas en dichas columnas.

Si decide crear estadísticas de columna única en todas las columnas de cada una de las tablas, puede usar el ejemplo de código de procedimiento almacenado `prc_sqldw_create_stats` del artículo sobre [estadísticas](sql-data-warehouse-tables-statistics.md).

El ejemplo siguiente es un buen punto de partida para la creación de estadísticas. Crea estadísticas de columna única en cada una de las columnas de la tabla de dimensiones y en cada una de las columnas de combinación de las tablas de hechos. Siempre puede agregar estadísticas de columna única o de varias columnas a otras columnas de la tabla de hechos más adelante.

## <a name="achievement-unlocked"></a>Logro conseguido.
Ha cargado correctamente datos en Azure SQL Data Warehouse. Buen trabajo.

## <a name="next-steps"></a>Pasos siguientes 
En este tutorial, creó tablas externas para definir la estructura de los datos almacenados en Azure Data Lake Store y, luego, utilizó la instrucción CREATE TABLE AS SELECT de PolyBase para cargar datos en el almacenamiento de datos. 

Hizo todo esto:
> [!div class="checklist"]
> * Creó objetos de base de datos necesarios para cargar datos desde Azure Data Lake Store.
> * Se conectó a un directorio de Azure Data Lake Store.
> * Cargó datos en Azure SQL Data Warehouse.
> 

La carga de datos es el primer paso para desarrollar una solución de almacenamiento de datos mediante SQL Data Warehouse. Consulte nuestros recursos de desarrollo.

> [!div class="nextstepaction"]
>[Obtenga información acerca de cómo desarrollar tablas en el SQL Data Warehouse](sql-data-warehouse-tables-overview.md)




