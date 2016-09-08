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
   ms.date="08/17/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL

Para proporcionar un rendimiento predecible a escala, Almacenamiento de datos SQL de Microsoft Azure le permite controlar los niveles de simultaneidad, así como las asignaciones de recursos, como la asignación de prioridades de CPU y memoria. En este artículo se presentan los conceptos de simultaneidad y administración de cargas de trabajo, y se explica cómo se han implementado ambas características y cómo puede controlarlas en su almacenamiento de datos. La administración de cargas de trabajo de Almacenamiento de datos SQL está diseñada para admitir entornos de varios usuarios. No está diseñada para cargas de trabajo multiinquilino.

## Límites de simultaneidad

Almacenamiento de datos SQL permite hasta 1.024 conexiones simultáneas. Todas las 1.024 conexiones pueden enviar consultas al mismo tiempo. Sin embargo, para optimizar el rendimiento, Almacenamiento de datos SQL puede poner en cola algunas consultas para asegurarse de que cada consulta tiene garantizado un mínimo de memoria. Durante el tiempo de ejecución de las consultas, estas se empiezan a poner en cola. Al iniciar una cola con las consultas cuando se alcanzan los límites de simultaneidad, Almacenamiento de datos SQL puede aumentar el rendimiento total, asegurándose de que las consultas activas obtienen el acceso a los recursos de la memoria que tanto necesitan.

Los límites de simultaneidad se rigen por dos conceptos: *consultas simultáneas* y *espacios de simultaneidad*. Para que una consulta se ejecute, lo ha de hacer tanto dentro de su límite de simultaneidad como dentro de la asignación de espacio de simultaneidad.

- Las consultas simultáneas son consultas que se ejecutan al mismo tiempo. Almacenamiento de datos SQL admite hasta 32 consultas simultáneas en los tamaños de DWU más grandes.
- Los espacios de simultaneidad se asignan según DWU. Cada 100 DWU proporciona 4 espacios de simultaneidad. Por ejemplo, un DW100 asigna 4 espacios de simultaneidad y DW1000 asigna 40. Cada consulta consume uno o más espacios de simultaneidad, en función de la [clase de recursos](#resource-classes) de la consulta. Las consultas que se ejecutan en la clase de recurso smallrc consumen una ranura de simultaneidad. Las consultas que se ejecutan en una clase de recurso superior consumirán más ranuras de simultaneidad.

La tabla siguiente describe los límites de consultas simultáneas y espacios de simultaneidad en los distintos tamaños de DWU.

### Límites de simultaneidad

| DWU | N.º máximo de consultas simultáneas | Espacios de simultaneidad asignados |
| :----  | :---------------------: | :-------------------------: |
| DW100 | 4 | 4 |
| DW200 | 8 | 8 |
| DW300 | 12 | 12 |
| DW400 | 16 | 16 |
| DW500 | 20 | | 20 | |
| DW600 | 24 | 24 |
| DW1000 | 32 | 40 |
| DW1200 | 32 | 48 |
| DW1500 | 32 | 60 |
| DW2000 | 32 | 80 |
| DW3000 | 32 | 120 |
| DW6000 | 32 | 240 |

Cuando se alcanza uno de estos umbrales, las consultas nuevas se ponen en cola. Almacenamiento de datos SQL ejecuta las consultas en cola en función del modelo "el primero en entrar es el primero en salir" a medida que otras consultas van finalizando, mientras que el número de las mismas y de los espacios cae por debajo de los límites.

> [AZURE.NOTE]  Las consultas *Select* que se ejecutan exclusivamente en vistas de administración dinámica (DMV) o vistas de catálogo no están reguladas por ninguno de los límites de simultaneidad. Los usuarios pueden supervisar el sistema independientemente del número de consultas que se ejecutan en él.

## Clases de recursos

Las clases de recursos le permiten controlar la asignación de memoria y los ciclos de CPI que se asignan a una consulta. Puede asignar cuatro clases de recursos a un usuario en forma de *roles de base de datos*. Las cuatro clases de recursos son **smallrc**, **mediumrc**, **largerc** y **xlargerc**. Los usuarios en smallrc tienen una menor cantidad de memoria y pueden aprovechar una mayor simultaneidad. Por el contrario, los usuarios asignados a xlargerc reciben grandes cantidades de memoria y, por tanto, son menos las consultas que se pueden ejecutar de manera simultánea.

De forma predeterminada, cada usuario es miembro de la clase de recursos pequeña: smallrc. El procedimiento `sp_addrolemember` se usa para aumentar la clase de recursos, mientras que `sp_droprolemember` se usa para disminuirla. Por ejemplo, este comando aumentaría la clase de recursos de loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Es una práctica recomendada asignar usuarios permanentemente a una clase de recursos en lugar de cambiar sus clases de recursos. Por ejemplo, las cargas a tablas de almacén de columnas de clúster crean índices de mayor calidad cuando se les asigna más memoria. Para asegurarse de que las cargas tienen acceso a una memoria superior, cree un usuario específico para cargar datos y asigne este usuario de forma permanente a una clase de recursos más alta.

Existen algunos tipos de consultas que no se benefician de una mayor asignación de memoria. El sistema omitirá su asignación de clase de recursos y siempre ejecutará estas consultas en la clase de recursos small. Si estas consultas se ejecutan siempre en la clase de recursos small, se pueden ejecutar cuando los espacios de simultaneidad estén bajo presión y no consumirán más espacios que los necesarios. Consulte [Excepciones de clase de recursos](#query-exceptions-to-concurrency-limits) para más información.

Más detalles en la clase de recursos:

- El permiso *Alter role* es necesario para cambiar la clase de recursos de un usuario.
- A pesar de que puede agregar un usuario a una o varias de las clases de recursos más altas, los usuarios tomarán los atributos de la clase de recursos más alta de todas a las que está asignado. Es decir, si un usuario está asignado a mediumrc y largerc, la clase de recursos más alta, largerc, es la clase de recursos que se aplicará.
- No se puede cambiar la clase de recursos del usuario administrativo.

Para un ejemplo detallado, consulte [Cambio de ejemplo de clase de recursos de usuario](#changing-user-resource-class-example).

## Asignación de memoria

Aumentar clase de recursos de un usuario tiene ventajas y desventajas. A pesar de que aumentar una clase de recursos para un usuario puede significar que sus consultas tengan acceso a más memoria y se ejecuten clases de recursos superiores, también reduce el número de consultas simultáneas que se pueden ejecutar. Esto es consecuencia del principio de mantener el equilibrio entre asignar grandes cantidades de memoria a una sola consulta y permitir que otras consultas, que también necesitan asignaciones de memoria, se ejecuten al mismo tiempo. Si un usuario recibe asignaciones altas de memoria para una consulta, otros usuarios no tendrán acceso a esa misma memoria a fin de ejecutar una consulta.

La tabla siguiente presenta un esquema de la memoria asignada a cada distribución por DWU y clases de recursos. En Almacenamiento de datos SQL, existe 60 distribuciones. Por ejemplo, una consulta que se ejecuta en DW2000 en la clase de recursos xlargerc, tendría acceso a 6400 MB de memoria en cada una de las 60 bases de datos distribuidas.

### Asignaciones de memoria por distribución (MB)

| DWU | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100 | 100 | 100 | 200 | 400 |
| DW200 | 100 | 200 | 400 | 800 |
| DW300 | 100 | 200 | 400 | 800 |
| DW400 | 100 | 400 | 800 | 1600 |
| DW500 | 100 | 400 | 800 | 1600 |
| DW600 | 100 | 400 | 800 | 1600 |
| DW1000 | 100 | 800 | 1600 | 3\.200 |
| DW1200 | 100 | 800 | 1600 | 3\.200 |
| DW1500 | 100 | 800 | 1600 | 3\.200 |
| DW2000 | 100 | 1600 | 3\.200 | 6\.400 |
| DW3000 | 100 | 1600 | 3\.200 | 6\.400 |
| DW6000 | 100 | 3\.200 | 6\.400 | 12\.800 |

En el ejemplo anterior, a una consulta que se ejecuta en DW2000 en la clase de recursos xlargerc se le asigna un total de 375 GB de memoria (6400 MB * 60 distribuciones/1024 para convertir a GB) sobre la totalidad de Almacenamiento de datos SQL.

### Asignaciones de memoria en todo el sistema (GB)

| DWU | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100 | 6 | 6 | 12 | 23 |
| DW200 | 6 | 12 | 23 | 47 |
| DW300 | 6 | 12 | 23 | 47 |
| DW400 | 6 | 23 | 47 | 94 |
| DW500 | 6 | 23 | 47 | 94 |
| DW600 | 6 | 23 | 47 | 94 |
| DW1000 | 6 | 47 | 94 | 188 |
| DW1200 | 6 | 47 | 94 | 188 |
| DW1500 | 6 | 47 | 94 | 188 |
| DW2000 | 6 | 94 | 188 | 375 |
| DW3000 | 6 | 94 | 188 | 375 |
| DW6000 | 6 | 188 | 375 | 750 |


## Consumo de ranuras de simultaneidad

Almacenamiento de datos SQL concederá más memoria a las consultas que se ejecutan en clases de recursos superiores. Debido a que la memoria es un recurso fijo, cuanta más memoria se asigne por consulta, menos simultaneidad se puede admitir. En la tabla siguiente se reiteran todos los conceptos anteriores en una vista única donde se muestra el número de espacios de simultaneidad disponibles por DWU, así como los espacios que consume cada clase de recurso.

### Asignación y consumo de espacios de simultaneidad

| DWU | N.º máximo de consultas simultáneas | Espacios de simultaneidad asignados | Ranuras utilizadas por smallrc | Ranuras utilizadas por mediumrc | Ranuras utilizadas por largerc | Ranuras utilizadas por xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100 | 4 | 4 | 1 | 1 | 2 | 4 |
| DW200 | 8 | 8 | 1 | 2 | 4 | 8 |
| DW300 | 12 | 12 | 1 | 2 | 4 | 8 |
| DW400 | 16 | 16 | 1 | 4 | 8 | 16 |
| DW500 | 20 | | 20 | | 1 | 4 | 8 | 16 |
| DW600 | 24 | 24 | 1 | 4 | 8 | 16 |
| DW1000 | 32 | 40 | 1 | 8 | 16 | 32 |
| DW1200 | 32 | 48 | 1 | 8 | 16 | 32 |
| DW1500 | 32 | 60 | 1 | 8 | 16 | 32 |
| DW2000 | 32 | 80 | 1 | 16 | 32 | 64 |
| DW3000 | 32 | 120 | 1 | 16 | 32 | 64 |
| DW6000 | 32 | 240 | 1 | 32 | 64 | 128 |


En esta tabla, puede ver que Almacenamiento de datos SQL, que se ejecuta como DW1000, ofrece un máximo de 32 consultas simultáneas y un total de 40 ranuras de simultaneidad. Si todos los usuarios se ejecutan en la clase smallrc, se permitirían 32 consultas simultáneas, ya que cada una consumiría 1 espacio de simultaneidad. Si todos los usuarios de DW1000 se ejecutaran en la clase mediumrc, se asignaría a cada consulta 800 MB por distribuciones para una asignación de memoria total de 47 GB por consulta y la simultaneidad se limitaría a 5 usuarios (40 espacios de simultaneidad/8 espacios por usuario mediumrc).

## Importancia de las consultas

Almacenamiento de datos SQL implementa las clases de recursos mediante el uso de grupos de cargas de trabajo. Hay un total de ocho grupos de cargas de trabajo que controlan el comportamiento de las clases de recursos en los distintos tamaños de DWU. Para cualquier DWU, Almacenamiento de datos SQL solo usa cuatro de los ocho grupos de cargas de trabajo. Esto tiene sentido porque cada grupo de cargas de trabajo está asignado a una de las cuatro clases de recursos: smallrc, mediumrc, largerc o xlargerc. La importancia de comprender los grupos de cargas de trabajo es que algunos de estos grupos se establecen con un nivel de *importancia* más alto. El nivel de importancia se usa para la programación de la CPU. Las consultas que se ejecutan con importancia alta obtendrán tres veces más ciclos de CPU que aquellas con importancia media. Por lo tanto, las asignaciones de espacio de simultaneidad también determinan la prioridad en la CPU. Si una consulta utiliza 16 o más espacios, se ejecuta con importancia alta.

La tabla siguiente muestra las asignaciones de importancia para cada grupo de cargas de trabajo.

### Asignaciones de grupos de cargas de trabajo a los espacios de simultaneidad e importancia

| Grupos de carga de trabajo | Asignación de espacio de simultaneidad | Asignación de importancia |
| :-------------- | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | Mediano |
| SloDWGroupC01 | 2 | Mediano |
| SloDWGroupC02 | 4 | Mediano |
| SloDWGroupC03 | 8 | Mediano |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Desde el gráfico **Asignación y consumo de espacios de simultaneidad**, es posible ver que un DW500 usa 1, 4, 8 o 16 espacios de simultaneidad para smallrc, mediumrc, largerc y xlargerc, respectivamente. Puede buscar estos valores en el gráfico anterior para encontrar la importancia de cada clase de recursos.

### Asignación de DW500 de las clases de recursos a importancia

| Clase de recursos | Grupo de cargas de trabajo | Espacios de simultaneidad usados | Importancia |
| :------------- | :------------- | :--------------------: | :--------- |
| smallrc | SloDWGroupC00 | 1 | Mediano |
| mediumrc | SloDWGroupC02 | 4 | Mediano |
| largerc | SloDWGroupC03 | 8 | Mediano |
| xlargerc | SloDWGroupC04 | 16 | Alto |


Puede usar la siguiente consulta DMV para ver las diferencias en la asignación de recursos de memoria en detalle desde la perspectiva del regulador de recursos, o bien para analizar el uso activo e histórico de los grupos de cargas de trabajo en el momento de solucionar problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON	wg.pdw_node_id	= pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

## Consultas que respetan los límites de simultaneidad

La mayoría de las consultas se rigen por las clases de recursos. Estas consultas deben encontrarse dentro de los umbrales de ranuras de simultaneidad y consultas simultáneas. Un usuario no puede elegir excluir una consulta del modelo de espacio de simultaneidad.

Nuevamente, las siguientes instrucciones respetan las clases de recursos:

- INSERT-SELECT
- UPDATE
- DELETE
- SELECT (al consultar las tablas de usuario)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Carga de datos
- Operaciones de movimiento de datos llevadas a cabo por el servicio de movimiento de datos (DMS)

## Excepciones de la consulta a los límites de simultaneidad

Algunas consultas no respetan la clase de recursos a la que está asignado el usuario. Estas excepciones a los límites de simultaneidad se realizan cuando los recursos de memoria necesarios para un determinado comando son bajos, a menudo porque el comando es una operación de metadatos. El objetivo de estas excepciones es evitar asignaciones mayores de memoria a las consultas que nunca las necesitarán. En estos casos, siempre se usa la clase de recursos predeterminada pequeña (smallrc) con independencia de la clase de recursos real asignada al usuario. Por ejemplo, `CREATE LOGIN` se ejecutará siempre en la clase smallrc. Los recursos necesarios para llevar a cabo esta operación son muy bajos, por lo que no tiene sentido incluir la consulta en el modelo de espacio de simultaneidad. Estas consultas tampoco están limitadas por el límite de simultaneidad de 32 usuarios; sino que puede ejecutar un número ilimitado de estas consultas hasta llegar al límite de 1024 sesiones.

Las instrucciones siguientes no respetan las clases de recursos:

- CREATE o DROP TABLE
- ALTER TABLE ... SWITCH, SPLIT o MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE o DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER o DROP USER
- CREATE, ALTER o DROP PROCEDURE
- CREATE o DROP VIEW
- INSERT VALUES
- SELECT (desde vistas del sistema y DMV)
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## Cambio de ejemplo de clase de recursos de usuario

1. **Cree un inicio de sesión:** abra una conexión con la base de datos **maestra** en Almacenamiento de datos SQL y ejecute los comandos siguientes.

	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Es recomendable crear usuarios para los inicios de sesión en la base de datos maestra en Base de datos SQL de Azure y en Almacenamiento de datos SQL de Azure. Hay dos roles de servidor disponibles en este nivel que requieren que el inicio de sesión tenga un usuario en la base de datos **maestra** para poder concederle la pertenencia. Los roles son `Loginmanager` y `dbmanager`. Tanto en Base de datos SQL como en Almacenamiento de datos SQL de Azure, estos roles conceden derechos para administrar los inicios de sesión y crear bases de datos. Esto es diferente de SQL Server. Para más detalles, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure][].

2. **Cree una cuenta de usuario:** abra una conexión con la base de datos de **Almacenamiento de datos SQL** y ejecute el comando siguiente.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Conceda permisos:** el ejemplo siguiente concede `CONTROL` sobre la base de datos de **Almacenamiento de datos SQL**. `CONTROL` en el nivel de base de datos es el equivalente de db\_owner en SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Aumente la clase de recursos:** use la consulta siguiente para agregar un usuario a un rol de administración de cargas de trabajo con privilegios más altos.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Disminuya la clase de recursos:** use la consulta siguiente para eliminar un usuario de un rol de administración de cargas de trabajo.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] No se puede quitar un usuario de smallrc.

## Detección de consulta en cola y otras DMV

Puede usar la DMV `sys.dm_pdw_exec_requests` para identificar las consultas que están a la espera en una cola de simultaneidad. Las consultas que esperan un espacio de simultaneidad tendrán el estado de **suspendido**.

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Los roles de administración de cargas de trabajo se pueden ver con `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

La consulta siguiente muestra qué rol tiene asignado cada usuario.

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

Almacenamiento de datos SQL tiene los siguientes tipos de espera:

- **LocalQueriesConcurrencyResourceType**: se refiere a las consultas que residen fuera del marco del espacio de simultaneidad. Las funciones del sistema y las consultas DMV como `SELECT @@VERSION` son ejemplos de consultas locales.
- **UserConcurrencyResourceType**: se refiere a las consultas que residen dentro del marco del espacio de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.
- **DmsConcurrencyResourceType**: se refiere a las esperas que son el resultado de operaciones de movimiento de datos.
- **BackupConcurrencyResourceType**: se refiere a una espera que indica que se está creando la copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se solicitaron al mismo tiempo, las demás se pondrán en la cola.

La DMV `sys.dm_pdw_waits` puede utilizarse para ver por qué recursos está esperando una solicitud.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
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
WHERE	[session_id] <> SESSION_ID();
```

La DMV `sys.dm_pdw_wait_stats` se puede utilizar para analizar las tendencias históricas de las esperas.

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## Pasos siguientes

Para más información sobre cómo administrar los usuarios y la seguridad de la base de datos, consulte [Proteger una base de datos en Almacenamiento de datos SQL][]. Para más información sobre cómo las clases de recursos mayores pueden mejorar la calidad de los índices de almacén de columnas agrupado, consulte [Regeneración de índices para mejorar la calidad de los segmentos].

<!--Image references-->

<!--Article references-->
[Proteger una base de datos en Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-manage-security.md
[Regeneración de índices para mejorar la calidad de los segmentos]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->