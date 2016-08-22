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
   ms.date="08/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL

Para proporcionar un rendimiento predecible a escala, Almacenamiento de datos SQL permite a los usuarios controlar los niveles de simultaneidad, así como las asignaciones de recursos como memoria y asignación de prioridades de CPU. En este artículo se presentan los conceptos de simultaneidad y administración de cargas de trabajo, y se explica cómo se han implementado ambas características y cómo puede controlarlas en su almacenamiento de datos. La administración de cargas de trabajo de Almacenamiento de datos SQL está diseñada para admitir entornos de varios usuarios. No está diseñada para cargas de trabajo de multiinquilino.

## Límites de simultaneidad

Almacenamiento de datos SQL permite hasta 1.024 conexiones simultáneas. Todas las 1.024 conexiones pueden enviar consultas al mismo tiempo. Sin embargo, para optimizar el rendimiento, Almacenamiento de datos SQL puede poner en cola algunas consultas para asegurarse de que cada consulta tiene garantizado un mínimo de memoria. Durante el tiempo de ejecución de las consultas, estas se empiezan a poner en cola. Al iniciar una cola con las consultas cuando se alcanzan los límites de simultaneidad, Almacenamiento de datos SQL puede aumentar el rendimiento total, asegurándose de que las consultas activas obtienen el acceso a los recursos de la memoria que tanto necesitan.

Los límites de simultaneidad se rigen por dos conceptos **consultas simultáneas** y **espacios de simultaneidad**. Para que una consulta se ejecute, lo ha de hacer tanto dentro de su límite de simultaneidad como dentro de la asignación de espacio de simultaneidad.

- **Consultas simultáneas** no es otra cosa que el número de consultas que se ejecutan al mismo tiempo. Almacenamiento de datos SQL admite hasta 32 consultas simultáneas.
- Las **ranuras de simultaneidad** se asignan según DWU. Cada 100 DWU proporciona 4 ranuras de simultaneidad. Por ejemplo, un DW100 asigna 4 ranuras de simultaneidad y DW1000 asignó 40. Cada consulta consume una o varias ranuras de simultaneidad, depende de la [clase de recurso](#resource-classes) de la consulta. Las consultas que se ejecutan en la clase de recurso smallrc consumen una ranura de simultaneidad. Las consultas que se ejecutan en una clase de recurso superior consumirán más ranuras de simultaneidad.

La siguiente tabla describe los límites de consultas simultáneas y ranuras de simultaneidad en los distintos tamaños de DWU.

### Límites de simultaneidad

| DWU | N.º máximo de consultas simultáneas | Ranuras de simultaneidad asignadas |
| :----  | :---------------------: | :-------------------------: |
| DW100 | 32 | 4 |
| DW200 | 32 | 8 |
| DW300 | 32 | 12 |
| DW400 | 32 | 16 |
| DW500 | 32 | 20 |
| DW600 | 32 | 24 |
| DW1000 | 32 | 40 |
| DW1200 | 32 | 48 |
| DW1500 | 32 | 60 |
| DW2000 | 32 | 80 |
| DW3000 | 32 | 120 |
| DW6000 | 32 | 240 |

Cuando se alcanza uno de estos umbrales, las nuevas consultas se ponen en cola. Las consultas en cola se ejecutan siguiendo el modelo "el primero en entrar es el primero en salir" al irse completando otras consultas, mientras que el número de las mismas y de los espacios cae por debajo de los límites.

> [AZURE.NOTE]  Las consultas SELECT que se ejecutan exclusivamente en vistas de administración dinámica (DMV) o vistas de catálogo **no** están reguladas por ninguno de los límites de simultaneidad. Esto permite a los usuarios supervisar el sistema independientemente del número de consultas que se ejecutan en él.

## Clases de recursos

Las clases de recursos son la manera en que controlar la asignación de memoria y los ciclos de CPU que se asignan a una consulta. Existen cuatro clases de recurso que pueden asignarse a un usuario en forma de un **rol de base de datos**. Las cuatro clases de recursos son **smallrc, mediumrc, largerc y xlargerc**. Los usuarios en smallrc tienen una menor cantidad de memoria y, por ello en esta clase se permite una mayor simultaneidad. Por el contrario, los usuarios asignados a xlargerc reciben grandes cantidades de memoria y, por tanto, la cantidad de estas consultas que está autorizada ejecutarse simultáneamente es menor.

De forma predeterminada, cada usuario es miembro de la clase de recursos pequeña: smallrc. El procedimiento `sp_addrolemember` se usa para aumentar la clase de recurso, mientras que `sp_droprolemember` se utiliza para reducirla. Por ejemplo, este comando aumentaría la clase de recursos de loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Es una práctica recomendada crear usuarios que estén permanentemente asignados a una clase de recursos, en lugar de cambiar la clase de recursos de un usuario. Por ejemplo, las cargas a tablas de almacén de columnas de clúster crean índices de mayor calidad cuando se les asigna más memoria. Para asegurarse de que las cargas tienen acceso a una memoria superior, cree un usuario específico para cargar datos y asigne este usuario de forma permanente a una clase de recursos más alta.

Hay algunos tipos de consultas que no se benefician de una mayor asignación de memoria. El sistema ignorará la asignación de su clase de recurso y ejecutará siempre estas consultas en una clase de recurso pequeña en su lugar. Forzar estas consultas para que siempre se ejecuten en la clase de recursos pequeña, permite que se ejecuten cuando los espacios de simultaneidad están bajo presión y evita que consuman más espacios de los necesarios. Estas [excepciones de clases de recurso](#query-exceptions-to-concurrency-limits) se tratan más adelante en este artículo.

Más detalles en la clase de recursos:

- El permiso `ALTER ROLE` es necesario para cambiar la clase de recursos de un usuario.
- Mientras que un usuario puede agregarse a una o varias de las clases de recursos más altas, los usuarios tomarán los atributos de la clase de recursos más alta de todas a las que está asignado. Es decir, si un usuario está asignado a mediumrc y largerc, la clase de recursos más alta, largerc, es la clase de recursos que se aplicará.
- No se puede cambiar la clase de recursos del usuario administrativo.
 
Para obtener un ejemplo detallado, vea [Cambio del ejemplo de clase de recurso de usuario](#changing-user-resource-class-example) al final de este artículo

## Asignación de memoria

Aumentar clase de recursos de un usuario tiene ventajas y desventajas. Aunque el aumento de clase de recurso para un usuario puede hacer que sus consultas tengan acceso a más memoria y se ejecuten clases de recurso superiores y más rápidas, también reduce el número de consultas simultáneas que puede ejecutar. Esto es consecuencia del principio de mantener el equilibrio entre asignar grandes cantidades de memoria a una sola consulta y permitir que otras consultas, que también necesitan asignaciones de memoria, se ejecuten al mismo tiempo. Si un usuario recibe asignaciones altas de memoria para una consulta, otros usuarios no tendrán acceso a esa misma memoria a fin de ejecutar una consulta.

La tabla siguiente presenta un esquema de la memoria asignada a cada distribución por DWU y clases de recursos. En Almacenamiento de datos SQL, existen 60 distribuciones. Por ejemplo, una consulta que se ejecuta en DW2000 en la clase de recurso extra grande, tendría acceso a 6400 MB de memoria en cada una de las 60 bases de datos distribuidas.

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

Utilizando el mismo ejemplo anterior, una consulta que se ejecuta en DW2000 en la clase de recursos extra grande se le asigna un total de 375 GB de memoria (6400 MB * 60 distribuciones/1024 para convertir a GB) sobre todo el Almacenamiento de datos SQL.

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

A las consultas que se ejecutan en una mayor la clase de recurso, se les concede más memoria. Puesto que la memoria es un recurso fijo, cuanta más memoria se asigne por consulta, menos simultaneidad se admite. La siguiente tabla reitera todos los conceptos anteriores en una sola vista donde se muestra el número de espacios de simultaneidad disponibles por DWU, así como los espacios consumidos por cada clase de recurso.

### Asignación y consumo de espacios de simultaneidad

| DWU | N.º máximo de consultas simultáneas | Ranuras de simultaneidad asignadas | Ranuras utilizadas por smallrc | Ranuras utilizadas por mediumrc | Ranuras utilizadas por largerc | Ranuras utilizadas por xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100 | 32 | 4 | 1 | 1 | 2 | 4 |
| DW200 | 32 | 8 | 1 | 2 | 4 | 8 |
| DW300 | 32 | 12 | 1 | 2 | 4 | 8 |
| DW400 | 32 | 16 | 1 | 4 | 8 | 16 |
| DW500 | 32 | 20 | 1 | 4 | 8 | 16 |
| DW600 | 32 | 24 | 1 | 4 | 8 | 16 |
| DW1000 | 32 | 40 | 1 | 8 | 16 | 32 |
| DW1200 | 32 | 48 | 1 | 8 | 16 | 32 |
| DW1500 | 32 | 60 | 1 | 8 | 16 | 32 |
| DW2000 | 32 | 80 | 1 | 16 | 32 | 64 |
| DW3000 | 32 | 120 | 1 | 16 | 32 | 64 |
| DW6000 | 32 | 240 | 1 | 32 | 64 | 128 |


En esta tabla, puede ver que Almacenamiento de datos SQL, que se ejecuta como DW1000, ofrece un máximo de 32 consultas simultáneas y un total de 40 ranuras de simultaneidad. Si todos los usuarios se ejecutan en la clase de recurso pequeña, se permitirían 32 consultas simultáneas, ya que cada una consumiría una ranura de simultaneidad. Si todos los usuarios de DW1000 se ejecutaran en la clase de recurso mediana, se asignaría a cada consulta 800 MB por distribuciones para una asignación de memoria total de 47 GB por consulta y la simultaneidad se limitaría a 5 usuarios (40 ranuras de simultaneidad/8 ranuras por usuario mediumrc).

## Importancia de las consultas

En realidad, las clases de recursos se implementan mediante grupos de carga de trabajo. Hay un total de ocho grupos de carga de trabajo que controlan el comportamiento de las clases de recurso en los distintos tamaños de DWU. Solo cuatro de los ocho grupos de carga de trabajo se utilizan en cualquiera de los DWU. Esto tiene sentido ya que cada grupo de carga de trabajo está asignado a una de las cuatro clases de recursos; smallrc, mediumrc, largerc o xlargerc. La importancia de comprender los grupos de carga de trabajo es que algunos de estos grupos se establecen con un nivel de **IMPORTANCIA** más alto. El nivel de importancia se usa para la programación de la CPU. Las consultas que se ejecutan con importancia **alta** obtendrá 3 veces más ciclos de CPU que aquellas con importancia **media**. Por lo tanto, las asignaciones de espacio de simultaneidad también determinan la prioridad en la CPU. Si una consulta utiliza 16 o más espacios, se ejecuta con importancia alta.

A continuación se muestran las asignaciones de importancia para cada grupo de carga de trabajo.

### Asignaciones de grupo de carga de trabajo a las ranuras de simultaneidad e importancia

| Grupos de carga de trabajo | Asignación de la ranura de simultaneidad | Asignación de importancia |
| :-------------- | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | Mediano |
| SloDWGroupC01 | 2 | Mediano |
| SloDWGroupC02 | 4 | Mediano |
| SloDWGroupC03 | 8 | Mediano |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Desde el gráfico **Asignación y consumo de espacios de simultaneidad**, podemos ver que un DW500 usa 1, 4, 8 o 16 ranuras de simultaneidad para smallrc, mediumrc, largerc y xlargerc respectivamente. Podemos buscar un esos valores en el gráfico anterior para obtener clases de recursos importantes.

### Asignación de DW500 de las clases de recursos a importancia

| Clase de recursos | Grupo de cargas de trabajo | Ranuras de simultaneidad usadas | Importancia |
| :------------- | :------------- | :--------------------: | :--------- |
| smallrc | SloDWGroupC00 | 1 | Mediano |
| mediumrc | SloDWGroupC02 | 4 | Mediano |
| largerc | SloDWGroupC03 | 8 | Mediano |
| xlargerc | SloDWGroupC04 | 16 | Alto |


La siguiente consulta DMV puede utilizarse para ver las diferencias en la asignación de recursos de memoria en detalle desde la perspectiva del regulador de recursos, o para analizar el uso activo e histórico de los grupos de carga de trabajo a la hora de solucionar problemas:

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

La mayoría de las consultas se rigen por las clases de recursos. Estas consultas deben encontrarse dentro de los umbrales de ranuras de simultaneidad y consultas simultáneas. Un usuario final no puede elegir excluir una consulta del modelo de ranura de simultaneidad.

Nuevamente, las siguientes instrucciones **respetan** las clases de recursos:

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

Hay algunas consultas que no respetan la clase de recurso al que está asignado el usuario. Estas excepciones a los límites de simultaneidad se realizan cuando los recursos de memoria necesarios para un determinado comando son bajos, a menudo porque el comando es una operación de metadatos. Al tener estas excepciones, se evitarán las asignaciones de memoria mayor a las consultas que nunca las necesitarán. En estos casos, siempre se usa la clase de recursos predeterminada pequeña (smallrc) con independencia de la clase de recursos real asignada al usuario. Por ejemplo, `CREATE LOGIN` se ejecutará siempre en la clase smallrc. Los recursos necesarios para llevar a cabo esta operación son muy bajos y por lo tanto no tendría sentido incluir la consulta en el modelo de ranura de simultaneidad. Sería una pérdida de tiempo asignar previamente grandes cantidades de memoria para esta acción. Al excluir `CREATE LOGIN` del modelo de ranura de simultaneidad el Almacenamiento de datos SQL puede ser mucho más eficaz.

Las siguientes instrucciones **no** respetan las clases de recursos:

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

## Cambio de ejemplo de clase de recurso de usuario

1. **Cree un inicio de sesión:** abra una conexión con la base de datos **maestra** para Almacenamiento de datos SQL y ejecute los siguientes comandos.
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Es recomendable crear usuarios para los inicios de sesión en la base de datos maestra en Base de datos SQL de Azure y en Almacenamiento de datos SQL de Azure. Hay dos roles de servidor disponibles en este nivel que requieren que el inicio de sesión tenga un usuario en la base de datos maestra para poder concederle la pertenencia. Los roles son `Loginmanager` y `dbmanager`. Tanto en Base de datos SQL como en Almacenamiento de datos SQL de Azure, estos roles conceden derechos para administrar los inicios de sesión y crear bases de datos. Esto es diferente de SQL Server. Para conocer más detalles, consulte el artículo [Autorización y autenticación de Base de datos SQL: concesión de acceso][].

2. **Cree una cuenta de usuario**: abra una conexión con la** base de datos de Almacenamiento de datos SQL** y ejecute el siguiente comando.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Otorgue permisos:** el ejemplo siguiente concede `CONTROL` sobre la base de datos de Almacenamiento de datos SQL. `CONTROL` en el nivel de base de datos es el equivalente de db\_owner en SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Aumente la clase de recursos:** para agregar un usuario a un rol de administración de cargas de trabajo con privilegios más altos, use la consulta siguiente.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Reduzca la clase de recursos:** para eliminar un usuario de un rol de administración de cargas de trabajo, use la consulta siguiente.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] No se puede quitar un usuario de smallrc.

## Detección de consulta en cola y otras DMV

La DMV `sys.dm_pdw_exec_requests` puede usarse para identificar las consultas que están a la espera en una cola de simultaneidad. Las consultas que esperan una ranura de simultaneidad tendrán el estado de **suspendido**.

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

Almacenamiento de datos SQL tiene los tipos de espera a continuación.

- LocalQueriesConcurrencyResourceType: se refiere a consultas que residen fuera del marco del espacio de simultaneidad. Las funciones del sistema y las consultas DMV como `SELECT @@VERSION` son ejemplos de consultas locales.
- UserConcurrencyResourceType: se refiere a consultas que residen dentro del marco del espacio de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.
- DmsConcurrencyResourceType: esperas que son el resultado de operaciones de movimiento de datos
- BackupConcurrencyResourceType: espera que indica que se está haciendo una copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se han solicitado al mismo tiempo, las demás se pondrán en cola.

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

La DMV `sys.dm_pdw_resource_waits` muestra solo las esperas de recursos consumidas por una consulta determinada. El tiempo de espera del recurso solo mide el tiempo que se espera a que se proporcionen los recursos, por oposición al tiempo de espera de la señal, que es el tiempo que tarda el servidor SQL Server subyacente en programar la consulta en la CPU.

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

Para conocer más acerca de cómo administrar los usuarios de la base de datos y la seguridad, consulte [Proteger una base de datos en Almacenamiento de datos SQL][]. Para más información acerca de cómo las clases de recursos mayores pueden mejorar la calidad de los índices de almacén de columnas agrupado, consulte [Regeneración de índices para mejorar la calidad de los segmentos].

<!--Image references-->

<!--Article references-->
[Proteger una base de datos en Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-manage-security.md
[Regeneración de índices para mejorar la calidad de los segmentos]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Autorización y autenticación de Base de datos SQL: concesión de acceso]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0810_2016-->