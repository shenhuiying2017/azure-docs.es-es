---
title: "Información general acerca de las tablas en SQL Data Warehouse | Microsoft Docs"
description: "Introducción a las tablas de Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 06/29/2016
ms.author: shigu;jrj
ms.openlocfilehash: c16fef2f302dbc56f257eaf2f0d2b68b6a3c1852
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>Información general de tablas en SQL Data Warehouse
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

Es sencillo empezar a crear tablas en SQL Data Warehouse.  La sintaxis básica de [CREATE TABLE][CREATE TABLE] sigue la sintaxis común que probablemente ya conozca si ha trabajado con otras bases de datos.  Para crear una tabla, solo es preciso asignarle un nombre, asignar un nombre a las columnas de la misma y definir los tipos de datos de cada columna.  Si ha creado las tablas en otras bases de datos, esto le resultará muy familiar.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

El ejemplo anterior crea una tabla denominada Customers con dos columnas, FirstName y LastName.  Cada columna se define con el tipo de datos VARCHAR(25), que limita los datos a 25 caracteres.  Estos atributos fundamentales de una tabla, así como otros, son prácticamente los mismos que los de otras bases de datos.  Los tipos de datos se definen para cada columna y garantizan la integridad de los datos.  Los índices se pueden agregar para mejorar el rendimiento, ya que reducen la E/S.  La creación de particiones se puede agregar para mejorar el rendimiento cuando necesite modificar los datos.

Así es el [cambio de nombre][RENAME] de una tabla de SQL Data Warehouse:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tablas distribuidas
Un nuevo atributo fundamental introducido por sistemas distribuidos como SQL Data Warehouse es la **columna de distribución**.  La columna de distribución es mucho lo que parece.  Es la columna que determina cómo distribuir, o dividir, los datos en segundo plano.  Si se crea una tabla sin especificar la columna de distribución, la tabla se distribuye automáticamente mediante el método **Round Robin**.  Aunque las tablas Round Robin pueden ser suficientes en algunos escenarios, la definición de columnas de distribución puede reducir considerablemente el movimiento de datos durante las consultas, lo que optimiza el rendimiento.  En situaciones en las que hay una pequeña cantidad de datos en una tabla, la elección de crear la tabla con el tipo de distribución **replica** copia los datos a cada nodo de ejecución y ahorra movimiento de datos en tiempo de ejecución de consulta. Para más información acerca de cómo seleccionar una columna de distribución, consulte [Distribución de tablas en SQL Data Warehouse][Distribute].

## <a name="indexing-and-partitioning-tables"></a>Indexación y creación de particiones de tablas
A medida que el uso de SQL Data Warehouse es más avanzado y con el fin de optimizar el rendimiento, deseará más información acerca del diseño de tablas.  Para más información, consulte los artículos [Tipos de datos de las tablas en SQL Data Warehouse][Data Types], [Distribución de tablas en SQL Data Warehouse][Distribute], [Indexación de tablas en SQL Data Warehouse][Index] y [Creación de particiones de tablas en SQL Data Warehouse][Partition].

## <a name="table-statistics"></a>Estadísticas de tabla
Las estadísticas son muy importantes para obtener el máximo rendimiento de SQL Data Warehouse.  Dado que SQL Data Warehouse aún no crea ni actualiza automáticamente las estadísticas, como se pueda esperar de Azure SQL Database, la lectura del artículo sobre [estadísticas][Statistics] puede ser muy importante para asegurarse de que obtiene el máximo rendimiento de las consultas.

## <a name="temporary-tables"></a>Tablas temporales
Las tablas temporales son tablas que solo existen durante el inicio de sesión y no pueden verlas los restantes usuarios.  Las tablas temporales no solo pueden ser una buena forma de evitar que otros vean los resultados temporales, sino que también reducen la necesidad de limpieza.  Dado que las tablas temporales también utilizan el almacenamiento local, pueden ofrecer un rendimiento más rápido para algunas operaciones.  Consulte el artículo [Tablas temporales en SQL Data Warehouse][Temporary] para más información acerca de las tablas temporales.

## <a name="external-tables"></a>Tablas externas
Las tablas externas, que también se conocen como tablas de Polybase, son tablas que pueden consultarse desde SQL Data Warehouse, pero que apuntan a datos externos de SQL Data Warehouse.  Por ejemplo, puede crear una tabla externa que apunte a archivos de Azure Blob Storage.  Para más información acerca de cómo crear y consultar una tabla externa, consulte [Load data from Azure blob storage into SQL Data Warehouse (PolyBase)][Load data with Polybase] [Carga de datos de Azure Blob Storage en SQL Data Warehouse (PolyBase)].  

## <a name="unsupported-table-features"></a>Características no compatibles de las tablas
Mientras que SQL Data Warehouse contiene muchas de las características de tabla que ofrecen otras bases de datos, hay algunas características que aún no son compatibles.  A continuación se muestra una lista de algunas de las características de tabla que no son compatibles.

| Características no admitidas |
| --- |
| Clave principal, claves externas, única y comprobar [restricciones de tabla][Table Constraints] |
| [Índices únicos][Unique Indexes] |
| [Columnas calculadas][Computed Columns] |
| [Columnas dispersas][Sparse Columns] |
| [Tipos definidos por el usuario][User-Defined Types] |
| [Secuencia][Sequence] |
| [Desencadenadores][Triggers] |
| [Vistas indizadas][Indexed Views] |
| [Sinónimos][Synonyms] |

## <a name="table-size-queries"></a>Consultas de tamaño de tabla
Una forma sencilla de identificar el espacio y las filas que consume una tabla en cada una de las 60 distribuciones es usar [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Sin embargo, el uso de los comandos DBCC puede resultar muy limitador.  Las vistas de administración dinámica (DMV) le permitirán ver mucho más detalle, además de proporcionar un control mucho mayor sobre los resultados de la consulta.  Empiece por crear esta vista, a la que se hará referencia en muchos de los ejemplos de este y otros artículos.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Resumen de espacio de tabla
Esta consulta devuelve las filas y el espacio por tabla.  Es una consulta excelente para ver qué tablas son las más grandes y si se han distribuido por hash, round robin o réplica.  Para las tablas distribuidas por hash, también se muestra la columna de distribución.  En la mayoría de los casos, las tablas más grandes deben distribuirse por hash con un índice de almacén de columnas agrupados.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espacio de tabla por tipo de distribución
```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espacio de tabla por tipo de índice
```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Resumen de espacio de distribución
```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Pasos siguientes
Para más información, consulte los artículos sobre [Tipos de datos de las tablas en SQL Data Warehouse][Data Types], [Distribución de tablas en SQL Data Warehouse][Distribute], [Indexación de tablas en SQL Data Warehouse][Index], [Creación de particiones de tablas en SQL Data Warehouse][Partition], [Administración de estadísticas en tablas en SQL Data Warehouse][Statistics] y [Tablas temporales en SQL Data Warehouse][Temporary].  Para obtener más información sobre los procedimientos recomendados, consulte [Procedimientos recomendados para SQL Data Warehouse de Azure][SQL Data Warehouse Best Practices].

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
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
