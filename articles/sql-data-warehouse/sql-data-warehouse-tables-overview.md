---
title: "Introducción al diseño de tablas: Azure SQL Data Warehouse | Microsoft Docs"
description: "Introducción al diseño de tablas en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 01/18/2018
ms.author: barbkess
ms.openlocfilehash: 5c163880a7508d69bce0019cc5379bca8c704d59
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="introduction-to-designing-tables-in-azure-sql-data-warehouse"></a>Introducción al diseño de tablas en Azure SQL Data Warehouse

Aprenda conceptos clave para diseñar tablas en Azure SQL Data Warehouse. 

## <a name="determining-table-category"></a>Determinación de la categoría de tabla 

Un [esquema de estrella](https://en.wikipedia.org/wiki/Star_schema) organiza los datos en tablas de hechos y dimensiones. Algunas tablas se utilizan para datos de integración o de almacenamiento provisional antes de que se pasen a una tabla de hechos o dimensiones. Al diseñar una tabla, decidirá si los datos de la misma pertenecen a una tabla de hechos, dimensiones o integración. Esta decisión informa de la distribución y estructura de tabla adecuadas. 

- Las **tablas de hechos** contienen datos cuantitativos que se suelen generar en un sistema transaccional y, después, se cargan en el almacenamiento de datos. Por ejemplo, una empresa minorista genera transacciones de ventas todos los días y, después, carga los datos en una tabla de hechos en el almacenamiento de datos para su análisis.

- Las **tablas de dimensiones** contienen datos de atributos que pueden cambiar, pero normalmente no cambian con frecuencia. Por ejemplo, el nombre y la dirección de un cliente se almacenan en una tabla de dimensiones y solo se actualizan cuando el perfil del cliente cambia. Para minimizar el tamaño de una tabla de hechos de gran tamaño, el nombre y la dirección del cliente no necesitan estar en todas las filas de una tabla de hechos. En su lugar, la tabla de hechos y la tabla de dimensiones pueden compartir un identificador de cliente. Una consulta puede combinar las dos tablas para asociar el perfil y las transacciones de un cliente. 

- Las **tablas de integración** proporcionan un lugar para integración o almacenamiento provisional de datos. Puede crear una tabla de integración como una tabla normal, una tabla externa o una tabla temporal. Por ejemplo, puede cargar datos a una tabla de almacenamiento provisional, realizar transformaciones en los datos en el almacenamiento provisional y luego insertar dichos datos en una tabla de producción.

## <a name="schema-and-table-names"></a>Nombres de esquema y tabla
En SQL Data Warehouse, un almacenamiento de datos es un tipo de base de datos. Todas las tablas del almacenamiento de datos están contenidas en la misma base de datos.  No puede combinar tablas a través de varios almacenamiento de datos. Este comportamiento es diferente de SQL Server, que admite combinaciones entre bases de datos. 

En una base de datos de SQL Server, puede usar fact, dim o integrate para los nombres de esquema. Si va a migrar una base de datos de SQL Server a SQL Data Warehouse, lo mejor es migrar todas las tablas de hechos, dimensiones e integración a un esquema en SQL Data Warehouse. Por ejemplo, podría almacenar todas las tablas en el almacenamiento de datos de ejemplo [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) dentro de un esquema denominado wwi. El siguiente código crea un [esquema definido por el usuario](/sql/t-sql/statements/create-schema-transact-sql) denominado wwi.

```sql
CREATE SCHEMA wwi;
```

Para mostrar la organización de las tablas en SQL Data Warehouse, puede utilizar fact, dim e int como prefijos para los nombres de tabla. En la tabla siguiente se muestran algunos de los nombres de esquema y tabla para WideWorldImportersDW. En ella se comparan los nombres en SQL Server con los de SQL Data Warehouse. 

| Tabla WideWorldImportersDW  | Tipo de tabla | SQL Server | SQL Data Warehouse |
|:-----|:-----|:------|
| City | Dimension Data | Dimension.City | wwi.DimCity |
| Orden | Hechos | Fact.Order | wwi.FactOrder |


## <a name="table-persistence"></a>Persistencia de tabla 

Las tablas almacenan datos de forma permanente en Azure Storage, temporalmente en Azure Storage o en un almacén de datos externo al almacenamiento de datos.

### <a name="regular-table"></a>Tabla normal

Una tabla normal almacena datos en Azure Storage como parte del almacenamiento de datos. La tabla y los datos persisten, independientemente de si hay una sesión abierta.  En este ejemplo se crea una tabla normal con dos columnas. 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabla temporal
Una tabla temporal solo existe mientras dura la sesión. Una tabla temporal se puede usar para evitar que otros usuarios vean los resultados temporales y también para reducir la necesidad de limpieza.  Dado que las tablas temporales también utilizan el almacenamiento local, pueden ofrecer un rendimiento más rápido para algunas operaciones.  Para más información, consulte [Tablas temporales](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabla externa
Una tabla externa apunta a datos ubicados en Azure Storage Blob o Azure Data Lake Store. Cuando se utiliza en combinación con la instrucción CREATE TABLE AS SELECT, la selección de una tabla externa importa datos en SQL Data Warehouse. Las tablas externas, por tanto, son útiles para cargar datos. Para un tutorial sobre la carga, consulte [Uso de PolyBase para cargar datos de Azure Blob Storage en Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Tipos de datos
SQL Data Warehouse admite los tipos de datos usados más frecuentemente. Para obtener una lista de los tipos de datos admitidos, consulte los [tipos de datos en la referencia de CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) de la instrucción CREATE TABLE. Minimizar el tamaño de los tipos de datos ayuda a mejorar el rendimiento de las consultas. Para una guía sobre el uso de los tipos de datos, vea [Guía para definir los tipos de datos para las tablas en SQL Data Warehouse](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tablas distribuidas
Una característica fundamental de SQL Data Warehouse es la forma en que puede almacenar y operar en tablas en 60 [distribuciones](massively-parallel-processing-mpp-architecture.md#distributions).  Las tablas se distribuyen mediante el método round robin, hash o replicación.

### <a name="hash-distributed-tables"></a>Tablas distribuidas mediante una función hash
La distribución hash distribuye las filas en función del valor de la columna de distribución. La tabla distribuida de hash está diseñada para lograr un alto rendimiento para combinaciones de consultas en tablas grandes. Hay varios factores que afectan a la elección de la columna de distribución. 

Para más información, vea [Distribución de tablas en SQL Data Warehouse](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tablas replicadas
Una tabla replicada tiene una copia completa de la tabla disponible en cada nodo de proceso. Las consultas se ejecutan rápidamente en tablas replicadas porque las combinaciones en las tablas replicadas no requieren movimiento de datos. Sin embargo, la replicación necesita almacenamiento adicional y este método no resulta práctico para tablas de gran tamaño. 

Para más información, consulte [Instrucciones de diseño para el uso de tablas replicadas en Azure SQL Data Warehouse](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tablas round robin
Una tabla round robin distribuye las filas de la tabla uniformemente entre todas las distribuciones. Las filas se distribuyen aleatoriamente. Cargar datos en una tabla round robin es rápido.  Sin embargo, las consultas pueden requerir más movimiento de datos que los demás métodos de distribución. 

Para más información, vea [Distribución de tablas en SQL Data Warehouse](sql-data-warehouse-tables-distribute.md).


### <a name="common-distribution-methods-for-tables"></a>Métodos comunes de distribución para tablas
La categoría de tabla a menudo determina qué opción elegir para distribuir la tabla. 

| Categoría de tabla | Opción de distribución recomendada |
|:---------------|:--------------------|
| Hechos           | Utilice la distribución por hash con el índice de almacén de columnas agrupado. El rendimiento mejora cuando se combinan dos tablas hash en la misma columna de distribución. |
| Dimension Data      | Use la distribución replicada para tablas más pequeñas. Si las tablas son demasiado grandes para almacenar en cada nodo de proceso, utilice la distribución por hash. |
| Ensayo        | Use round robin para la tabla de almacenamiento provisional. La carga con CTAS es rápida. Una vez que los datos estén en la tabla de almacenamiento provisional, utilice INSERT... SELECT para mover los datos a las tablas de producción. |

## <a name="table-partitions"></a>Particiones de tabla
Una tabla con particiones almacena y realiza operaciones en las filas de la tabla según los intervalos de datos. Por ejemplo, una tabla puede tener particiones por día, mes o año. Puede mejorar el rendimiento de las consultas mediante la eliminación de particiones, ya que limita el examen de una consulta a los datos dentro de una partición. También puede mantener los datos a través de modificación de particiones. Puesto que los datos de SQL Data Warehouse ya están distribuidos, demasiadas particiones pueden ralentizar el rendimiento de las consultas. Para más información, consulte [Creación de particiones de tablas en SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="columnstore-indexes"></a>Índices de almacén de columnas
De forma predeterminada, SQL Data Warehouse almacena una tabla como índice de almacén de columnas agrupado. Esta forma de almacenamiento de datos logra una compresión de datos y rendimiento de las consultas altos en tablas grandes.  El índice de almacén de columnas agrupado suele ser la mejor opción, pero en algunos casos un índice agrupado o un montón es la estructura de almacenamiento adecuada.

Para una lista de características de almacén de columnas, vea [Novedades de los índices de almacén de columnas](/sql/relational-databases/indexes/columnstore-indexes-what-s-new). Para mejorar el rendimiento del índice de almacén de columnas, vea [Maximización de la calidad del grupo de filas del almacén de columnas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Estadísticas
El optimizador de consultas utiliza estadísticas de columna cuando crea el plan de ejecución de una consulta. Para mejorar el rendimiento de las consultas, es importante crear estadísticas en columnas individuales, especialmente en las columnas que se usan en combinaciones de consultas. La creación y actualización de estadísticas no se realiza automáticamente. [Cree las estadísticas](/sql/t-sql/statements/create-statistics-transact-sql) después de crear una tabla. Actualice las estadísticas cuando se agregue o cambie un número significativo de filas. Por ejemplo, actualice las estadísticas después de una carga. Para más información, consulte [Administración de estadísticas en tablas en SQL Data Warehouse](sql-data-warehouse-tables-statistics.md).

## <a name="commands-for-creating-tables"></a>Comandos para la creación de tablas
Puede crear una tabla como una nueva tabla vacía. También puede crear y rellenar una tabla con los resultados de una instrucción SELECT. A continuación se muestran los comandos de T-SQL para crear una tabla.

| Instrucción de T-SQL | DESCRIPCIÓN |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | Crea una tabla vacía mediante la definición de todas las opciones y columnas de la tabla. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | Crea una tabla externa. La definición de la tabla se almacena en SQL Data Warehouse. Los datos de la tabla se almacenan en Azure Blob Storage o Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | Rellena una nueva tabla con los resultados de una instrucción SELECT. Las columnas de tabla y los tipos de datos se basan en los resultados de la instrucción SELECT. Para importar datos, puede seleccionar esta instrucción en una tabla externa. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | Crea una nueva tabla externa mediante la exportación de los resultados de una instrucción SELECT a una ubicación externa.  La ubicación es Azure Blob Storage o Azure Data Lake Store. |

## <a name="aligning-source-data-with-the-data-warehouse"></a>Alineación de los datos de origen con el almacenamiento de datos

Las tablas del almacenamiento de datos se rellenan cargando datos desde otro origen de datos. Para realizar una carga correcta, el número y los tipos de datos de las columnas de los datos de origen se deben alinear con la definición de tabla en el almacenamiento de datos. Obtener los datos que se van a alinear podría ser la parte más complicada de diseñar las tablas. 

Si los datos proceden de varios almacenamientos de datos, puede colocar dichos datos en el almacenamiento de datos y almacenarlos en una tabla de integración. Una vez que los datos están en la tabla de integración, puede utilizar la eficacia de SQL Data Warehouse para realizar operaciones de transformación. Una vez que los datos están preparados, puede insertarlos en tablas de producción.

## <a name="unsupported-table-features"></a>Características no compatibles de las tablas
SQL Data Warehouse admite muchas, pero no todas, de las características de tabla que ofrecen otras bases de datos.  En la lista siguiente se muestran algunas de las características de tabla que no se admiten en SQL Data Warehouse.

- Primary key, Foreign key, Unique, [restricciones de tabla](/sql/t-sql/statements/alter-table-table-constraint-transact-sql) Check

- [Columnas calculadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [Vistas indizadas](/sql/relational-databases/views/create-indexed-views)
- [Secuencia](/sql/t-sql/statements/create-sequence-transact-sql)
- [Columnas dispersas](/sql/relational-databases/tables/use-sparse-columns)
- [Claves suplentes](). Implementar con [Identity](sql-data-warehouse-tables-identity.md).
- [Sinónimos](/sql/t-sql/statements/create-synonym-transact-sql)
- [Desencadenadores](/sql/t-sql/statements/create-trigger-transact-sql)
- [Índices únicos](/sql/t-sql/statements/create-index-transact-sql)
- [Tipos definidos por el usuario](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>Consultas de tamaño de tabla
Una forma sencilla de identificar el espacio y las filas que consume una tabla en cada una de las sesenta distribuciones es usar [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Sin embargo, el uso de los comandos DBCC puede resultar muy limitador.  Las vistas de administración dinámica (DMV) muestran más detalles que los comandos DBCC. Empiece por crear esta vista.

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

Esta consulta devuelve las filas y el espacio por tabla.  Permite ver qué tablas son las más grandes y si se han distribuido por hash, round robin o réplica.  Para las tablas distribuidas por hash, la consulta muestra la columna de distribución.  

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

## <a name="next-steps"></a>pasos siguientes
Después de crear las tablas para el almacenamiento de datos, el paso siguiente es cargar datos en la tabla.  Para un tutorial sobre la carga, consulte [Uso de PolyBase para cargar de datos de Azure Blob Storage en Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).