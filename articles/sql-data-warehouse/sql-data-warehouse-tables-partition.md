---
title: "Creación de particiones en tablas en SQL Data Warehouse | Microsoft Docs"
description: "Introducción a la creación de particiones en tablas en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a28cb1f8a2e48332b344566620dc49b29d9d3c99
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Creación de particiones de tablas en SQL Data Warehouse
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

La creación de particiones es compatible con todos los tipos de tabla de SQL Data Warehouse, entre los que se incluyen almacén de columnas en clúster, índice agrupado y montón.  La creación de particiones también es compatible con todos los tipos de distribución, incluidos la distribución por hash o la distribución Round Robin.  La creación de particiones permite dividir los datos en grupos de datos más pequeños y en la mayoría de los casos, se realiza en una columna de fecha.

## <a name="benefits-of-partitioning"></a>Ventajas de la creación de particiones
La creación de particiones puede beneficiar el mantenimiento de datos y el rendimiento de las consultas.  El hecho de que beneficie a ambos aspectos, o solo a uno de ellos, dependerá de la forma en que se carguen los datos y de la misma columna se puede usar para ambos propósitos, ya que la creación de particiones solo se puede hacer en una columna.

### <a name="benefits-to-loads"></a>Ventajas para las cargas
La principal ventaja de la creación de particiones en SQL Data Warehouse es que se mejora la eficiencia y el rendimiento de la carga de datos mediante el uso de la eliminación, conmutación y combinación de particiones.  En la mayoría de los casos, la creación de particiones de los datos se realiza en una columna de fecha que está estrechamente relacionada con el orden con el que se cargan los datos en la base de datos.  Una de las mayores ventajas de utilizar particiones para mantener los datos es que se evita el registro de transacciones.  Aunque la mera inserción, actualización o eliminación de datos puede ser el enfoque más sencillo, con un poco de esfuerzo, el uso de la creación de particiones durante el proceso de carga puede mejorar considerablemente el rendimiento.

La conmutación de particiones se puede utilizar para quitar o reemplazar rápidamente cualquier sección de una tabla.  Por ejemplo, una tabla de datos de ventas podría contener datos solo de los últimos 36 meses.  Al final de cada mes, el mes más antiguo de datos de ventas se elimina de la tabla.  Estos datos pueden eliminarse mediante el uso de una instrucción delete para eliminar los datos del mes más antiguo.  Sin embargo, la eliminación una gran cantidad de datos fila a fila con una instrucción delete puede tardar mucho tiempo, así como crear el riesgo de transacciones grandes que podría tardar mucho tiempo en revertirse si algo va mal.  Un enfoque mejor es colocar la partición de datos más antigua.  Aunque la eliminación de las filas individuales podría tardar horas, la de toda una partición puede tardar segundos.

### <a name="benefits-to-queries"></a>Ventajas para las consultas
La creación de particiones también puede utilizarse para mejorar el rendimiento de las consultas.  Una consulta que aplica un filtro a los datos con particiones puede limitar el análisis a solo las particiones idóneas. Este método de filtrado puede evitar un recorrido de tabla completa y examinar solo un subconjunto más pequeño de datos. Con la introducción de índices de almacén de columnas en clúster, las ventajas de rendimiento de la eliminación del predicado son menores, pero en algunos casos puede haber beneficios para las consultas.  Por ejemplo, si la tabla de datos de ventas se particiona en 36 meses con el campo de fecha de ventas, las consultas que se filtren por esa fecha de ventas pueden omitir la búsqueda en las particiones que no se ajusten al filtro.

## <a name="partition-sizing-guidance"></a>Guía sobre el tamaño de las particiones
Aunque la creación de particiones se puede usar para mejorar el rendimiento en algunos escenarios, la creación de una tabla con **demasiadas** particiones pueden afectar negativamente al rendimiento en algunas circunstancias.  Estas cuestiones se dan especialmente en las tablas de almacén de columnas en clúster.  Para que la creación de particiones sea útil, es importante saber cuándo usarla y el número de particiones que se deben crear.  No hay ninguna regla inamovible con respecto a cuántas particiones son demasiadas, depende de los datos y del número de particiones en que se realiza la carga de manera simultánea.  Normalmente, un esquema de partición correcta tiene decenas o centenas de particiones, no miles.

Al crear particiones en tablas de **almacén de columnas en clúster**, es importante considerar el número de filas que pertenece a cada partición.  Para que tanto la compresión como el rendimiento de las tablas de almacén de columnas en clúster sean óptimos, se necesita un mínimo de un millón de filas por partición y distribución.  Antes de que se creen particiones, SQL Data Warehouse ya divide cada tabla en 60 bases de datos distribuidas.  Todas las particiones que se agreguen a una tabla se sumarán a las distribuciones creadas en segundo plano.  Usando este ejemplo, si la tabla de datos de ventas contenía 36 particiones mensuales, y dado que SQL Data Warehouse tiene 60 distribuciones, la tabla de datos de ventas debería contener 60 millones de filas por mes, o 2 100 millones de filas cuando se rellenen todos los meses.  Si una tabla contiene muchas menos filas que el número mínimo recomendado de filas por partición, considere utilizar menos particiones para hacer que aumente el número de filas por partición.  Consulte también el artículo sobre [indexación][Index] que incluye las consultas que se pueden ejecutar en SQL Data Warehouse para evaluar la calidad de los índices de almacén de columnas en clúster.

## <a name="syntax-difference-from-sql-server"></a>Diferencia con respecto a la sintaxis de SQL Server
SQL Data Warehouse introduce una manera simplificada de definir particiones que difiere ligeramente de la de SQL Server.  Las funciones y los esquemas de la creación de particiones no se usan en SQL Data Warehouse como en SQL Server.  En su lugar, lo único que se debe hacer es identificar la columna con particiones y los puntos limítrofes.  Aunque la sintaxis de la creación de particiones puede variar ligeramente con respecto a la de SQL Server, los conceptos básicos son los mismos.  SQL Server y SQL Data Warehouse admiten una columna de partición por tabla, que puede ser una partición con intervalos.  Para obtener más información sobre las particiones, consulte [Tablas e índices con particiones][Partitioned Tables and Indexes].

El siguiente ejemplo de una instrucción [CREATE TABLE][CREATE TABLE] con particiones de SQL Data Warehouse particiona la tabla FactInternetSales en la columna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migración de particiones de SQL Server
Para migrar las definiciones de particiones de SQL Server a SQL Data Warehouse:

* Elimine el [esquema de particiones][partition scheme] de SQL Server.
* Agregue la definición de [función de partición][partition function] a CREATE TABLE.

Si va a migrar una tabla con particiones de una instancia de SQL Server, la instrucción SQL siguiente puede ayudarle a consultar el número de filas que se encuentran en cada partición.  Tenga en cuenta que si se utiliza la misma granularidad de particiones en SQL Data Warehouse, el número de filas por partición se reduce en un factor de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Administración de cargas de trabajo
Una última consideración que debe tenerse a la hora de decidir el número de particiones de una tabla es la [administración de la carga de trabajo][workload management].  La administración de la carga de trabajo en SQL Data Warehouse es principalmente la administración de memoria y de la concurrencia.  En SQL Data Warehouse, las clases de recursos son las que rigen la cantidad máxima de memoria que se asigna a cada distribución durante la ejecución de la consulta.  Lo ideal es que los tamaños de las particiones dependan de otros factores, como las necesidades de creación de índices de almacén de columnas en clúster que tiene la memoria.  Los índices de almacén de columnas en clúster suponen una mayor ventaja cuando se les asigna más memoria.  Por lo tanto, deberá asegurarse de que ninguna recompilación del índice de particiones se quede sin memoria. Para aumentar la cantidad de memoria disponible para las consultas se puede conmutar del rol predeterminado, smallrc, a uno de los otros roles, como largerc.

Para obtener información sobre la asignación de memoria por distribución, consulte las vistas de administración dinámica del regulador de recursos. En realidad, la concesión de memoria será inferior a la que se indica en las ilustraciones siguientes. Sin embargo, esto proporciona un nivel de instrucciones que puede utilizar al cambiar el tamaño de las particiones para las operaciones de administración de datos.  Intente evitar cambiar el tamaño de las particiones por encima de la concesión de memoria proporcionada por la clase de recurso extra grande. Si las particiones crecen más allá de lo que se muestra en esta ilustración, se corre el riesgo de presionar la memoria, lo que a su vez conlleva una compresión menos óptima.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Modificación de particiones
SQL Data Warehouse admite la separación, combinación y modificación de particiones. Cada una de estas funciones se ejecuta mediante la instrucción [ALTER TABLE][ALTER TABLE].

Para cambiar particiones entre dos tablas, debe asegurarse de que las particiones se alinean en sus límites correspondientes y que las definiciones de tablas coinciden. Como las restricciones check no están disponibles para aplicar el intervalo de valores en una tabla, la tabla de origen debe contener los mismos límites de partición que la tabla de destino. Si no es así, el modificador de particionamiento generará un error, ya que los metadatos de la partición no estarán sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>División de una partición que contiene datos
El método más eficaz para dividir una partición que ya contiene datos es usar una instrucción `CTAS` . Si la tabla con particiones es un almacén de columnas en clúster, la partición de tabla debe estar vacía antes de que se pueda dividir.

A continuación, se muestra una tabla de ejemplo con particiones del almacén de columnas que contiene una fila en cada partición:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Al crear el objeto estadístico, nos aseguramos de que los metadatos de la tabla son más precisos. Si se omite la creación de estadísticas, SQL Data Warehouse utilizará los valores predeterminados. Para obtener detalles sobre las estadísticas, consulte las [estadísticas][statistics].
> 
> 

A continuación, podemos consultar el recuento de filas mediante la vista de catálogo `sys.partitions` :

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Si intentamos dividir esta tabla, se producirá un error:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

La cláusula Msg 35346, Level 15, State 1, Line 44 SPLIT de la instrucción ALTER PARTITION ha generado un error porque la partición no está vacía.  Solo las particiones vacías se pueden dividir cuando existe un índice de almacén de columnas en la tabla. Considere la posibilidad de deshabilitar el índice de almacén de columnas antes de emitir la instrucción ALTER PARTITION y, a continuación, vuelva a generar el índice de almacén de columnas una vez completada la instrucción ALTER PARTITION.

Sin embargo, se puede utilizar `CTAS` para crear una nueva tabla que contenga los datos.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Se permite la modificación porque los límites de partición están alineados. Esto dejará la tabla de origen con una partición vacía que podemos dividir posteriormente.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Lo único que falta por hacer es alinear los datos con los nuevos límites de partición mediante `CTAS` y volver a cambiar los datos a la tabla principal.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Después de haber transferido los datos, es buena idea actualizar las estadísticas en la tabla de destino para asegurarse de que reflejan con precisión la distribución nueva de los datos en sus respectivas particiones:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Control de código fuente de particiones de tabla
Para evitar que la definición de tabla se **oxide** en el sistema de control de código fuente, es conveniente tener en cuenta lo siguiente:

1. Crear la tabla como una tabla con particiones, pero sin valores de partición

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT` la tabla como parte del proceso de implementación:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Con este enfoque, el código de control de código fuente permanece estático y se permite que los valores de límite de partición sean dinámicos, de tal forma que evoluciona con el almacenamiento de datos con el tiempo.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los artículos sobre [información general de tablas][Overview], [tipos de datos de tabla][Data Types], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [mantenimiento de estadísticas de tabla][Statistics] y [tablas temporales][Temporary].  Para obtener más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para SQL Data Warehouse de Azure][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
