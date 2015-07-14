<properties
   pageTitle="Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre la simultaneidad y la administración de cargas de trabajo en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL
Para proporcionar rendimiento predecible a escala, Almacenamiento de datos SQL implementa mecanismos para administrar la simultaneidad de cargas de trabajo y la asignación de recursos computacionales.

En este artículo se presentan los conceptos de simultaneidad y administración de cargas de trabajo; se explica cómo se han implementado ambas características y cómo puede controlarlas en su almacenamiento de datos.

## Simultaneidad
Es importante comprender que la simultaneidad en Almacenamiento de datos SQL se rige por dos conceptos; **consultas simultáneas** y **ranuras de simultaneidad**.

Las consultas simultáneas son iguales al número de consultas que se ejecutan al mismo tiempo. Almacenamiento de datos SQL admite hasta 32 **consultas simultáneas**. Cada ejecución de consulta se cuenta como una sola consulta independientemente de si es una consulta en serie (un único subproceso) o una consulta en paralelo (multiproceso). Este es un límite fijo y se aplica a todos los niveles de servicio.

Las ranuras de simultaneidad son un concepto más dinámico y se relacionan con el objetivo de nivel de servicio de Unidad de almacenamiento de datos (DWU) para el almacenamiento de datos. A medida que aumenta el número de DWU asignadas a Almacenamiento de datos SQL más recursos de proceso se asignan. Sin embargo, también aumenta el número de **ranuras de simultaneidad** disponibles.

Almacenamiento de datos SQL tiene que funcionar dentro de ambos umbrales. Si hay más de 32 consultas simultáneas o se supera el número de ranuras de simultaneidad, la consulta se pondrá en cola hasta que puedan satisfacerse ambos umbrales.

Cada consulta que se ejecuta de forma simultánea consume una o varias ranuras de simultaneidad. El número exacto de ranuras depende de dos factores:

1. El valor de DWU para Almacenamiento de datos SQL
2. La **clase de recurso** a la que pertenezca el usuario 

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| Consumo de ranuras de simultaneidad | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| N.º máximo de consultas simultáneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| N.º máximo de ranuras de simultaneidad | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 

Las clases de recursos son una parte esencial de la administración de cargas de trabajo de Almacenamiento de datos SQL dado que también determinan los recursos computacionales asignados a la consulta. Este tema se abordará en la sección de administración de cargas de trabajo más adelante.

## Administración de cargas de trabajo

Almacenamiento de datos SQL expone cuatro clases de recursos diferentes en forma de **roles de base de datos** como parte de su implementación de la administración de cargas de trabajo.

Los roles son:

- smallrc
- mediumrc
- largerc
- xlargerc

Puede ver los roles que le corresponden con la siguiente consulta.

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

De forma predeterminada, cada usuario es miembro de la clase de recurso pequeña: smallrc. Sin embargo, cualquier usuario se puede agregar a una o varias de las clases de recursos más altas. Almacenamiento de datos SQL tomará la pertenencia al rol más alto para la ejecución de consultas. El hecho de agregar un usuario a una clase de recursos más alta aumenta los recursos para ese usuario pero también consume una mayor cantidad de ranuras de simultaneidad, lo que limitará posiblemente la posibilidad de simultaneidad. Esto se debe a que a medida que se asignan más recursos a una consulta, el sistema debe limitar los recursos que otras consumen. No hay comida gratis.

El recurso más importante que se rige por las clases de recursos superiores es la memoria. La mayoría de tablas de almacenamiento de datos de cualquier tamaño significativo usará índices de almacén de columnas agrupados. Si bien esto normalmente proporciona el mejor rendimiento para cargas de trabajo de almacenamiento de datos, su mantenimiento es una operación que consume mucha memoria. A menudo resulta beneficioso usar las clases de recursos más altas en operaciones de administración de datos, por ejemplo, en las regeneraciones de índices.

Para aumentar la memoria, simplemente agregue el usuario de base de datos a uno de los roles mencionados anteriormente.

Puede agregarse y quitarse usted mismo del rol de base de datos de administración de cargas de trabajo con los procedimientos `sp_addrolemember` y `sp_droprolemember`. Tenga en cuenta que necesitará el permiso `ALTER ROLE` para hacerlo. No podrá usar la sintaxis ALTER ROLE DDL. Debe usar los procedimientos almacenados anteriormente mencionados.

> [AZURE.NOTE]En lugar de entrar y sacar un usuario en un grupo de administración de cargas de trabajo, con frecuencia resulta más sencillo iniciar esas operaciones de mayor carga a través de un inicio de sesión o usuario independiente que se asigna permanentemente a la clase de recurso más alta.

En la tabla siguiente se detalla el aumento de memoria disponible para cada consulta, sujeto a la clase de recurso aplicada al usuario que la ejecuta:

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->
| Memoria disponible (por dist) | Prioridad | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | Mediano | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | Mediano | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | Alto | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | Alto | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |


Además, como se mencionó anteriormente, cuanto mayor es la clase de recurso asignada al usuario mayor es el consumo de ranuras de simultaneidad. En la siguiente tabla se documenta el consumo de ranuras de simultaneidad por las consultas en una clase de recurso especificada.

<!--
| Concurrency slot consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Consumo de ranuras de simultaneidad | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| N.º máximo de consultas simultáneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| N.º máximo de ranuras de simultaneidad | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

Es importante recordar que la carga de trabajo de consulta activa debe estar dentro de los umbrales de consulta simultánea y de ranura de simultaneidad. Una vez que se ha superado el umbral, las consultas comenzarán a ponerse en cola. Las consultas en cola se tratarán en orden de prioridad, seguido por la hora de envío.

## Detección de consulta en cola
Para identificar las consultas que se mantienen en una cola de simultaneidad, siempre puede consultar la `sys.dm_pdw_exec_requests` DMV.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
FROM    sys.dm_pdw_exec_requests r
;
```

Almacenamiento de datos SQL tiene tipos de espera específicos para medir la simultaneidad.

Son las siguientes:
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType se refiere a consultas que residen fuera el marco de la ranura de simultaneidad. Las consultas DMV y las funciones del sistema como SELECT @@VERSION son ejemplos de localqueries.

UserConcurrencyResourceType se refiere a consultas que residen dentro del marco de la ranura de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.

DmsConcurrencyResourceType se refiere a esperas que son el resultado de operaciones de movimiento de datos.

BackupConcurrencyResourceType puede verse cuando se hace una copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se han solicitado al mismo tiempo, las demás se pondrán en cola.


Para realizar análisis de las consultas actualmente en cola a fin de averiguar qué recursos espera una solicitud, consulte la `sys.dm_pdw_waits` DMV.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

Para ver solo las esperas de recursos usadas por una consulta determinada, puede consultar la `sys.dm_pdw_resource_waits` DMV. El tiempo de espera del recurso solo mide el tiempo que se espera a que se proporcionen los recursos, por oposición al tiempo de espera de la señal, que es el tiempo que tarda el servidor SQL Server subyacente en programar la consulta en la CPU.

```
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
WHERE	[session_id] <> SESSION_ID()
;
```

Por último, para analizar las tendencias históricas de esperas, Almacenamiento de datos SQL proporciona la `sys.dm_pdw_wait_stats` DMV.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=July15_HO1-->