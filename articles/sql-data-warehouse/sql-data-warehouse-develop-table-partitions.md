<properties
   pageTitle="Particiones de tablas en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar las particiones de tabla en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Particiones de tabla en el Almacenamiento de datos SQL

Para migrar las definiciones de las particiones de SQL Server al Almacenamiento de datos SQL:

- Quite esquemas y funciones de partición de SQL Server, ya que esto se administra automáticamente al crear la tabla.
- Defina las particiones al crear la tabla. Simplemente especifique puntos límite de partición y, si desea que el punto límite sea eficaz, `RANGE RIGHT` o `RANGE LEFT`.

### Tamaño de la partición
El tamaño de la partición es una consideración importante para el Almacenamiento de datos SQL. Normalmente, las operaciones de administración de datos y las rutinas de carga de datos se centran en particiones individuales en lugar de tratar toda la tabla de una sola vez. Esto es especialmente relevante para el almacenamiento de columnas en clúster (CCI). El CCI puede consumir gran cantidad de memoria. Por lo tanto, aunque nos conviene revisar la granularidad del plan de partición, no queremos cambiar el tamaño de las particiones a un tamaño que conlleve presionar la memoria al tratar de ejecutar tareas de administración.

Al decidir la granularidad de las particiones, es importante recordar que el Almacenamiento de datos SQL distribuirá automáticamente los datos en las distribuciones. En consecuencia, los datos que existirían normalmente en una tabla en una partición de una base de datos SQL Server, ahora se encuentran en una partición en muchas tablas de una base de datos del Almacenamiento de datos SQL. Para mantener un número significativo de filas en cada partición, se suele cambiar el tamaño del límite de partición. Por ejemplo, si ha utilizado la creación de particiones de nivel de día para el almacenamiento de datos, es conveniente que tenga en cuenta una granularidad algo inferior, como los meses o trimestres.

Para cambiar el tamaño de la base de datos actual al nivel de partición, use una consulta como la siguiente:

```
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
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
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

El número total de distribuciones equivale al número de ubicaciones de almacenamiento que se utilizan cuando se crea una tabla. Es una forma compleja de decir que cada tabla se crea una vez por cada distribución.

También puede prever aproximadamente el número de filas y, por tanto, qué tamaño tendrá cada partición. La partición del almacenamiento de datos de origen se subdividirá en cada distribución.

Utilice el siguiente cálculo para orientarse al determinar el tamaño de la partición:

Tamaño de la partición MPP = Tamaño de la partición SMP/ Número de distribuciones

Puede averiguar cuántas distribuciones de la base de datos del Almacenamiento de datos SQL tiene con la consulta siguiente:

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Ahora que ya sabe el tamaño de cada partición del sistema de origen y el tamaño previsto para SQLDW, puede decidir el límite de partición.

### Administración de cargas de trabajo
Un último detalle que debe tener en cuenta para decidir la partición de tabla es la administración de la carga de trabajo. Esta característica controla la memoria máxima asignada para cada distribución durante la ejecución de la consulta en el Almacenamiento de datos SQL. Consulte el siguiente artículo para obtener más detalles sobre la [administración de cargas de trabajo]. Lo ideal es cambiar el tamaño de la partición con operaciones en memoria, teniendo en cuenta las recompilaciones de índices de almacén de columnas. Una recompilación de índice es una operación de uso intensivo de memoria. Por lo tanto, deberá asegurarse de que la recompilación del índice de la partición no colapsa la memoria. Se puede aumentar la cantidad de memoria disponible para las consultas al cambiar del rol predeterminado a otros roles disponibles.

Para obtener información sobre la asignación de memoria por distribución, consulte las vistas de administración dinámica del regulador de recursos. En realidad, la concesión de memoria será inferior a la que se indica en las ilustraciones siguientes. Sin embargo, esto proporciona un nivel de instrucciones que puede utilizar al cambiar el tamaño de las particiones para las operaciones de administración de datos.

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
```

> [AZURE.NOTE]Intente evitar cambiar el tamaño de las particiones por encima de la concesión de memoria proporcionada por la clase de recurso extra grande. Si las particiones crecen más allá de lo que se muestra en esta ilustración, se corre el riesgo de presionar la memoria, lo que a su vez conlleva una compresión menos óptima.

## Modificación de particiones
Para cambiar particiones entre dos tablas, debe asegurarse de que las particiones se alinean en sus límites correspondientes y que las definiciones de tablas coinciden. Como las restricciones check no están disponibles para aplicar el intervalo de valores en una tabla, la tabla de origen debe contener los mismos límites de partición que la tabla de destino. Si no es el caso, la modificación de la partición generará un error porque los metadatos de la partición no estarán sincronizados.

### División de una partición que contiene datos
El método más eficaz para dividir una partición que ya contiene datos es usar una instrucción `CTAS`. Si la tabla con particiones es un almacén de columnas en clúster, la partición de tabla debe estar vacía antes de que se pueda dividir.

A continuación, se muestra una tabla de ejemplo con particiones del almacén de columnas que contiene una fila en la última partición:

```
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
VALUES (1,20010101,1,1,1,1,1,1)

CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey)
```

> [AZURE.NOTE]Al crear el objeto estadístico, nos aseguramos de que los metadatos de tabla son más precisos. Si omitimos la creación de estadísticas, el Almacenamiento de datos SQL utilizará los valores predeterminados. Para obtener detalles sobre las estadísticas, consulte las [estadísticas][].

A continuación, podemos consultar el recuento de filas mediante la vista de catálogo `sys.partitions`:

```
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
```

Si tratamos de dividir esta tabla, se producirá un error:

```
ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

La cláusula Msg 35346, Level 15, State 1, Line 44 SPLIT de la instrucción ALTER PARTITION ha generado un error porque la partición no está vacía. Solo las particiones vacías se pueden dividir cuando existe un índice de almacén de columnas en la tabla. Considere la posibilidad de deshabilitar el índice de almacén de columnas antes de emitir la instrucción ALTER PARTITION y, a continuación, vuelva a generar el índice de almacén de columnas una vez completada la instrucción ALTER PARTITION.

Sin embargo, podemos utilizar `CTAS` para crear una nueva tabla para alojar nuestros datos.

```
CREATE TABLE dbo.FactInternetSales_20010101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101
                                )
                            )
            )
AS
SELECT *
FROM	FactInternetSales
WHERE	1=2
```

Se permite la modificación porque los límites de partición están alineados. Esto dejará la tabla de origen con una partición vacía que podemos dividir posteriormente.

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20010101 PARTITION 2

ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

Lo único que falta por hacer es alinear los datos con los nuevos límites de partición mediante `CTAS` y volver a cambiar los datos a la tabla principal.

```
CREATE TABLE [dbo].[FactInternetSales_20010101_20020101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101,20020101
                                )
                            )
            )
AS
SELECT  *
FROM	[dbo].[FactInternetSales_20010101]
WHERE	[OrderDateKey] >= 20010101
AND     [OrderDateKey] <  20020101

ALTER TABLE FactInternetSales_20010101_20020101 SWITCH PARTITION 3 TO  FactInternetSales PARTITION 3
```

Después de haber transferido los datos, es buena idea actualizar las estadísticas en la tabla de destino para asegurarse de que reflejan con precisión la distribución nueva de los datos en sus respectivas particiones:

```
UPDATE STATISTICS [dbo].[FactInternetSales]
```

### Control de código fuente de particiones de tabla
Para evitar que la definición de tabla se **oxide** en el sistema de control de código fuente, es conveniente tener en cuenta lo siguiente:

1. Crear la tabla como una tabla con particiones, pero sin valores de partición

```
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

2. `SPLIT` la tabla como parte del proceso de implementación:

```
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
,       @i INT = 1                     --iterator for while loop
,       @q NVARCHAR(4000)              --query
,       @p NVARCHAR(20)     = N''      --partition_number
,       @s NVARCHAR(128)    = N'dbo'   --schema
,       @t NVARCHAR(128)    = N'table' --table
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

>[AZURE.NOTE]El cambio de partición tiene algunas diferencias en comparación con SQL Server. Lea [Migración del código][] para obtener más información sobre este tema.


## Pasos siguientes
Una vez migrado correctamente el esquema de base de datos al Almacenamiento de datos SQL, puede continuar con uno de los siguientes artículos:

- [Migración de los datos][]
- [Migración del código][]

<!--Image references-->

<!-- Article references -->
[Migración del código]: sql-data-warehouse-migrate-code.md
[Migración de los datos]: sql-data-warehouse-migrate-data.md
[estadísticas]: sql-data-warehouse-develop-statistics.md
[administración de cargas de trabajo]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=July15_HO3-->