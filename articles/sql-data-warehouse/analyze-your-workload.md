---
title: 'Analizar la carga de trabajo: Azure SQL Data Warehouse | Microsoft Docs'
description: "Técnicas para analizar la priorización de consultas para la carga de trabajo en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 98617f6b8366662e52d00420adc4c81abffc598d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="analyze-your-workload"></a>Análisis de la carga de trabajo
Técnicas para analizar la priorización de consultas para la carga de trabajo en Azure SQL Data Warehouse.

## <a name="workload-groups"></a>Grupos de carga de trabajo 
Almacenamiento de datos SQL implementa las clases de recursos mediante el uso de grupos de cargas de trabajo. Hay un total de ocho grupos de cargas de trabajo que controlan el comportamiento de las clases de recursos en los distintos tamaños de DWU. Para cualquier DWU, Almacenamiento de datos SQL solo usa cuatro de los ocho grupos de cargas de trabajo. Esto tiene sentido porque cada grupo de cargas de trabajo está asignado a una de las cuatro clases de recursos: smallrc, mediumrc, largerc o xlargerc. La importancia de comprender los grupos de cargas de trabajo es que algunos de estos grupos se establecen con un nivel de *importancia*más alto. El nivel de importancia se usa para la programación de la CPU. Las consultas que se ejecutan con importancia alta obtendrán tres veces más ciclos de CPU que aquellas con importancia media. Por lo tanto, las asignaciones de espacio de simultaneidad también determinan la prioridad en la CPU. Si una consulta utiliza 16 o más espacios, se ejecuta con importancia alta.

La tabla siguiente muestra las asignaciones de importancia para cada grupo de cargas de trabajo.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Asignaciones de grupos de cargas de trabajo a los espacios de simultaneidad e importancia

| Grupos de carga de trabajo | Asignación de espacio de simultaneidad | MB / distribución (Elasticity) | MB / distribución (Compute) | Asignación de importancia |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | Mediano             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | Mediano             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | Mediano             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | Mediano             |
| SloDWGroupC04   | 16                       |  1600                         | 4000                        | Alto               |
| SloDWGroupC05   | 32                       |  3.200                         | 8000                        | Alto               |
| SloDWGroupC06   | 64                       |  6.400                         | 16 000                      | Alto               |
| SloDWGroupC07   | 128                      | 12.800                         | 32 000                      | Alto               |
| SloDWGroupC08   | 256                      | 25.600                         | 64 000                      | Alto               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
Desde el gráfico **Asignación y consumo de espacios de simultaneidad** , es posible ver que un DW500 usa 1, 4, 8 o 16 espacios de simultaneidad para smallrc, mediumrc, largerc y xlargerc, respectivamente. Puede buscar estos valores en el gráfico anterior para encontrar la importancia de cada clase de recursos.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Asignación de DW500 de las clases de recursos a importancia
| clase de recursos | Grupo de cargas de trabajo | Espacios de simultaneidad usados | MB/Distribución | importancia |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | Mediano     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | Mediano     |
| largerc        | SloDWGroupC03  | 8                      | 800               | Mediano     |
| xlargerc       | SloDWGroupC04  | 16                     | 1600             | Alto       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | Mediano     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | Mediano     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | Mediano     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | Mediano     |
| staticrc50     | SloDWGroupC03  | 16                     | 1600             | Alto       |
| staticrc60     | SloDWGroupC03  | 16                     | 1600             | Alto       |
| staticrc70     | SloDWGroupC03  | 16                     | 1600             | Alto       |
| staticrc80     | SloDWGroupC03  | 16                     | 1600             | Alto       |

## <a name="view-workload-groups"></a>Vista de grupos de carga de trabajo
Puede usar la siguiente consulta DMV para ver las diferencias en la asignación de recursos de memoria en detalle desde la perspectiva del regulador de recursos, o bien para analizar el uso activo e histórico de los grupos de cargas de trabajo en el momento de solucionar problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>Detección de consulta en cola y otras DMV
Puede usar la DMV `sys.dm_pdw_exec_requests` para identificar las consultas que están a la espera en una cola de simultaneidad. Las consultas que esperan un espacio de simultaneidad tendrán el estado de **suspendido**.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Los roles de administración de cargas de trabajo se pueden ver con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

La consulta siguiente muestra qué rol tiene asignado cada usuario.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

Almacenamiento de datos SQL tiene los siguientes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: se refiere a las consultas que residen fuera del marco del espacio de simultaneidad. Las funciones del sistema y las consultas DMV como `SELECT @@VERSION` son ejemplos de consultas locales.
* **UserConcurrencyResourceType**: se refiere a las consultas que residen dentro del marco del espacio de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.
* **DmsConcurrencyResourceType**: se refiere a las esperas que son el resultado de operaciones de movimiento de datos.
* **BackupConcurrencyResourceType**: se refiere a una espera que indica que se está creando la copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se solicitaron al mismo tiempo, las demás se pondrán en la cola.

La DMV `sys.dm_pdw_waits` puede utilizarse para ver por qué recursos está esperando una solicitud.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

La DMV `sys.dm_pdw_resource_waits` muestra solo las esperas de recursos consumidas por una consulta determinada. El tiempo de espera del recurso solo mide el tiempo que se espera a que se proporcionen los recursos, por oposición al tiempo de espera de la señal, que es el tiempo que tardan los servidores SQL Server subyacentes en programar la consulta en la CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
También puede usar la DMW `sys.dm_pdw_resource_waits` para calcular cuántas ranuras de simultaneidad se han concedido.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

La DMV `sys.dm_pdw_wait_stats` se puede utilizar para analizar las tendencias históricas de las esperas.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo administrar los usuarios y la seguridad de la base de datos, consulte [Proteger una base de datos en SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para más información sobre cómo las clases de recursos mayores pueden mejorar la calidad de los índices de almacén de columnas agrupado, consulte [Regeneración de índices para mejorar la calidad de los segmentos].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Regeneración de índices para mejorar la calidad de los segmentos]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
