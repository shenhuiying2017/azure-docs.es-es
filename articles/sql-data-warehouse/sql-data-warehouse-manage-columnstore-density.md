<properties
   pageTitle="Administración del sesgo de distribución de la tabla | Microsoft Azure"
   description="Orientación para ayudar a los usuarios a identificar el sesgo de distribución en las tablas distribuidas"
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
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Administración de índices de almacén de columnas
Los índices de almacén de columnas son la espina dorsal de Almacenamiento de datos SQL de Azure. Al mantener los índices en un estado óptimo se maximiza el rendimiento del sistema.

En este artículo se explica cómo interrogar los metadatos del índice de almacén de columnas de las tablas; lo que ayuda a diagnosticar problemas y resolverlos rápidamente.

## Consulta de metadatos de almacén de columnas
Para comprender la densidad del almacén de columnas se requiere una consulta en los metadatos del sistema. A continuación se muestra un ejemplo del tipo de información que se puede descubrir.

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

Una vez creada la vista de los metadatos del almacén de columnas pueden analizarse fácilmente. A continuación se ofrece un ejemplo de consulta.

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

## Mejora de la densidad del almacén de columnas
Una vez ejecutada la consulta, puede empezar a examinar los datos y analizar los resultados.

Hay varias cosas que mirar:

| Columna | Uso de estos datos |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Si la tabla tiene particiones, puede que vea mayores recuentos de grupos de filas abiertos. En teoría, cada partición de la distribución podría tener asociado un grupo de filas abierto. Incluir esto en el análisis. Una tabla pequeña en la que se hayan incluido particiones podría optimizarse eliminando por completo las particiones, ya que esto mejoraría la compresión. |
| [row\_count\_total] | Número total de filas de la tabla, por ejemplo, este valor puede utilizarse para calcular el porcentaje de filas en estado comprimido. |
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

## Administración de índices
Puede volver a comprimir los grupos de filas creando las particiones con [CTAS][] o volviendo a generar el propio índice con [ALTER INDEX][]. [CTAS][] suele ser más rápido que [ALTER INDEX][], sobre todo en el caso de tablas o particiones de gran tamaño. Pero, en el caso de particiones o tablas más pequeñas, [ALTER INDEX][] suele ser mucho más cómodo.

>[AZURE.NOTE] ALTER INDEX...REBUILD es una operación sin conexión. ALTER INDEX...REORGANISE es una operación en línea. Pero REORGANISE no afecta a los grupos de filas abiertos. Para incluir grupos de filas abiertos, se requiere ALTER INDEX...REBUILD.

La recompilación de índices, sobre todo en tablas especialmente grandes, suele beneficiarse de recursos adicionales. Almacenamiento de datos SQL de Azure tiene una característica de administración de cargas de trabajo que se puede usar para asignar más memoria a un usuario. Para entender cómo reservar más memoria para las recompilaciones de índice, consulte la sección de administración de cargas de trabajo del artículo sobre [simultaneidad][].

## Pasos siguientes
Para más detalles sobre cómo volver a crear particiones con `CTAS`, consulte los siguientes artículos:

* [Partición de tabla][]
* [simultaneidad][]

Para recomendaciones más detalladas sobre la administración de índices, consulte el artículo [administración de índices][].

Para otras sugerencias de administración, vaya a la información general de [administración][]

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Partición de tabla]: sql-data-warehouse-develop-table-partitions.md
[simultaneidad]: sql-data-warehouse-develop-concurrency.md
[administración]: sql-data-warehouse-manage-monitor.md
[administración de índices]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/es-ES/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->