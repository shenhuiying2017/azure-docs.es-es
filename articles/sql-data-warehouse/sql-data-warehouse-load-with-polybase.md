<properties
   pageTitle="Tutorial sobre PolyBase en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre qué es PolyBase y cómo usarlo en escenarios de almacenamiento de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
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

> [AZURE.IMPORTANT]Los tipos de cuenta de Almacenamiento de Azure admitidos por PolyBase son:
> 
> + Almacenamiento con redundancia local estándar (Standard-LRS)
> + Almacenamiento con redundancia geográfica (Standard-GRS)
> + Almacenamiento con redundancia geográfica con acceso de lectura (Standard-RAGRS)
>
> PolyBase NO admite los tipos de cuenta Almacenamiento con redundancia de zona estándar (Standard-ZRS) y Almacenamiento con redundancia local premium (Premium-LRS). Si va a crear una cuenta de Almacenamiento de Azure, asegúrese de que selecciona desde el Plan de tarifa un tipo de cuenta que admita PolyBase.


## Creación de clave maestra de base de datos
Conéctese a la base de datos de usuarios en el servidor para crear una clave maestra de base de datos. Esta clave se usa para cifrar el secreto de credencial en el paso siguiente.

```
-- Creating master key
CREATE MASTER KEY;
```

Tema de referencia: [CREATE MASTER KEY (Transact-SQL)][].

## Creación de una credencial con ámbito de base de datos
Para tener acceso al almacenamiento de blobs de Azure, deberá crear una credencial con ámbito de base de datos que almacene información de autenticación para la cuenta de almacenamiento de Azure. Conéctese a la base de datos de almacenamiento de datos y cree una credencial con ámbito de base de datos para cada cuenta de almacenamiento de Azure a la que quiera tener acceso. Especifique un nombre de identidad y la clave de la cuenta de almacenamiento de Azure como secreto. El nombre de identidad no afecta a la autenticación en el Almacenamiento de Azure.

Para ver si ya existe una credencial con ámbito de base de datos, use sys.database\_credentials, no sys.credentials, que solo muestra las credenciales del servidor.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = '<azure_storage_account_key>'
;
```

Tema de referencia: [CREATE CREDENTIAL (Transact-SQL)][].

Para quitar una credencial de ámbito de base de datos, use simplemente la siguiente sintaxis:

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

Tema de referencia: [DROP CREDENTIAL (Transact-SQL)][].

## Creación de un origen de datos externo
El origen de datos externo es un objeto de base de datos que almacena la ubicación de los datos de almacenamiento de blobs de Azure y la información de acceso. Debe definir un origen de datos externo para cada contenedor de Almacenamiento de Azure al que quiera tener acceso.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@test.blob.core.windows.net'
,   CREDENTIAL = ASBSecret
)
;
```

Tema de referencia: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

Para quitar el origen de datos externo, la sintaxis es la siguiente:

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

Tema de referencia: [DROP EXTERNAL DATA SOURCE (Transact-SQL)][].

## Creación de un formato de archivo externo
El formato de archivo externo es un objeto de base de datos que especifica el formato de los datos externos. En este ejemplo, hemos descomprimido datos en un archivo de texto y los campos están separados por el carácter de barra vertical ('|').

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

PolyBase puede trabajar con datos comprimidos y sin comprimir en formatos de texto delimitado, Hive RCFILE y HIVE ORC.

Tema de referencia: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

Para quitar un formato de archivo externo, la sintaxis es la siguiente:

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```
Tema de referencia: [DROP EXTERNAL FILE FORMAT (Transact-SQL)][].

## Creación de una tabla externa

La definición de tabla externa es similar a una definición de tabla relacional. La principal diferencia es la ubicación y el formato de los datos. La definición de tabla externa se almacena en la base de datos de Almacenamiento de datos SQL. Los datos se almacenan en la ubicación especificada por el origen de datos.

La opción LOCATION especifica la ruta de acceso a los datos desde la raíz del origen de datos. En este ejemplo, los datos se encuentran en 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'. Todos los archivos de la misma tabla tienen que estar en la misma carpeta lógica de BLOB de Azure.

Opcionalmente, también puede especificar opciones de rechazo (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE) que determinan la manera en que PolyBase controlará registros con modificaciones que recibe del origen de datos externo.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]Tenga en cuenta que en este momento no se pueden crear estadísticas en una tabla externa.

Tema de referencia: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Los objetos recién creados se almacenan en la base de datos de Almacenamiento de datos SQL. Puede verlos en el Explorador de objetos de SQL Server Data Tools (SSDT).

Para quitar una tabla externa necesita usar la sintaxis siguiente:

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]Al quitar una tabla externa, debe usar `DROP EXTERNAL TABLE`. **No puede** usar `DROP TABLE`.

Tema de referencia: [DROP EXTERNAL TABLE (Transact-SQL)][].

También merece la pena mencionar que las tablas externas están visibles tanto en `sys.tables` como (de manera más específica) en las vistas de catálogo de `sys.external_tables`.

## Rotación de claves de almacenamiento

De vez en cuando deseará cambiar la clave de acceso para el almacenamiento de blobs por motivos de seguridad.

La manera más elegante de realizar esta tarea es seguir un proceso conocido como "rotar las claves". Puede que haya observado que dispone de dos claves de almacenamiento para su cuenta de almacenamiento de blobs. Esto es para que puede realizar la transición.

La rotación de las claves de la cuenta de almacenamiento de Azure es un proceso sencillo de tres pasos

1. Crear la segunda credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento secundaria
2. Crear el segundo origen de datos externo basado en esta nueva credencial
3. Quitar y crear las tablas externas señalando al nuevo origen de datos externo

Cuando haya migrado todas las tablas externas al nuevo origen de datos externo, puede realizar las tareas de limpieza:
 
1. Quitar el primer origen de datos externo
2. Quitar la primera credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento principal
3. Iniciar sesión en Azure y volver a generar la clave de acceso principal lista para la próxima vez

## Consulta de datos en el almacenamiento de blobs de Azure
Las consultas en tablas externas simplemente usan el nombre de tabla como si fuese una tabla relacional.


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]Una consulta acerca de una tabla externa puede producir el error *"Consulta anulada: se alcanzó el umbral de rechazo máximo al leer desde un origen externo"*. Esto indica que sus datos externos contienen registros *con modificaciones*. Un registro de datos se considera "con modificaciones" si los tipos de datos reales o el número de columnas no coincide con las definiciones de columna de la tabla externa o si los datos no se ajustan al formato de archivo externo especificado. Para corregirlo, asegúrese de que la tabla externa y las definiciones de formato de archivo externos son correctas y que los datos externos se ajustan a estas definiciones. En el caso de que un subconjunto de registros de datos externos estén modificados, puede rechazar estos registros para sus consultas mediante las opciones de rechazo en CREATE EXTERNAL TABLE DDL.


## Carga de datos del almacenamiento de blobs de Azure
En este ejemplo se cargan datos del almacenamiento de blobs de Azure en la base de datos de Almacenamiento de datos SQL.

El almacenamiento directo de datos quita el tiempo de transferencia de datos de las consultas. El almacenamiento de datos con un índice ColumnStore mejora en hasta 10 veces el rendimiento de las consultas en consultas de análisis.

En este ejemplo se usa la instrucción CREATE TABLE AS SELECT para cargar datos. La nueva tabla hereda las columnas designadas en la consulta. Hereda los tipos de datos de esas columnas de la definición de tabla externa.

CREATE TABLE AS SELECT es una instrucción Transact-SQL de alto rendimiento que reemplaza a INSERT...SELECT. Se desarrolló originalmente para el motor de procesamiento paralelo masivo (MPP) de Analytics Platform System y se incluye ahora en Almacenamiento de datos SQL.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

Vea [CREATE TABLE AS SELECT (Transact-SQL)][].


## Evitar el requisito UTF-8 de PolyBase
Actualmente PolyBase admite la carga de archivos de datos que se han codificado con UTF-8. Como UTF-8 usa la misma codificación de caracteres que PolyBase ASCII, también admitirá la carga de datos codificada con ASCII. Sin embargo, PolyBase no admite otra codificación de caracteres como UTF-16/Unicode o caracteres ASCII extendidos. ASCII extendido incluye caracteres con acentos como la diéresis que es común en alemán.

Para satisfacer este requisito, la mejor respuesta es volver a escribir con codificación UTF-8.

Hay varias maneras de hacerlo. A continuación se muestran dos enfoques de uso de Powershell:

### Ejemplo sencillo para archivos pequeños

A continuación se muestra un script de Powershell sencillo de una línea crea el archivo.
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Sin embargo, aunque se trata de una manera sencilla de volver a codificar los datos, no es de ningún modo la más eficaz. El siguiente ejemplo de streaming de entrada/salida es mucho más rápido y logra el mismo resultado.

### Ejemplo de streaming de E/S para archivos de mayor tamaño

El siguiente ejemplo de código es más complejo, pero conforme transmite por secuencias las filas de datos del origen al destino es mucho más eficaz. Use este método para archivos más grandes.

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

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

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/es-ES/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/es-ES/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/es-ES/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/es-ES/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/es-ES/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/es-ES/library/ms189450.aspx

<!---HONumber=Oct15_HO4-->