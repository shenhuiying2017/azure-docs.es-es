---
title: "Indexación de tablas en SQL Data Warehouse | Microsoft Azure"
description: "Introducción a la indexación de tablas en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 3e617674-7b62-43ab-9ca2-3f40c41d5a88
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 672270536a7405e617edbcf5ec0e6eff68be7fde
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexación de tablas en SQL Data Warehouse
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

SQL Data Warehouse ofrece varias opciones de indexación, entre las que se incluyen [índices de almacén de columnas][clustered columnstore indexes] e [índices agrupados y no agrupados descritos][clustered indexes and nonclustered indexes].  Además, también no ofrece una opción sin índice, que también se conoce como [montón][heap].  En este artículo se tratan las ventajas de cada tipo de índice, así como sugerencias para obtener el máximo rendimiento de los índices. Para obtener más información sobre cómo crear tablas en SQL Data Warehouse, consulte [create table syntax][create table syntax] (Sintaxis de CREATE TABLE).

## <a name="clustered-columnstore-indexes"></a>Índices de almacén de columnas en clúster
De forma predeterminada, SQL Data Warehouse crea un índice de almacén de columnas en clúster cuando se especifican opciones sin índice en una tabla. Las tablas del almacén de columnas en clúster ofrecen el máximo nivel de compresión de datos, así como el mejor rendimiento general de las consultas.  Por lo general, las tablas del almacén de columnas en clúster funcionan mejor que las tablas de montón o de índice agrupado, y suelen ser la mejor opción para tablas grandes.  Por estos motivos, el almacén de columnas agrupado es el mejor lugar por el que empezar cuando no se sabe cómo indexar una tabla.  

Para crear una tabla de almacén de columnas en clúster, simplemente especifique CLUSTERED COLUMNSTORE INDEX en la cláusula WITH u omita la cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Hay algunos escenarios en los que un almacén de columnas en clúster puede que no sea una buena opción:

* Las tablas de almacén de columnas no admiten varchar (max), nvarchar (max) y varbinary (max).  Considere la posibilidad de usar un índice agrupado o de montón en su lugar.
* Las tablas de almacén de columnas pueden que sean menos eficientes para datos transitorios.  Considere la posibilidad de usar tablas de montón, e incluso tablas temporales.
* Tablas pequeñas con menos de 100 millones de filas.  Considere la posibilidad de usar tablas de montón.

## <a name="heap-tables"></a>Tablas de montón
Cuando almacene datos temporalmente en SQL Data Warehouse, las tablas de apilamiento pueden agilizar el proceso global.  Esto se debe a que las cargas en montones son más rápidas que la indexación de tablas y, en algunos casos, la posterior lectura se puede realizar desde la memoria caché.  Si solo carga datos para transformarlos después, cargar la tabla de apilamiento será mucho más rápido que cargar los datos en una tabla de almacén de columnas agrupadas. Además, la carga de datos en una [tabla temporal][Temporary] también es una operación mucho más rápida que la carga de una tablas en un almacenamiento permanente.  

Para tablas de búsqueda pequeñas, con menos de 100 millones de filas, a menudo tiene sentido usar tablas de montón.  Las tablas de almacén de columnas en clúster empiezan a lograr una compresión óptima cuando hay más de 100 millones de filas.

Para crear una tabla de montón, solo es preciso especificar HEAP en la cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices clúster y no clúster
Los índices clúster pueden ser mejores que las tablas del almacén de columnas en clúster cuando se necesita recuperar rápidamente una sola fila.  En el caso de las consultas en que se requiera realizar una búsqueda en una sola o en muy pocas filas a gran velocidad, considere la posibilidad de un índice clúster o un índice no clúster secundario.  La desventaja de utilizar un índice clúster es que solo se beneficiarán las consultas que utilicen un filtro muy selectivo en la columna del índice clúster.  Para mejorar el filtro en las restantes columnas se puede agregar un índice no clúster a las mismas.  Sin embargo, cada índice que se agrega a una tabla agregará espacio y tiempo de procesamiento a las cargas.

Para crear una tabla de índice clúster, solo es preciso especificar CLUSTERED INDEX en la cláusula WITH:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para agregar un índice no agrupado en una tabla, simplemente use la sintaxis siguiente:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Optimización de índices de almacén de columnas en clúster
Las tablas de almacén de columnas en clúster organizan los datos en segmentos.  El hecho de que los segmentos sean de gran calidad es fundamental para conseguir un rendimiento óptimo de las consultas en una tabla de almacén de columnas.  La calidad de los segmentos se puede medir por el número de filas de un grupo de filas comprimido.  La calidad de los segmentos es mayor donde hay al menos 100 000 filas por grupo de filas comprimido y su rendimiento aumenta a medida que el número de filas por grupo se aproxima a 1.048.576, que es el número máximo de filas que puede contener un grupo.

La siguiente vista se puede crear y utilizar en un sistema para calcular el promedio de filas por cada fila de grupo e identificar los índices de almacén de columnas de clúster que no llegan a ser óptimos.  La última columna de esta vista se generará como una instrucción SQL que se puede usar para volver a generar los índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
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
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Ahora que ha creado la vista, ejecute esta consulta para identificar las tablas con grupos de filas con menos de 100 000 filas.  Por supuesto, puede aumentar el umbral de 100 000 si desea obtener mayor calidad de los segmentos. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Una vez ejecutada la consulta, puede empezar a examinar los datos y analizar los resultados. En esta tabla se explica lo que hay que buscar en el análisis de los grupos de filas.

| Columna | Uso de estos datos |
| --- | --- |
| [table_partition_count] |Si la tabla tiene particiones, puede que vea mayores recuentos de grupos de filas abiertos. En teoría, cada una de las particiones de la distribución puede tener asociado un grupo de filas abierto. Incluir esto en el análisis. Una tabla pequeña con particiones se puede optimizar eliminando por completo todas las particiones, ya que esto mejoraría la compresión. |
| [row_count_total] |Número total de filas de la tabla. Por ejemplo, puede usar este valor para calcular el porcentaje de filas en estado comprimido. |
| [row_count_per_distribution_MAX] |Si todas las filas se distribuyen uniformemente, este valor sería el número de filas de destino por cada distribución. Compare este valor con el compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de filas en formato de almacén de columnas de la tabla. |
| [COMPRESSED_rowgroup_rows_AVG] |Si el número medio de filas es mucho menor que el número máximo de filas de un grupo de filas, considere la posibilidad de usar CTAS o ALTER INDEX REBUILD para volver a comprimir los datos. |
| [COMPRESSED_rowgroup_count] |Número de grupos de filas en formato de almacén de columnas. Si este número es muy alto con respecto a la tabla es un indicador de que la densidad del almacén de columnas es baja. |
| [COMPRESSED_rowgroup_rows_DELETED] |Las filas se eliminan lógicamente en formato de almacén de columnas. Si el número es alto con respecto al tamaño de la tabla, considere la posibilidad de volver a crear la partición o de volver a compilar el índice, ya que esto las quita físicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Utilícelo en conjunción con las columnas AVG y MAX para conocer el intervalo de valores para los grupos de filas del almacén de columnas. Un número bajo por encima del umbral de carga (102 400 por cada distribución de particiones alineada) sugiere que hay optimizaciones disponibles en la carga de datos |
| [COMPRESSED_rowgroup_rows_MAX] |Mismo caso anterior. |
| [OPEN_rowgroup_count] |Los grupos de filas abiertos son normales. Cabe esperar un grupo de filas OPEN por cada distribución de tabla (60). Cantidades excesivas sugieren carga de datos entre particiones. Compruebe la estrategia de creación de particiones para asegurarse de que es válida. |
| [OPEN_rowgroup_rows] |Cada grupo de filas puede incluir un máximo de 1 048 576 filas. Use este valor para ver lo llenos que están actualmente los grupos de filas abiertos |
| [OPEN_rowgroup_rows_MIN] |Los grupos abiertos indican que los datos se cargan poco a poco en la tabla o que la carga anterior distribuyó las filas restantes en este grupo de filas. Utilice las columnas MIN, MAX, AVG para ver cuántos datos se encuentran en los grupos de filas OPEN. En tablas pequeñas, podría ser el 100% de los datos. En este caso use ALTER INDEX REBUILD para forzar los datos a pasar al almacén de columnas. |
| [OPEN_rowgroup_rows_MAX] |Mismo caso anterior. |
| [OPEN_rowgroup_rows_AVG] |Mismo caso anterior. |
| [CLOSED_rowgroup_rows] |Examine las filas de grupos de filas cerrados para comprobar su integridad. |
| [CLOSED_rowgroup_count] |El número de grupos de filas cerrados debe ser bajo, si es que hay alguno. Los grupos de filas cerrados pueden convertirse en grupos de filas comprimidos mediante el comando ALTER INDEX... REORGANISE. Pero esto no suele ser necesario. Los grupos cerrados se convierten automáticamente en grupos de filas de almacén de columnas mediante el proceso en segundo plano "motor de tupla". |
| [CLOSED_rowgroup_rows_MIN] |Los grupos de filas cerrados deben tener una velocidad de relleno muy alta. Si la velocidad de relleno de un grupo de filas cerrado es baja, se requiere un análisis más profundo del almacén de columnas. |
| [CLOSED_rowgroup_rows_MAX] |Mismo caso anterior. |
| [CLOSED_rowgroup_rows_AVG] |Mismo caso anterior. |
| [Rebuild_Index_SQL] |SQL para volver a generar un índice de almacén de columnas de una tabla |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de una calidad deficiente del índice de almacén de columnas
Si ha identificado las tablas con una calidad deficiente de sus segmentos, deseará identificar la causa raíz.  A continuación se muestran otras causas comunes de baja calidad de los segmentos:

1. Presión de la memoria cuando se generó el índice
2. Gran volumen de operaciones de DML
3. Operaciones de carga pequeña o lenta
4. Demasiadas particiones

Estos factores pueden ser la causa de que un índice de almacén de columnas tenga mucho menos de un millón de filas por grupo de filas, que es el número óptimo.  También pueden provocar que las filas vayan al grupo de filas delta, en lugar de a un grupo de filas comprimido. 

### <a name="memory-pressure-when-index-was-built"></a>Presión de la memoria cuando se generó el índice
El número de filas por grupo de filas comprimido está directamente relacionado con el ancho de la fila y la cantidad de memoria disponible para procesar el grupo de filas.  Escriben filas en las tablas de almacén de columnas bajo presión de memoria afecta a la calidad de segmento.  Por consiguiente, es un procedimiento recomendado dar a la sesión que escribe en las tablas del índice de almacén de columnas acceso a tanta memoria como sea posible.  Dado que hay un equilibrio entre memoria y concurrencia, la guía sobre la asignación de memoria adecuada depende de los datos de cada fila de la tabla, de las unidades de almacenamiento de datos que ha asignado al sistema y de la cantidad de ranuras de concurrencia que puede dar a la sesión que escribe datos en la tabla.  Como procedimiento recomendado se recomienda comenzar con xlargerc si se usa DW300, o menos, largerc si se usa entre DW400 y DW600, y mediumrc si se usa DW1000, o más.

### <a name="high-volume-of-dml-operations"></a>Gran volumen de operaciones de DML
Un alto volumen de operaciones de DML que actualizan y eliminan filas puede introducir ineficacia en el almacén de columnas. Esto sucede especialmente cuando se modifica la mayor parte de las filas de un grupo de filas.

* La eliminación de una fila de un grupo de filas comprimido solo marca lógicamente la fila como eliminada. La fila permanece en el grupo de filas comprimido hasta que se vuelven a generar la partición o la tabla.
* La inserción de una fila la agrega a una tabla de almacén de filas interna denominada grupo de filas delta. La fila insertada no se convierte en almacén de columnas hasta que el grupo de filas delta está lleno y marcado como cerrado. Los grupos de filas se cierran cuando alcanzan la capacidad máxima de 1 048 576 filas. 
* La actualización de una fila en formato de almacén de columnas se procesa como eliminación lógica y luego como inserción. La fila insertada se puede guardar en el almacén delta.

Las operaciones de actualización e inserción por lotes que superen el umbral en masa de 102 400 filas por distribución de particiones alineada se escribirán directamente en formato de almacén de columnas. Pero para ello, suponiendo una distribución uniforme, tendría que estar modificando más de 6.144 millones de filas en una sola operación. Si el número de filas de una determinada distribución de particiones alineada es inferior a 102 400, las filas irán al almacén delta y permanecerán allí hasta que se hayan insertado o modificado un número suficiente de filas para cerrar el grupo de filas o hasta que se haya recompilado el índice.

### <a name="small-or-trickle-load-operations"></a>Operaciones de carga pequeña o lenta
Las cargas pequeñas que fluyen al SQL Data Warehouse también se denominan cargas lentas. Normalmente, representan una transmisión prácticamente constante de los datos que ingiere el sistema. Pero como esta transmisión es casi continua, el volumen de filas no es especialmente grande. Con frecuencia los datos están significativamente por debajo del umbral necesario para una carga directa en formato de almacén de columnas.

En estas situaciones, a menudo es preferible que los datos vayan primero a Almacenamiento de blobs de Azure y dejar que se acumulen antes de cargarlos. A menudo, esta técnica se conoce como *procesamiento por microlotes*.

### <a name="too-many-partitions"></a>Demasiadas particiones
Otra cosa que se debe tener en cuenta es el impacto de la creación de particiones en las tablas de almacén de columnas en clúster.  Antes de crear particiones, SQL Data Warehouse ya divide los datos en 60 bases de datos.  La creación de particiones divide aún más los datos.  Si crea particiones de los datos, debe considerar que **cada** partición deberá tener un mínimo de un millón de filas para beneficiarse de un índice de almacén de columnas en clúster.  Si crea 100 particiones en una tabla, esta deberá tener al menos 6000 millones de filas para beneficiarse de un índice de almacén de columnas en clúster (60 distribuciones * 100 particiones * 1 millón de filas). Si la tabla de 100 particiones no tiene 6000 millones de filas, reduzca el número de particiones o considere la posibilidad de usar una tabla de montón en su lugar.

Una vez que las tablas se hayan cargado las tablas con datos, siga los pasos que se indican a continuación para identificar las tablas y volver a generarlas con índices de almacén de columnas clúster que no llegan a ser óptimas.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Regeneración de índices para mejorar la calidad de los segmentos
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Paso 1: identificar o crear un usuario que utilice la clase de recurso adecuada
Una manera rápida de mejorar rápidamente la calidad de los segmentos es volver a generar el índice.  La instrucción SQL que devuelve la vista anterior devolverá una instrucción ALTER INDEX REBUILD que puede utilizarse para volver a generar los índices.  Al regenerar los índices, asegúrese de asignar suficiente memoria a la sesión que volverá a generar el índice.  Para ello, aumente la clase de recurso de un usuario que tenga permisos para volver a generar el índice en esta tabla al mínimo recomendado.  La clase de recurso del usuario propietario de la base de datos no se puede cambiar, por lo que si no ha creado un usuario en el sistema, será preciso que lo haga antes.  Lo mínimo que se recomienda es xlargerc si se usa DW300, o menos, largerc si se usa entre DW400 y DW600, y mediumrc si se usa DW1000, o más.

A continuación se muestra un ejemplo de cómo asignar más memoria a un usuario mediante el aumento de su clase de recurso.  Para obtener más información sobre las clases de recurso y cómo crear un nuevo usuario, consulte el artículo [Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL][Concurrency].

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Paso 2: volver a generar índices de almacén de columnas en clúster con un usuario con una clase de recurso mayor
Inicie sesión como el usuario del paso 1 (p. ej., LoadUser), que ahora usa una clase de recurso superior, y ejecute las instrucciones ALTER INDEX.  Asegúrese de que este usuario tiene el permiso ALTER en las tablas en las que se vuelve a generar el índice.  En estos ejemplos se muestra cómo volver a generar todo el índice de almacén de columnas y una sola partición. En tablas mayores, es más práctico volver a generar los índices partición a partición.

Como alternativa, en lugar de volver a generar el índice, se puede copiar la tabla en otra tabla nueva con [CTAS][CTAS].  ¿De qué manera es mejor? En el caso de grandes volúmenes de datos, [CTAS][CTAS] suele ser más rápido que [ALTER INDEX][ALTER INDEX]. Sin embargo, en el caso de volúmenes menores de datos, [ALTER INDEX][ALTER INDEX] es más fácil de usar y no requerirá el intercambio de la tabla.  Para más información acerca de cómo volver a generar índices con CTAS, consulte **Regeneración de índices con CTAS y conmutación de particiones** .

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

La regeneración de un índice en SQL Data Warehouse es una operación que se realiza sin conexión.  Para obtener más información sobre cómo volver a generar índices, consulte la sección ALTER INDEX REBUILD de [Columnstore Indexes Defragmentation][Columnstore Indexes Defragmentation] (Desfragmentación de índices de almacén de columnas) y [ALTER INDEX][ALTER INDEX].

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Paso 3: comprobar que ha mejorado la calidad de los segmentos de almacén de columnas en clúster
Vuelva a ejecutar la consulta que identificó la tabla con una calidad deficiente de los segmentos y compruebe que dicha calidad ha mejorado.  Si no lo ha hecho, puede deberse a que las filas de la tabla son demasiado anchas.  Considere el uso de una clase de recurso superior o de DWU al volver a generar los índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Regeneración de índices con CTAS y conmutación de particiones
En este ejemplo se usa [CTAS][CTAS] y la conmutación de particiones para volver a generar una partición de la tabla. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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

-- Step 2: Create a SWITCH out table
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

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Para obtener más información sobre cómo volver a crear particiones mediante `CTAS`, consulte el artículo sobre [particiones][Partition].

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los artículos sobre [información general de tablas][Overview], [tipos de datos de tabla][Data Types], [distribución de una tabla][Distribute], [creación de particiones de una tabla][Partition], [mantenimiento de estadísticas de tabla][Statistics] y [tablas temporales][Temporary].  Para obtener más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para SQL Data Warehouse de Azure][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[clustered indexes and nonclustered indexes]: https://msdn.microsoft.com/library/ms190457.aspx
[create table syntax]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[clustered columnstore indexes]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
