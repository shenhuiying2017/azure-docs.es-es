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
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# Carga de datos con PolyBase
La tecnología PolyBase permite consultar y combinar datos de varios orígenes, todo mediante comandos de Transact-SQL.

Con PolyBase puede consultar datos almacenados en el almacenamiento de blobs de Azure y cargarlos en la base de datos de Almacenamiento de datos SQL siguiendo estos pasos:

- Cree la clave maestra de base de datos y las credenciales.
- Cree los objetos PolyBase: origen de datos externo, formato de archivo externo y tabla externa. 
- Consulte los datos almacenados en el almacenamiento de blobs de Azure.
- Cargue los datos del almacenamiento de blobs de Azure en Almacenamiento de datos SQL.
- Exporte datos desde Almacenamiento de datos SQL al almacenamiento de blobs de Azure.


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

## Paso 1: Almacenamiento de credenciales en la base de datos
Para tener acceso al almacenamiento de blobs de Azure, deberá crear una credencial con ámbito de base de datos que almacene información de autenticación para la cuenta de almacenamiento de Azure. Siga estos pasos para almacenar una credencial en la base de datos.

1. Conéctese a la base de datos de Almacenamiento de datos SQL.
2. Use [CREATE MASTER KEY (Transact-SQL)][] para crear una clave maestra para la base de datos. Si la base de datos ya tiene una clave maestra, no tiene que crear otra. Esta clave sirve para cifrar el "secreto" de la credencial en el paso siguiente.

    ```
    -- Create a E master key
    CREATE MASTER KEY;
    ```

1. Compruebe si ya tiene las credenciales de la base de datos. Para ello, use la vista del sistema sys.database\_credentials, no sys.credentials que solo muestra las credenciales del servidor.

    ``` -- Compruebe las credenciales de ámbito de base de datos existentes. SELECT * FROM sys.database\_credentials;

3. Use [CREATE CREDENTIAL (Transact-SQL)][] para crear una credencial con ámbito de base de datos para cada cuenta de almacenamiento de Azure a la que quiera acceder. En este ejemplo, IDENTITY es un nombre descriptivo para la credencial. No afecta a la autenticación en el almacenamiento de Azure. SECRET se refiere a la clave de la cuenta de almacenamiento de Azure.

    -- Cree una credencial con ámbito de base de datos: CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe' , secreto = '<azure_storage_account_key>' ; ```

1. Si necesita quitar una credencial de ámbito de base de datos, use [DROP CREDENTIAL (Transact-SQL)][]\:

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

## Paso 2: Creación de un origen de datos externo
El origen de datos externo es un objeto de base de datos que almacena la ubicación de los datos de almacenamiento de blobs de Azure y la información de acceso. Use [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][] para definir un origen de datos externo para cada blob de almacenamiento de Azure al que quiera acceder.

    ```
    -- Create an external data source for an Azure storage blob
    CREATE EXTERNAL DATA SOURCE azure_storage 
    WITH
    (
        TYPE = HADOOP,
        LOCATION ='wasbs://mycontainer@test.blob.core.windows.net',
        CREDENTIAL = ASBSecret
    )
    ;
    ```

Si necesita quitar la tabla externa, use [DROP EXTERNAL DATA SOURCE][]:

    ```
    -- Drop an external data source
    DROP EXTERNAL DATA SOURCE azure_storage
    ;
    ```

## Paso 3: Creación de un formato de archivo externo
El formato de archivo externo es un objeto de base de datos que especifica el formato de los datos externos. PolyBase puede trabajar con datos comprimidos y sin comprimir en formatos de texto delimitado, Hive RCFILE y HIVE ORC.

Use [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] para crear el formato de archivo externo. En este ejemplo se especifica que los datos del archivo son texto sin comprimir y los campos están separados por el carácter de barra vertical ('|').

```
-- Create an external file format for a text-delimited file.
-- Data is uncompressed and fields are separated with the
-- pipe character.
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT, 
    FORMAT_OPTIONS  
    (
        FIELD_TERMINATOR ='|',
        USE_TYPE_DEFAULT = TRUE
    )
)
;
```

Si necesita quitar un formato de archivo externo, use [DROP EXTERNAL FILE FORMAT].

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```

## Creación de una tabla externa

La definición de tabla externa es similar a una definición de tabla relacional. La principal diferencia es la ubicación y el formato de los datos.

- La definición de tabla externa se almacena en forma de metadatos en la base de datos de Almacenamiento de datos SQL. 
- Los datos se almacenan en la ubicación externa que especifique el origen de datos.

Use [CREATE EXTERNAL TABLE (Transact-SQL)][] para definir la tabla externa.

La opción LOCATION especifica la ruta de acceso a los datos desde la raíz del origen de datos. En este ejemplo, los datos se encuentran en 'wasbs://mycontainer@test.blob.core.windows.net/path/Demo/'. Todos los archivos de la misma tabla tienen que estar en la misma carpeta lógica del almacenamiento de blobs de Azure.

Opcionalmente, también puede especificar opciones de rechazo (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE) que determinan la manera en que PolyBase controlará registros con modificaciones que recibe del origen de datos externo.

```
-- Creating an external table for data in Azure blob storage.
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL,
     [CustomerKey]   int    NOT NULL,
     [GeographyKey]  int        NULL,
     [Speed]         float  NOT NULL,
     [YearMeasured]  int    NOT NULL,
)
WITH 
(
    LOCATION    = '/Demo/',
    DATA_SOURCE = azure_storage,
    FILE_FORMAT = text_file_format      
)
;
```

Los objetos recién creados se almacenan en la base de datos de Almacenamiento de datos SQL. Puede verlos en el Explorador de objetos de SQL Server Data Tools (SSDT).

Para quitar una tabla externa necesita usar la sintaxis siguiente:

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]Al quitar una tabla externa, hay que usar `DROP EXTERNAL TABLE`. **No puede** usar `DROP TABLE`.

Tema de referencia: [DROP EXTERNAL TABLE (Transact-SQL)][].

También merece la pena mencionar que las tablas externas están visibles en las vistas de catálogo `sys.tables` y, más concretamente, `sys.external_tables`.

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

> [AZURE.NOTE]Una consulta en una tabla externa puede producir el error *"Consulta anulada: se alcanzó el umbral de rechazo máximo al leer desde un origen externo"*. Esto indica que sus datos externos contienen registros *con modificaciones*. Un registro de datos se considera "con modificaciones" si los tipos de datos reales o el número de columnas no coincide con las definiciones de columna de la tabla externa o si los datos no se ajustan al formato de archivo externo especificado. Para corregirlo, asegúrese de que la tabla externa y las definiciones de formato de archivo externos son correctas y que los datos externos se ajustan a estas definiciones. En el caso de que un subconjunto de registros de datos externos estén modificados, puede rechazar estos registros para sus consultas mediante las opciones de rechazo en CREATE EXTERNAL TABLE DDL.


## Carga de datos del almacenamiento de blobs de Azure
En este ejemplo se cargan datos del almacenamiento de blobs de Azure en la base de datos de Almacenamiento de datos SQL.

El almacenamiento directo de datos quita el tiempo de transferencia de datos de las consultas. El almacenamiento de datos con un índice ColumnStore mejora en hasta 10 veces el rendimiento de las consultas en consultas de análisis.

En este ejemplo se usa la instrucción CREATE TABLE AS SELECT para cargar datos. La nueva tabla hereda las columnas designadas en la consulta. Hereda los tipos de datos de esas columnas de la definición de tabla externa.

CREATE TABLE AS SELECT es una instrucción Transact-SQL de alto rendimiento que carga los datos en paralelo a todos los nodos de proceso de su Almacenamiento de datos SQL. Se desarrolló originalmente para el motor de procesamiento paralelo masivo (MPP) de Analytics Platform System y se incluye ahora en Almacenamiento de datos SQL.

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

## Crear estadísticas de los datos recién cargados

Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Si quiere ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][] en el grupo de temas relacionados con el desarrollo. A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Exportación de datos al almacenamiento de blobs de Azure
Esta sección muestra cómo exportar datos desde el Almacenamiento de datos SQL al almacenamiento de blobs de Azure. En este ejemplo se utiliza CREATE EXTERNAL TABLE AS SELECT, que es una instrucción Transact-SQL de alto rendimiento para exportar los datos en paralelo desde todos los nodos de proceso.

En el ejemplo siguiente se crea una tabla externa Weblogs2014 con definiciones de columna y datos de la tabla dbo.Weblogs. La definición de tabla externa se almacena en Almacenamiento de datos SQL y los resultados de la instrucción SELECT se exportan en el directorio "/archive/log2014/" del contenedor de blobs especificado por el origen de datos. Los datos se exportan en el formato de archivo de texto especificado.

```
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


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
[Estadísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_1210_2015-->