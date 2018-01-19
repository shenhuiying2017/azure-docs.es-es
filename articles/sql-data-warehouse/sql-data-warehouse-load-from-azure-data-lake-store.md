---
title: Carga de datos de Azure Data Lake Store a SQL Data Warehouse | Microsoft Docs
description: "Obtenga información acerca de cómo usar tablas externas de PolyBase para cargar datos de Azure Data Lake Store en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 12/14/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: a2a7d15eb51374b828d1d641e0e6754115f7aaf6
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>Carga de datos de Azure Data Lake Store en SQL Data Warehouse
En este documento encontrará los pasos necesarios para cargar datos de Azure Data Lake Store (ADLS) en SQL Data Warehouse mediante PolyBase.
Aunque es posible ejecutar consultas ad hoc en los datos almacenados en ADLS con tablas externas, como procedimiento recomendado se recomienda importar los datos en SQL Data Warehouse.

En este tutorial, aprenderá a:

1. Crear objetos de base de datos externa que se van a cargar desde Azure Data Lake Store
2. Conectarse a un directorio de Azure Data Lake Store
3. Cargar datos en Azure SQL Data Warehouse

## <a name="before-you-begin"></a>Antes de empezar
Para ejecutar este tutorial, necesitará:

* La aplicación Azure Active Directory, que se usará para la autenticación entre servicios. Para la creación, siga [Autenticación con Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)

>[!NOTE] 
> Se necesitan el identificador del cliente, la clave y el valor del punto de conexión del token de OAuth2.0 de su aplicación Active Directory para conectarse a su instancia de Azure Data Lake desde SQL Data Warehouse. En el vínculo siguiente se detalla cómo obtener estos valores.
>Tenga en cuenta que el registro de aplicación de Azure Active Directory usa el "Id. de la aplicación" como identificador de cliente.

* SQL Server Management Studio o SQL Server Data Tools, para descargar SSMS y conectarse consulte [Consulta de SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* Una instancia de Azure SQL Data Warehouse. Para crearla, consulte: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision_

* Una instancia de Azure Data Lake Store. Para crearla, consulte: https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal


###  <a name="create-a-credential"></a>Creación de una credencial
Para acceder a su instancia de Azure Data Lake Store, debe crear una clave maestra de base de datos para cifrar su secreto de credencial que se usa en el paso siguiente.
Después, cree una credencial con ámbito de base de datos, que almacena las credenciales de la entidad de servicio configuradas en AAD. Para aquellos que han usado PolyBase para conectarse a Microsoft Azure Storage Blob, hay que tener en cuenta que la sintaxis de las credenciales es diferente.
Para conectarse a Azure Data Lake Store, **primero** debe crear una aplicación de Azure Active Directory, crear una clave de acceso y conceder acceso a la aplicación al recurso de Azure Data Lake. Las instrucciones para realizar estos pasos se encuentran [aquí](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory).

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

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


### <a name="create-the-external-data-source"></a>Creación del origen de datos externo
Utilice el comando [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] para almacenar la ubicación de los datos. Para encontrar el identificador URI de ADL en Azure Portal, navegue hasta Azure Data Lake Store y examine el panel Essentials.

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
Para importar los datos de ADLS, es preciso especificar el formato de archivo externo. Este comando tiene opciones de formato específicas para describir los datos.
Examine la documentación de T-SQL para obtener una lista completa de [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT]

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

Las opciones REJECT_TYPE y REJECT_VALUE permiten definir cuántas filas o qué porcentaje de los datos deben estar presentes en la tabla final. Durante la carga, si se alcanza el valor de rechazo, se produce un error en la carga. La causa más común de filas rechazadas es un error de coincidencia de la definición de esquema. Por ejemplo, si una columna especifica incorrectamente el esquema de int cuando los datos del archivo son una cadena, cada fila se producirá un error al cargar.

 Azure Data Lake Store usa el control de acceso basado en rol (RBAC) para controlar el acceso a los datos. Esto significa que la entidad de servicio debe tener permisos de lectura para los directorios que se definen en el parámetro de ubicación y para los elementos secundarios de los archivos y los directorios finales. De esta forma PolyBase puede autenticar y leer y cargar esos datos. 

## <a name="load-the-data"></a>Carga de los datos
Para cargar los datos de Azure Data Lake Store, use la instrucción [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)]. 

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
De forma predeterminada, SQL Data Warehouse almacena la tabla como índice de almacén de columnas agrupado. Una vez completada una carga, puede que algunas de las filas de datos no se compriman en el almacén de columnas.  Existen varios motivos por los que esto puede ocurrir. Para aprender más, consulte el artículo sobre [administración de índices de almacén de columnas][manage columnstore indexes].

Para optimizar el rendimiento de las consultas y la compresión de almacén de columnas después de una carga, vuelva a crear la tabla para obligar al índice de almacén de columnas a comprimir todas las filas.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

Para más información sobre el mantenimiento de los índices de almacén de columnas, consulte el artículo sobre [administración de índices de almacén de columnas][manage columnstore indexes].

## <a name="optimize-statistics"></a>Optimización de estadísticas
Es mejor crear estadísticas de columna única inmediatamente después de una carga. Hay algunas opciones para las estadísticas. Por ejemplo, si crea estadísticas de columna única en cada columna, la recompilación de todas las estadísticas puede llevar mucho tiempo. Si sabe que algunas columnas no van a estar en predicados de consulta, puede omitir la creación de estadísticas en dichas columnas.

Si decide crear estadísticas de columna única en todas las columnas de cada una de las tablas, puede usar el ejemplo de código de procedimiento almacenado `prc_sqldw_create_stats` en el artículo sobre [estadísticas][statistics].

El ejemplo siguiente es un buen punto de partida para la creación de estadísticas. Crea estadísticas de columna única en cada una de las columnas de la tabla de dimensiones y en cada una de las columnas de combinación de las tablas de hechos. Siempre puede agregar estadísticas de columna única o de varias columnas a otras columnas de la tabla de hechos más adelante.


## <a name="achievement-unlocked"></a>Logro conseguido.
Ha cargado correctamente datos en Azure SQL Data Warehouse. Buen trabajo.

## <a name="next-steps"></a>Pasos siguientes
La carga de datos es el primer paso para desarrollar una solución de almacenamiento de datos mediante SQL Data Warehouse. Consulte nuestros recursos de desarrollo en [Tablas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview) y [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md).


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
