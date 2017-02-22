---
title: "Tutorial: Introducción al lenguaje U-SQL de Azure Data Lake Analytics | Microsoft Docs"
description: Use este tutorial para aprender sobre el lenguaje U-SQL de Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 96747e898b2d84cbce9251758a90941f85112dd0
ms.openlocfilehash: 7aa3844b9bdc2c5372c6e54b05296dcde6b1c05f


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake
U-SQL es un lenguaje que combina las ventajas de SQL con la potencia expresiva de su propio código para procesar datos a cualquier escala. Mediante la funcionalidad de consulta distribuida escalable de U-SQL, puede analizar los datos de manera eficaz entre almacenes relacionales, como Azure SQL Database. Con U-SQL, puede procesar los datos no estructurados mediante la aplicación de esquemas en la lectura y la inserción de lógica personalizada y UDF. Además, U-SQL incluye extensibilidad que proporciona un control más preciso sobre cómo ejecutarlo a escala. Para más información sobre la filosofía de diseño que subyace a U-SQL, consulte la entrada de blog de Visual Studio [Introducing U-SQL – A Language that makes Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/) (Introducción a U-SQL: un lenguaje que facilita el procesamiento de macrodatos).

U-SQL se diferencia en algunas cosas de ANSI SQL o T-SQL. Por ejemplo, palabras clave como SELECT deben estar todas en mayúscula.

 Su sistema de tipos y el lenguaje de expresión, dentro de las cláusulas SELECT y los predicados WHERE, son de C#. Esto significa que los tipos de datos son los tipos de C# y emplean la semántica NULL de C#; además, las operaciones de comparación dentro de un predicado siguen la sintaxis de C# (por ejemplo, a == "foo"). También significa que los valores son objetos .NET completos, por lo que puede usar fácilmente cualquier método para operar sobre el objeto (por ejemplo, "f o o o".Split(' ')).

Para más información sobre U-SQL, consulte la [referencia del lenguaje U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

### <a name="prerequisites"></a>Requisitos previos
Si todavía no lo ha hecho, lea y complete el [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Después de haber completado el tutorial, vuelva a este artículo.

En el tutorial, ha ejecutado un trabajo de Azure Data Lake Analytics con el siguiente script de U-SQL:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Este script no contiene ningún paso de transformación. Lee el archivo de origen llamado SearchLog.tsv, lo esquematiza y vuelve a escribir el conjunto de filas en un archivo llamado SearchLog-first-u-sql.csv.

Observe el signo de interrogación junto al tipo de datos del campo **Duration**. Esto significa que el campo **Duration** podría ser nulo.

En el script, encontrará los siguientes conceptos y palabras clave:

* Variables de conjunto de filas: cada expresión de consulta que produce un conjunto de filas se puede asignar a una variable. U-SQL sigue el patrón de nomenclatura de variables de T-SQL; por ejemplo, (@searchlog, en el script.

 >[!NOTE]
 >La asignación no fuerza la ejecución. Simplemente nombra la expresión para que pueda crear expresiones más complejas.
* EXTRACT: mediante el uso de esta palabra clave, puede definir un esquema en la lectura. El esquema se especifica mediante un par de nombre de columna y nombre de tipo de C# por columna. Usa lo que se conoce como extractor (Extractors.Tsv(), por ejemplo) para extraer archivos .tsv. Puede desarrollar extractores personalizados.
* OUTPUT: esta palabra clave toma un conjunto de filas y lo serializa. Outputters.Csv() escribe un archivo delimitado por comas en la ubicación especificada. También puede desarrollar outputters personalizados.

 >[!NOTE]
 >Las dos rutas de acceso son relativas. También puede usar rutas de acceso absolutas. Por ejemplo:    
 >     adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
 >
 >Debe usar una ruta de acceso absoluta para acceder a los archivos de las cuentas de almacenamiento vinculadas.  La sintaxis de los archivos almacenados en la cuenta de almacenamiento de Azure vinculada es: wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

 >[!NOTE]
 >Actualmente no se admiten contenedores de almacenamiento de Azure Blob con permisos de acceso a contenedores públicos o blobs públicos.

## <a name="use-scalar-variables"></a>Uso de variables escalares
Puede usar variables escalares para facilitar el mantenimiento del script. El script de U-SQL anterior también se puede escribir como:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformación de conjuntos de filas
Use **SELECT** para transformar conjuntos de filas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La cláusula WHERE usa una [expresión booleana de C#](https://msdn.microsoft.com/library/6a71f45d.aspx). Puede usar el lenguaje de expresiones de C# para crear sus propias expresiones y funciones. Incluso puede realizar un filtrado más complejo si las combina con conjunciones (AND) y disyunciones (OR) lógicas.

El siguiente script usa el método DateTime.Parse() y una conjunción.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >La segunda consulta opera con el resultado del primer conjunto de filas, que crea una composición de ambos filtros. También puede volver a usar un nombre de variable y los nombres tienen un ámbito léxico.

## <a name="aggregate-rowsets"></a>Conjuntos de filas agregados
U-SQL proporciona las conocidas cláusulas ORDER BY, GROUP BY y agregaciones.

La siguiente consulta busca la duración total por región y después muestra las cinco duraciones principales de forma ordenada.

Los conjuntos de filas de U-SQL no conservan el orden en la siguiente consulta. Por lo tanto, para ordenar los resultados, necesita agregar ORDER BY a la instrucción OUTPUT:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

La cláusula ORDER BY de U-SQL se debe combinar con la cláusula FETCH en una expresión SELECT.

La cláusula HAVING de U-SQL puede usarse para restringir los resultados a los grupos que cumplan la condición HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Combinación de datos
U-SQL proporciona operadores de combinación habituales como INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN o SEMI JOIN para combinar no solo tablas, sino también conjuntos de filas (incluso los producidos a partir de archivos).

El script siguiente combina searchlog con un registro de impresión de anuncios y proporciona los anuncios para la cadena de consulta de una fecha determinada.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL solamente admite la sintaxis de combinación compatible con ANSI: conjuntoDeFilas1 JOIN conjuntoDeFilas2 ON predicado. _No_ se admite la sintaxis antigua FROM conjuntoDeFilas1, conjuntoDeFilas2 WHERE predicado.
El predicado en una JOIN debe ser una combinación de igualdad y no una expresión. Si desea usar una expresión, agréguela a la cláusula SELECT de un conjunto de filas anterior. Si desea realizar una comparación diferente, puede moverla a la cláusula WHERE.

## <a name="create-databases-table-valued-functions-views-and-tables"></a>Creación de bases de datos, funciones con valores de tabla, vistas y tablas
En U-SQL, puede usar datos en el contexto de una base de datos y un esquema y no siempre tienes que leer archivos o escribir en ellos.

Cada script U-SQL se ejecuta con una base de datos predeterminada (maestra) y un esquema predeterminado (DBO) como contexto predeterminado. Puede crear una base de datos o un esquema propios. Para cambiar el contexto, use la instrucción USE.

### <a name="create-a-tvf"></a>Creación de una TVF
En el anterior script de U-SQL, repitió el uso de EXTRACT para leer el mismo archivo de origen. La función con valores de tabla de U-SQL (TVF) permite encapsular los datos para volver a usarlos más adelante.  

El siguiente script crea una función TVF llamada *Searchlog()* en la base de datos y el esquema  predeterminados:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

El siguiente script muestra cómo usar la TVF definida en el anterior script:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Creación de vistas
Si solo tiene una expresión de consulta que desea abstraer pero no crear un parámetro a partir de ella, puede crear una vista en lugar de una función con valores de tabla.

El siguiente script crea una vista llamada *SearchlogView* en la base de datos y el esquema predeterminados:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

El siguiente script muestra el uso de la vista definida:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Cree las tablas.
De forma semejante a una tabla de base de datos relacional, U-SQL permite crear una tabla con un esquema predefinido o crear una tabla que infiere el esquema a partir de la consulta que rellena la tabla (lo que también se conoce como CREATE TABLE AS SELECT o CTAS).

Cree una base de datos y dos tablas mediante el siguiente script:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SearchLogDb;
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    DISTRIBUTED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Consulta de tablas
Puede consultar tablas, como las creadas en el script anterior, de la misma forma que se consultan los archivos de datos. En lugar de crear un conjunto de filas mediante EXTRACT, ahora se puede hacer referencia al nombre de la tabla.

Para leer las tablas, modifique el script de transformación que usó anteriormente:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

 >[!NOTE]
 >Actualmente, no puede ejecutar una instrucción SELECT en una tabla del mismo script que aquel donde creó la tabla.

## <a name="conclusion"></a>Conclusión
Este tutorial trata solamente una pequeña parte de U-SQL. Debido a su ámbito limitado, no se han descrito muchas otras ventajas de U-SQL. Por ejemplo, puede:

* Uso de CROSS APPLY para desempaquetar partes de cadenas, matrices y asignaciones en filas.
* Operación de conjuntos de datos particionados (conjuntos de archivos y tablas con particiones).
* Desarrollar operadores definidos por el usuario como extractores, outputters, procesadores y agregadores definidos por el usuario en C#.
* Uso de las funciones de ventana de U-SQL.
* Administrar código de U-SQL con vistas, funciones con valores de tabla y procedimientos almacenados.
* Ejecución de código personalizado arbitrario en sus nodos de procesamiento.
* Conectarse a bases de datos SQL y federar consultas entre ellas y sus datos de U-SQL y Azure Data Lake.

## <a name="see-also"></a>Consulte también
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desarrollo de scripts de U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Uso de funciones de ventana de U-SQL para trabajos de Análisis de Azure Data Lake](data-lake-analytics-use-window-functions.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Díganos su opinión
* [Envíe una solicitud de característica](http://aka.ms/adlafeedback)
* [Obtenga ayuda en los foros](http://aka.ms/adlaforums)
* [Proporcione comentarios sobre U-SQL](http://aka.ms/usqldiscuss)



<!--HONumber=Dec16_HO4-->


