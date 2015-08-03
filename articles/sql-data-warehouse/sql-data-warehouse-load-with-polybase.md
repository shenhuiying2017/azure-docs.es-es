<properties
   pageTitle="Tutorial sobre PolyBase en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre qué es PolyBase y cómo usarlo en escenarios de almacenamiento de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# Carga de datos con PolyBase
La tecnología PolyBase permite consultar y combinar datos de varios orígenes, todo mediante comandos de Transact-SQL.

Con PolyBase puede consultar datos almacenados en el almacenamiento de blobs de Azure y cargarlos en la base de datos de Almacenamiento de datos SQL siguiendo estos pasos:

- Cree la clave maestra de base de datos y las credenciales.
- Cree los objetos PolyBase: origen de datos externo, formato de archivo externo y tabla externa. 
- Consulte los datos almacenados en el almacenamiento de blobs de Azure.
- Cargue los datos del almacenamiento de blobs de Azure en Almacenamiento de datos SQL.


## Requisitos previos
Para seguir paso a paso este tutorial, necesita:

- Cuenta de almacenamiento de Azure
- Los datos almacenados en el almacenamiento de blobs de Azure como archivos de texto delimitado

En primer lugar, creará los objetos que requiere PolyBase para conectarse y consultar datos en el almacenamiento de blobs de Azure.

## Creación de clave maestra de base de datos
Conéctese a la base de datos de usuarios en el servidor para crear una clave maestra de base de datos. Esta clave se usa para cifrar el secreto de credencial en el paso siguiente.

```
-- Creating master key
CREATE MASTER KEY;
```

Tema de referencia: [CREATE MASTER KEY (Transact-SQL)][].

## Creación de una credencial con ámbito de base de datos
Para tener acceso al almacenamiento de blobs de Azure, deberá crear una credencial con ámbito de base de datos que almacene información de autenticación para la cuenta de almacenamiento de Azure. Conéctese a la base de datos de almacenamiento de datos y cree una credencial con ámbito de base de datos para cada cuenta de almacenamiento de Azure a la que quiera tener acceso. Especifique un nombre de identidad y la clave de la cuenta de almacenamiento de Azure como secreto. El nombre de identidad no afecta a la autenticación en el Almacenamiento de Azure.

Para ver si ya existe una credencial con ámbito de base de datos, use sys.database_credentials, no sys.credentials, que solo muestra las credenciales del servidor.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

Tema de referencia: [CREATE CREDENTIAL (Transact-SQL)][].

Al girar las claves de la cuenta de almacenamiento de Azure, tendrá quitar la credencial y volver a crearla con la nueva clave como secreto.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

Tema de referencia: [DROP CREDENTIAL (Transact-SQL)][].


## Creación de un origen de datos externo
El origen de datos externo es un objeto de base de datos que almacena la ubicación de los datos de almacenamiento de blobs de Azure y la información de acceso. Debe definir un origen de datos externo para cada contenedor de Almacenamiento de Azure al que quiera tener acceso.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

Tema de referencia: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## Creación de un formato de archivo externo
El formato de archivo externo es un objeto de base de datos que especifica el formato de los datos externos. En este ejemplo, hemos descomprimido datos en un archivo de texto y los campos están separados por el carácter de barra vertical ('|').

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase puede trabajar con datos comprimidos y sin comprimir en formatos de texto delimitado, Hive RCFILE y HIVE ORC.

Tema de referencia: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## Creación de una tabla externa

La definición de tabla externa es similar a una definición de tabla relacional. La principal diferencia es la ubicación y el formato de los datos. La definición de tabla externa se almacena en la base de datos de Almacenamiento de datos SQL. Los datos se almacenan en la ubicación especificada por el origen de datos.

La opción LOCATION especifica la ruta de acceso a los datos desde la raíz del origen de datos. En este ejemplo, los datos se encuentran en 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]Tenga en cuenta que en este momento no se pueden crear estadísticas en una tabla externa.

Todos los archivos de la misma tabla tienen que estar en la misma carpeta lógica de BLOB de Azure. Como práctica recomendada, divida los datos de almacenamiento de Azure en archivos de no más de 1 GB cuando sea posible para el procesamiento paralelo con Almacenamiento de datos SQL.

Tema de referencia: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Los objetos recién creados se almacenan en la base de datos de Almacenamiento de datos SQL. Puede verlos en el Explorador de objetos de SQL Server Data Tools (SSDT).


## Consulta de datos en el almacenamiento de blobs de Azure
Las consultas en tablas externas simplemente usan el nombre de tabla como si fuese una tabla relacional.

Se trata de una consulta ad hoc que combina datos de cliente de seguros almacenados en Almacenamiento de datos SQL con datos de sensor de automóviles almacenados en almacenamiento de blobs de Azure. El resultado muestra los conductores que conducen más rápido que otros.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Carga de datos del almacenamiento de blobs de Azure
En este ejemplo se cargan datos del almacenamiento de blobs de Azure en la base de datos de Almacenamiento de datos SQL.

El almacenamiento directo de datos quita el tiempo de transferencia de datos de las consultas. El almacenamiento de datos con un índice ColumnStore mejora en hasta 10 veces el rendimiento de las consultas en consultas de análisis.

En este ejemplo se usa la instrucción CREATE TABLE AS SELECT para cargar datos. La nueva tabla hereda las columnas designadas en la consulta. Hereda los tipos de datos de esas columnas de la definición de tabla externa.

CREATE TABLE AS SELECT es una instrucción Transact-SQL de alto rendimiento que reemplaza a INSERT...SELECT. Se desarrolló originalmente para el motor de procesamiento paralelo masivo (MPP) de Analytics Platform System y se incluye ahora en Almacenamiento de datos SQL.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

Vea [CREATE TABLE AS SELECT (Transact-SQL)][].


## Limitaciones
La carga con PolyBase solo admite el tipo de codificación UTF-8. Para otros tipos de codificación, digamos UTF-16, considere el uso de la utilidad bcp, SSIS o la Factoría de datos de Azure para cargar datos en la base de datos de Almacenamiento de datos SQL.

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/es-es/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/es-es/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/es-es/library/ms189450.aspx

<!---HONumber=July15_HO4-->