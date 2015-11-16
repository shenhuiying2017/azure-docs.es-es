<properties
   pageTitle="Cargar datos con el tutorial de PolyBase | Microsoft Azure"
   description="Obtenga información sobre cómo usar PolyBase para cargar datos en el Almacenamiento de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# Carga de datos con PolyBase

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-load-with-polybase-short.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

En este tutorial se le enseñará a cargar datos en el Almacenamiento de datos SQL mediante PolyBase. Para obtener más información sobre PolyBase, consulte el [Tutorial de PolyBase en el Almacenamiento de datos SQL][].


## Requisitos previos
Para seguir paso a paso este tutorial, necesita:

- Base de datos del Almacenamiento de datos SQL
- Cuenta de almacenamiento de Azure


## Paso 1: Crear un archivo de datos de origen
Abra el Bloc de notas y copie las siguientes líneas de datos en un nuevo archivo. Guárdelo en el directorio temporal local, C:\\Temp\\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```


## Paso 2: Migrar datos al almacenamiento de blobs de Azure

- Descrgue la [versión más reciente de AzCopy][].
- Abra una ventana de comandos y vaya al directorio de instalación de AzCopy en el equipo donde se encuentra AzCopy.exe. De forma predeterminada, el directorio de instalación es %ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\ en un equipo de Windows de 64 bits.
- Ejecute el siguiente comando para cargar el archivo. Especifique la clave de la cuenta de almacenamiento de Azure para /DestKey.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

Para obtener más información sobre AzCopy, consulte [Introducción a la utilidad de línea de comandos AzCopy][].


## Paso 3: Crear tablas externas

A continuación, debe crear tablas externas en el Almacenamiento de datos SQL para hacer referencia a los datos del almacenamiento de blobs de Azure. Para crear una tabla externa, siga estos pasos:

- [Create Master Key][]\: para cifrar el secreto de la credencial con ámbito de base de datos.
- [Create Database Scoped Credential]\: para especificar la información de autenticación para la cuenta de almacenamiento de Azure.
- [Create External Data Source]\: para especificar la ubicación del almacenamiento blobs de Azure.
- [Create External File Format]\: para especificar el diseño de los datos.
- [Create External Table]\: para hacer referencia a los datos del almacenamiento de Azure.


```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## Paso 4: Carga de datos en Almacenamiento de datos SQL

- Para cargar los datos en una tabla nueva, ejecute la instrucción [CREATE TABLE AS SELECT (Transact-SQL)][]. La nueva tabla hereda las columnas designadas en la consulta. Hereda los tipos de datos de esas columnas de la definición de tabla externa. 
- Para cargar los datos en una tabla existente, use la instrucción INSERT...SELECT.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```


## Paso 5: Crear estadísticas de los datos recién cargados 

Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Si desea ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][] en el grupo de temas relacionados con el desarrollo. A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.


```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

<!--Article references-->
[Tutorial de PolyBase en el Almacenamiento de datos SQL]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[versión más reciente de AzCopy]: http://aka.ms/downloadazcopy
[Introducción a la utilidad de línea de comandos AzCopy]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Create External Data Source]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Create External File Format]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Create External Table]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Create Master Key]: https://msdn.microsoft.com/es-ES/library/ms174382.aspx
[Create Database Scoped Credential]: https://msdn.microsoft.com/es-ES/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx


<!--Article references-->

[Estadísticas]: ./sql-data-warehouse-develop-statistics.md

<!---HONumber=Nov15_HO2-->