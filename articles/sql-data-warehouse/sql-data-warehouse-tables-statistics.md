---
title: "Administración de estadísticas de tablas en SQL Data Warehouse | Microsoft Docs"
description: "Introducción a las estadísticas de tablas en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: b007e1894f163d50dbf31e3c09b4b5ff329adb59
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Administración de estadísticas en tablas en SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Información general][Overview]
> * [Tipos de datos][Data Types]
> * [Distribución][Distribute]
> * [Índice][Index]
> * [Partición][Partition]
> * [Estadísticas][Statistics]
> * [Temporal][Temporary]
> 
> 

Cuanto más sepa Azure SQL Data Warehouse acerca de los datos, más rápido podrá ejecutar consultas en ellos. La recopilación de estadísticas sobre los datos y su carga en SQL Data Warehouse es una de las tareas más importantes que se pueden realizar para optimizar las consultas. Esto se debe a que el optimizador de consultas de SQL Data Warehouse se basa en costos. Compara el costo de varios planes de consulta y elige el menor que, en la mayoría de los casos, es el que se ejecutará más rápidamente. Por ejemplo, si el optimizador estima que la fecha de filtro de la consulta devolverá una fila, puede elegir un plan completamente diferente del que elegiría si calcula que la fecha seleccionada devolverá un millón de filas.

En la actualidad, el proceso de creación y actualización de estadísticas es manual, pero muy fácil.  Pronto podrá crear y actualizar automáticamente las estadísticas en columnas únicas e índices.  Mediante la información siguiente, se puede automatizar en gran medida la administración de las estadísticas de los datos. 

## <a name="getting-started-with-statistics"></a>Introducción a las estadísticas
Crear estadísticas de muestra en cada columna es una forma sencilla de empezar a trabajar con las estadísticas. Las estadísticas obsoletas provocarán un rendimiento en las consultas que no es el óptimo. Sin embargo, actualizar las estadísticas en todas las columnas a medida que los datos crecen pueden consumir la memoria. 

Estas son algunas recomendaciones para diferentes escenarios:
| **Escenario** | Recomendación |
|:--- |:--- |
| **Introducción** | Actualice todas las columnas después de migrar a SQL Data Warehouse |
| **Columna más importante para estadísticas** | Clave de distribución hash |
| **Segunda columna más importante para estadísticas** | Clave de partición |
| **Otras columnas importantes para estadísticas** | Fecha, combinaciones frecuentes, GROUP BY, HAVING y WHERE |
| **Frecuencia de actualizaciones de estadísticas**  | Conservadora: a diario <br></br> Después de cargar o transformar los datos |
| **Muestreo** |  Con menos de mil millones de filas, utilice el muestreo predeterminado (20 por ciento) <br></br> Con más de mil millones de filas, un intervalo de estadísticas de un dos por ciento es apropiado |

## <a name="updating-statistics"></a>Actualización de estadísticas

Uno de los procedimientos recomendados es la actualización diaria de estadísticas en columnas de fecha al agregarse nuevas fechas. Cada fila nueva de tiempo se carga en el almacenamiento de datos, se agregan nuevas fechas de carga o de transacción. Estas cambian la distribución de los datos y hacen que las estadísticas se queden obsoletas. Por el contrario, es posible que las estadísticas de una columna de país en una tabla de clientes nunca tengan que actualizarse, pues la distribución de valores no suele cambiar. Suponiendo que la distribución es constante entre los clientes, agregar nuevas filas a la variación de tabla no va a cambiar la distribución de datos. Sin embargo, si su almacenamiento de datos solo contiene un país y trae datos de un nuevo país, y esto da lugar al almacenamiento de datos de varios países, debe actualizar las estadísticas en la columna de país.

Una de las primeras preguntas que se deben formular para la solución de problemas de una consulta es **"¿Están actualizadas las estadísticas?"**

No se trata de una pregunta que se pueda responder por la antigüedad de los datos. Un objeto de estadísticas actualizadas podría ser antiguo si no ha habido ningún cambio material en los datos subyacentes. Si el número de filas ha cambiado significativamente o hay un cambio material en la distribución de valores para una columna, *entonces* es el momento de actualizar las estadísticas.

Dado que no hay una vista de administración dinámica para determinar si los datos de la tabla han cambiado desde la última actualización de estadísticas, estar al tanto de su antigüedad puede proporcionarle una visión parcial.  Puede usar la siguiente consulta para determinar la última vez que se actualizaron las estadísticas en cada tabla.  

> [!NOTE]
> Recuerde que, si hay un cambio material en la distribución de valores para una columna, debe actualizar las estadísticas independientemente de la última vez que se actualizaran.  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Las **columnas de fecha** en un almacenamiento de datos, por ejemplo, normalmente necesitan que las estadísticas se actualicen con frecuencia. Cada fila nueva de tiempo se carga en el almacenamiento de datos, se agregan nuevas fechas de carga o de transacción. Estas cambian la distribución de los datos y hacen que las estadísticas se queden obsoletas.  Por el contrario, es posible que las estadísticas en una columna de género de una tabla de clientes no necesiten actualizarse nunca. Suponiendo que la distribución es constante entre los clientes, agregar nuevas filas a la variación de tabla no va a cambiar la distribución de datos. Sin embargo, si el almacén de datos contiene solo un género y un nuevo requisito da como resultado varios géneros, tiene que actualizar las estadísticas en la columna de género.

Para obtener más información, consulte [Estadísticas][Statistics] en MSDN.

## <a name="implementing-statistics-management"></a>Implementación de administración de estadísticas
A menudo resulta conveniente extender el proceso de carga de datos para garantizar que las estadísticas están actualizadas al final de la carga. La carga de datos se produce cuando las tablas cambian su tamaño con mayor frecuencia o la distribución de los valores. Por consiguiente, se trata de un lugar lógico para implementar algunos procesos de administración.

Los siguientes principios fundamentales se proporcionan para actualizar las estadísticas durante el proceso de carga:

* Asegúrese de que cada tabla cargada tiene al menos un objeto de estadísticas actualizado. Así se actualiza la información del tamaño de las tablas (recuento de filas y recuento de páginas) como parte de la actualización de las estadísticas.
* Céntrese en las columnas que participan en las cláusulas JOIN, GROUP BY, ORDER BY y DISTINCT.
* Considere la posibilidad de actualizar con más frecuencia las columnas mediante "clave ascendente", como las fechas de transacción, ya que estos valores no se incluirán en el histograma de estadísticas.
* Considere la posibilidad de actualizar columnas de distribución estática con menor frecuencia.
* Recuerde que cada objeto de estadística se actualiza en secuencia. Implementar solo `UPDATE STATISTICS <TABLE_NAME>` no es siempre recomendable, especialmente para las tablas amplias con muchos objetos de estadísticas.

Para obtener más información, consulte [estimación de cardinalidad][Cardinality Estimation] en MSDN.

## <a name="examples-create-statistics"></a>Ejemplos: crear estadísticas
Estos ejemplos muestran cómo utilizar diversas opciones de creación de estadísticas. Las opciones que utiliza para cada columna dependen de las características de los datos y cómo se utilizará la columna en las consultas.

### <a name="create-single-column-statistics-with-default-options"></a>Crear estadísticas de columna única con las opciones predeterminadas
Para crear estadísticas de una columna, basta con proporcionar un nombre para el objeto de estadística y el nombre de la columna.

Esta sintaxis utiliza todas las opciones predeterminadas. De forma predeterminada, SQL Data Warehouse muestrea el **20 %** de la tabla cuando crea estadísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por ejemplo: 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Crear estadísticas de columna única mediante el examen de todas las filas
La velocidad de muestreo predeterminada del 20 % es suficiente para la mayoría de las situaciones. Sin embargo, puede ajustar la velocidad de muestreo.

Para probar la tabla completa, utilice esta sintaxis:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por ejemplo: 

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Crear estadísticas de columna única especificando el tamaño de muestra
Como alternativa, se puede especificar el tamaño de muestra en forma de porcentaje:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Crear estadísticas de columna única solo en algunas filas
También puede crear estadísticas en una parte de las filas de la tabla. A esto se le denomina una estadística filtrada.

Por ejemplo, puede utilizar las estadísticas filtradas si piensa consultar una partición específica de una tabla grande con particiones. Al crear estadísticas basadas únicamente en los valores de partición, mejorará la precisión de las estadísticas y, por tanto, también el rendimiento de consulta.

En este ejemplo se crean estadísticas sobre un intervalo de valores. Los valores pueden definirse fácilmente para que coincidan con el intervalo de valores de una partición.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que el optimizador de consultas considere utilizar estadísticas filtradas al elegir el plan de consulta distribuida, la consulta debe adecuarse a la definición del objeto de estadísticas. Usando el ejemplo anterior, la cláusula WHERE de la consulta tiene que especificar valores de col1 entre 2 000 101 y 20 001 231.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Crear estadísticas de columna única con todas las opciones
También puede combinar las opciones. En el ejemplo siguiente se crea un objeto de estadísticas filtradas con un tamaño personalizado de ejemplo:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obtener la referencia completa, consulte [CREATE STATISTICS][CREATE STATISTICS] en MSDN.

### <a name="create-multi-column-statistics"></a>Crear estadísticas de varias columnas
Para crear un objeto de estadísticas de varias columnas, simplemente use los ejemplos anteriores, pero especifique más columnas.

> [!NOTE]
> El histograma, que se utiliza para calcular el número de filas en el resultado de la consulta, solo está disponible para la primera columna de la definición del objeto de estadísticas.
> 
> 

En este ejemplo, el histograma se encuentra en *product\_category*. Las estadísticas entre columnas se calculan en *product\_category* y *product\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Dado que no hay una correlación entre *product\_category* y *product\_sub\_category*, un objeto de estadística de varias columnas puede ser útil si se tiene acceso a estas columnas al mismo tiempo.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Creación de estadísticas en todas las columnas de una tabla
Una forma de crear estadísticas consiste en emitir comandos CREATE STATISTICS después de crear la tabla:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Utilizar un procedimiento almacenado para crear estadísticas de todas las columnas de una base de datos
SQL Data Warehouse no tiene un procedimiento almacenado del sistema equivalente a sp_create_stats en SQL Server. Este procedimiento almacenado crea un objeto de estadísticas de columna única en todas las columnas de la base de datos que ya no tienen estadísticas.

El ejemplo siguiente le ayudará a empezar a trabajar con el diseño de la base de datos. Puede adaptarlo a sus necesidades:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para crear estadísticas de todas las columnas de la tabla con este procedimiento, simplemente llame al procedimiento.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Ejemplos: actualizar estadísticas
Para actualizar estadísticas, puede:

- Actualizar un objeto de estadísticas. Especificar el nombre del objeto de estadísticas que desea actualizar.
- Actualizar todos los objetos de estadísticas de una tabla. Especificar el nombre de la tabla en lugar de un objeto de estadísticas específico.

### <a name="update-one-specific-statistics-object"></a>Actualizar un objeto de estadísticas específico
Para actualizar un objeto de estadísticas específico, use la siguiente sintaxis:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por ejemplo: 

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Al actualizar los objetos de estadísticas específicos, puede minimizar el tiempo y los recursos necesarios para administrar las estadísticas. Esto requiere pensar cómo elegir los mejores objetos de estadísticas que se recomienda actualizar.

### <a name="update-all-statistics-on-a-table"></a>Actualizar todas las estadísticas de una tabla
Se muestra un método sencillo para actualizar todos los objetos de estadísticas de una tabla:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por ejemplo: 

```sql
UPDATE STATISTICS dbo.table1;
```

Esta instrucción es fácil de usar. Solo tiene que recordar que se actualizan *todas* las estadísticas de la tabla y, por tanto, puede realizar más trabajo del necesario. Si el rendimiento no es un problema, es la forma más fácil y más completa de garantizar que las estadísticas están actualizadas.

> [!NOTE]
> Al actualizar todas las estadísticas de una tabla, SQL Data Warehouse realiza un análisis para crear muestras de la tabla para cada objeto de estadística. Si la tabla es grande y tiene muchas columnas y estadísticas, puede resultar más eficaz actualizar las estadísticas individualmente en función de las necesidades.
> 
> 

Para obtener una implementación de un procedimiento `UPDATE STATISTICS`, consulte el artículo sobre [Tablas temporales][Temporary]. El método de implementación difiere ligeramente del procedimiento `CREATE STATISTICS`, pero el resultado es el mismo.

Para obtener la sintaxis completa, consulte [Update Statistics][Update Statistics] en MSDN.

## <a name="statistics-metadata"></a>Metadatos de las estadísticas
Hay varias funciones y vistas del sistema que puede usar para encontrar información sobre las estadísticas. Por ejemplo, puede ver si un objeto de estadísticas podría estar obsoleto usando la función stats-date para consultar la fecha en que las estadísticas se crearon o actualizaron por última vez.

### <a name="catalog-views-for-statistics"></a>Vistas de catálogo para las estadísticas
Estas vistas del sistema proporcionan información acerca de las estadísticas:

| Vista de catálogo | DESCRIPCIÓN |
|:--- |:--- |
| [sys.columns][sys.columns] |Una fila por cada columna. |
| [sys.objects][sys.objects] |Una fila por cada objeto de la base de datos. |
| [sys.schemas][sys.schemas] |Una fila por cada esquema de la base de datos. |
| [sys.stats][sys.stats] |Una fila por cada objeto de estadísticas. |
| [sys.stats_columns][sys.stats_columns] |Una fila por cada columna del objeto de estadísticas. Vínculos a la sys.columns. |
| [sys.tables][sys.tables] |Una fila por cada tabla (incluye tablas externas). |
| [sys.table_types][sys.table_types] |Una fila por cada tipo de datos. |

### <a name="system-functions-for-statistics"></a>Funciones del sistema para las estadísticas
Estas funciones del sistema son útiles para trabajar con las estadísticas:

| Función del sistema | DESCRIPCIÓN |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Fecha en que se actualizó por última vez el objeto de estadísticas. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Información resumida y detallada acerca de la distribución de valores según lo entiende el objeto de estadísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar funciones y columnas de estadísticas en una vista
Esta vista agrupa las columnas relacionadas con las estadísticas y los resultados de la función STATS_DATE().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Ejemplos de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() muestra los datos contenidos en un objeto de estadísticas. Estos datos se presentan en tres partes:

- Encabezado
- Vector de densidad
- Histograma

Los metadatos de encabezado sobre las estadísticas. El histograma muestra la distribución de valores en la primera columna de clave del objeto de estadísticas. El vector de densidad mide la correlación entre las columnas. SQL Data Warehouse calcula las estimaciones de cardinalidad con cualquiera de los datos en el objeto de estadísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar el encabezado, la densidad y el histograma
Este ejemplo muestra las tres partes de un objeto de estadísticas:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por ejemplo: 

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrar una o varias partes de DBCC SHOW_STATISTICS()
Si solo está interesado en ver partes específicas, use la cláusula `WITH` y especifique qué partes desea ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por ejemplo: 

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Diferencias de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() se implementa de forma más estricta en SQL Data Warehouse en comparación con SQL Server:

- No se admiten las características no documentadas.
- No se puede usar Stats_stream.
- No se pueden combinar resultados de subconjuntos específicos de datos de estadísticas. Por ejemplo, (STAT_HEADER JOIN DENSITY_VECTOR).
- No se puede establecer NO_INFOMSGS para la supresión de mensajes.
- No se pueden usar corchetes alrededor de los nombres de las estadísticas.
- No se pueden usar nombres de columna para identificar objetos de estadísticas.
- No se admite el error personalizado 2767.

## <a name="next-steps"></a>pasos siguientes
Para obtener más información, consulte [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] en MSDN.

  Para más información, consulte los artículos sobre [información general de tablas][Overview], [tipos de datos de tabla][Data Types], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [creación de particiones en una tabla][Partition] y [tablas temporales][Temporary].
  
   Para obtener más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para SQL Data Warehouse de Azure][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
