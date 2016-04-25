<properties
   pageTitle="Administración de índices de almacén de columnas en Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Tutorial de administración de índices de almacén de columnas para mejorar el rendimiento de las consultas y la compresión en Almacenamiento de datos SQL de Azure."
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Administración de índices de almacén de columnas en Almacenamiento de datos SQL de Azure.

En este tutorial se explica cómo administrar índices de almacén de columnas para mejorar el rendimiento de las consultas.

Las consultas realizadas en índices de almacén de columnas se ejecutan mejor cuando el índice comprime las filas en "grupos de filas" de un millón de filas (1 048 576 para ser exacto). Esto aporta la mejor compresión y el mejor rendimiento de las consultas. Pero pueden darse condiciones en las que los grupos de filas tengan muchas menos de un millón de filas. Cuando los grupos de filas no estén densamente rellenos de filas, considere la posibilidad de realizar ajustes.

En este tutorial, aprenderá a:

- Uso de metadatos para determinar el promedio de filas por grupo
- Análisis de la causa principal de que haya grupos de filas con pocas filas
- Recompilación de un índice de almacén de columnas para volver a comprimir todas las filas en nuevos grupos de filas 

Para conocer los conceptos básicos sobre los índices de almacén de columnas, consulte [Descripción de los índices de almacén de columnas](https://msdn.microsoft.com/library/gg492088.aspx).

## Paso 1: Creación de una vista que muestra metadatos de grupo de filas

En esta vista se calcula el promedio de filas por grupo de filas. También se muestra información adicional sobre grupos de filas.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

## Paso 2: Consulta de la vista

Ahora que ha creado la vista, ejecute esta consulta de ejemplo para ver los metadatos de grupo de filas del índice de almacén de columnas.

```sql
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Paso 3: Analizar los resultados

Una vez ejecutada la consulta, puede empezar a examinar los datos y analizar los resultados. En esta tabla se explica lo que hay que buscar en el análisis de grupo de filas.


| Columna | Uso de estos datos |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Si la tabla tiene particiones, puede que vea mayores recuentos de grupos de filas abiertos. En teoría, cada partición de la distribución podría tener asociado un grupo de filas abierto. Incluir esto en el análisis. Una tabla pequeña en la que se hayan incluido particiones podría optimizarse eliminando por completo las particiones, ya que esto mejoraría la compresión. |
| [row\_count\_total] | Número total de filas de la tabla. Por ejemplo, puede usar este valor para calcular el porcentaje de filas en estado comprimido. |
| [row\_count\_per\_distribution\_MAX] | Si todas las filas se distribuyen uniformemente, este valor sería el número de filas de destino por cada distribución. Compare este valor con el compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Número total de filas en formato de almacén de columnas de la tabla. |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Si el número medio de filas es mucho menor que el número máximo de filas para un grupo de filas, considere la posibilidad de usar CTAS o ALTER INDEX REBUILD para volver a comprimir los datos. |
| [COMPRESSED\_rowgroup\_count] | Número de grupos de filas en formato de almacén de columnas. Si este número es muy alto con respecto a la tabla es un indicador de que la densidad del almacén de columnas es baja. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Las filas se eliminan lógicamente en formato de almacén de columnas. Si el número es alto con respecto al tamaño de la tabla, considere la posibilidad de volver a crear la partición o recompilar el índice ya que esto las quita físicamente. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Utilícela con las columnas AVG y MAX para entender el intervalo de valores para los grupos de filas del almacén de columnas. Un número bajo por encima del umbral de carga (102 400 por cada distribución de particiones alineada) sugiere que hay optimizaciones disponibles en la carga de datos |
| [COMPRESSED\_rowgroup\_rows\_MAX] | Mismo caso anterior. |
| [OPEN\_rowgroup\_count] | Los grupos de filas abiertos son normales. Cabe esperar un grupo de filas OPEN por cada distribución de tabla (60). Cantidades excesivas sugieren carga de datos entre particiones. Compruebe la estrategia de creación de particiones para asegurarse de que es válida. |
| [OPEN\_rowgroup\_rows] | Cada grupo de filas puede incluir como máximo 1 048 576 filas. Use este valor para ver cuántos grupos de filas abiertos hay actualmente. |
| [OPEN\_rowgroup\_rows\_MIN] | Los grupos abiertos indican que los datos se cargan poco a poco en la tabla o que la carga anterior distribuyó las filas restantes en este grupo de filas. Utilice las columnas MIN, MAX, AVG para ver cuántos datos se encuentran en grupos de filas OPEN. En tablas pequeñas, podría ser el 100% de los datos. En este caso use ALTER INDEX REBUILD para forzar los datos a pasar al almacén de columnas. |
| [OPEN\_rowgroup\_rows\_MAX] | Mismo caso anterior. |
| [OPEN\_rowgroup\_rows\_AVG] | Mismo caso anterior. |
| [CLOSED\_rowgroup\_rows] | Examine las filas de grupos de filas cerrados para comprobar su integridad. Si hay |
| [CLOSED\_rowgroup\_count] | El número de grupos de filas cerrados debe ser bajo, si es que hay alguno. Los grupos de filas cerrados pueden convertirse en grupos de filas comprimidos mediante el comando ALTER INDEX... REORGANISE. Pero esto no suele ser necesario. Los grupos cerrados se convierten automáticamente en grupos de filas del almacén de columnas por el proceso en segundo plano "motor de tupla". |
| [CLOSED\_rowgroup\_rows\_MIN] | Los grupos de filas cerrados deben tener una tasa de relleno muy alto. Si la tasa de relleno de un grupo de filas cerrado es baja, se requiere un análisis más profundo del almacén de columnas. |
| [CLOSED\_rowgroup\_rows\_MAX] | Mismo caso anterior. |
| [CLOSED\_rowgroup\_rows\_AVG] | Mismo caso anterior. |


## Paso 4: Examen de la causa principal

El examen de la causa principal le ayudará a saber si puede realizar el diseño de tabla, cargar o procesar otros cambios que mejorarán la densidad de la fila de los grupos de filas; lo que mejora la compresión y el rendimiento de las consultas.

Estos factores pueden ser la causa de que un índice de almacén de columnas tenga muchas menos de 1 048 576 filas por grupo de filas. También pueden provocar que las filas vayan al grupo de filas delta y no a un grupo de filas comprimido.

1. Operaciones de DML intensivas
2. Operaciones de carga pequeña o lenta
3. Demasiadas particiones

### Operaciones de DML intensivas** 

Las operaciones de DML intensivas que actualizan y eliminan filas reducen la eficacia del almacén de columnas. Esto es especialmente cierto cuando se modifica la mayoría de las filas de un grupo de filas.

- La eliminación de una fila de un grupo de filas comprimido solo marca lógicamente la fila como eliminada. La fila permanece en el grupo de filas comprimido hasta que la partición o tabla se recompile.
- La inserción de una fila agrega la fila a una tabla de almacén de filas interna denominada grupo de filas delta. La fila insertada no se convierte en almacén de columnas hasta que el grupo de filas delta esté lleno y se marque como cerrada. Los grupos de filas se cierran cuando alcanzan la capacidad máxima de 1 048 576 filas. 
- La actualización de una fila en formato de almacén de columnas se procesa como eliminación lógica y luego como inserción. La fila insertada se almacenará en el almacén delta.

Las operaciones de actualización e inserción por lotes que superen el umbral en masa de 102 400 filas por distribución de particiones alineada se escribirán directamente en formato de almacén de columnas. Pero para ello, suponiendo una distribución uniforme, tendría que estar modificando más de 6.144 millones de filas en una sola operación. Si el número de filas de una determinada distribución de particiones alineada es inferior a 102 400, las filas irán al almacén delta y permanecerán allí hasta que se hayan insertado o modificado suficientes filas para cerrar el grupo de filas o se haya recompilado el índice.

### Operaciones de carga pequeña o lenta

Las cargas pequeñas que fluyen al Almacenamiento de datos SQL también se denominan cargas lentas. Normalmente, representan una transmisión prácticamente constante de los datos que ingiere el sistema. Pero como esta transmisión es casi continua, el volumen de filas no es especialmente grande. Con frecuencia los datos están significativamente por debajo del umbral necesario para una carga directa en formato de almacén de columnas.

En estas situaciones es preferible que los datos vayan primero a Almacenamiento de blobs de Azure y dejar que se acumulan antes de cargarlos. Esta técnica se conoce como *procesamiento por microlotes*.

### Demasiadas particiones

Es posible que tenga demasiadas particiones. En la arquitectura de procesamiento paralelo masivo (MPP), una tabla definida por el usuario se distribuye e implementa en segundo plano como 60 tablas. Por consiguiente, cada índice de almacén de columnas se implementa como 60 índices de almacén de columnas. Asimismo, cada partición se implementa en los 60 índices de almacén de columnas. Se trata de una **gran diferencia** con respecto a SQL Server, que tiene una arquitectura de multiproceso simétrico (SMP).

Si carga 1 048 576 filas en una sola partición de una tabla de SMP de SQL Server, se comprimirá en el almacén de columnas. Pero si carga 1 048 576 filas en una sola partición de una tabla de Almacenamiento de datos SQL, solo irán 17 467 filas a cada una de las 60 distribuciones (suponiendo la distribución uniforme de los datos). Puesto que 17 467 es menor que el umbral de 102 400 filas por distribución, Almacenamiento de datos SQL incluirá los datos en grupos de filas de almacén delta. Por lo tanto, para cargar filas directamente en formato comprimido de almacén de columnas, debe cargar más de 6,1444 millones de filas (60 x 102 400) en una sola partición de una tabla de Almacenamiento de datos SQL.

## Paso 5: Asignación de recursos de proceso adicionales

La recompilación de índices, sobre todo en tablas especialmente grandes, a menudo requiere recursos adicionales. Almacenamiento de datos SQL tiene una característica de administración de cargas de trabajo que puede asignar más memoria a un usuario. Para entender cómo reservar más memoria para las recompilaciones de índice, consulte la sección de administración de cargas de trabajo del artículo sobre [simultaneidad][].

## Paso 6: Recompilación del índice para mejorar el promedio de filas por grupo de filas

Para combinar los grupos de filas comprimidos existentes y llevar los grupos de filas delta al almacén de columnas, debe recompilar el índice. Normalmente, hay demasiados datos para recompilar todo el índice, por lo que es mejor recompilar una o más particiones. En Almacenamiento de datos SQL, puede recompilar el índice de una de las dos maneras siguientes:

1. Use [ALTER INDEX][] para recompilar una partición.
2. Use [CTAS][] para volver a crear una partición en una nueva tabla y luego use la modificación de particiones para devolver la partición a la tabla original.

¿De qué manera es mejor? En el caso de grandes volúmenes de datos, [CTAS][] suele ser más rápido que [ALTER INDEX][]. Para volúmenes menores de datos, [ALTER INDEX][] es más fácil de usar.

### Método #1: Uso de ALTER INDEX para recompilar pequeñas cantidades de datos sin conexión

En estos ejemplos se muestra cómo recompilar el índice de almacén de columnas completo y recompilar una sola partición. En tablas de gran tamaño, solo resulta práctico generar una sola partición. Se trata de una operación sin conexión.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

Para más información, consulte la sección ALTER INDEX REBUILD de [Desfragmentación de índices de almacén de columnas](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1) y el tema de sintaxis [ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx).

### Método #2: Uso de CTAS para recompilar y modificar particiones de grandes volúmenes de datos en línea

En este ejemplo se usa [CTAS][] y la modificación de particiones para recompilar una partición de tabla.


```sql
-- Step 01. Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para más información sobre cómo volver a crear particiones mediante `CTAS`, consulte [particiones de tabla][] y [simultaneidad][].


## Pasos siguientes

Para recomendaciones más detalladas sobre la administración de índices, consulte el artículo [administración de índices][].

Para obtener más sugerencias de administración, consulte la información general de [administración][].

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[particiones de tabla]: sql-data-warehouse-develop-table-partitions.md
[simultaneidad]: sql-data-warehouse-develop-concurrency.md
[administración]: sql-data-warehouse-manage-monitor.md
[administración de índices]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->