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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL

Para proporcionar un rendimiento predecible a escala, Almacenamiento de datos SQL permite a los usuarios controlar los niveles de simultaneidad, así como las asignaciones de recursos como memoria y asignación de prioridades de CPU. En este artículo se presentan los conceptos de simultaneidad y administración de cargas de trabajo, y se explica cómo se han implementado ambas características y cómo puede controlarlas en su almacenamiento de datos. La administración de cargas de trabajo de Almacenamiento de datos SQL está diseñada para admitir entornos de varios usuarios. No está diseñada para cargas de trabajo de multiinquilino.

## Límites de simultaneidad

Almacenamiento de datos SQL permite hasta 1.024 conexiones simultáneas. Todas las 1.024 conexiones pueden enviar consultas al mismo tiempo. Sin embargo, para optimizar el rendimiento, Almacenamiento de datos SQL puede poner en cola algunas consultas para asegurarse de que cada consulta tiene garantizado un mínimo de memoria. Cuando se alcanzan los límites de simultaneidad durante el tiempo de ejecución de las consultas, estas se empiezan a poner en cola. Al iniciar una cola con las consultas cuando se alcanzan los límites de simultaneidad, Almacenamiento de datos SQL puede aumentar el rendimiento total, asegurándose de que las consultas activas obtienen el acceso a los recursos de la memoria que tanto necesitan.

Los límites de simultaneidad se rigen por dos conceptos **consultas simultáneas** y **espacios de simultaneidad**. Para que una consulta se ejecute, lo ha de hacer dentro tanto del límite de simultaneidad como de la asignación de espacio de simultaneidad.

- **Consultas simultáneas** no es otra cosa que el número de consultas que se ejecutan al mismo tiempo. Almacenamiento de datos SQL admite hasta 32 **consultas simultáneas** en los tamaños de almacenamiento de datos más grandes, DW1000 y posteriores. Sin embargo, dado que el número de consultas simultáneas varía según el número de DWU, hemos proporcionado una tabla para mostrar las limitaciones de DWU.
- **Espacios de simultaneidad** es un concepto más dinámico. Cada consulta que se ejecuta de forma simultánea consume una o varias ranuras de simultaneidad. El número exacto de espacios que consume una consulta depende del tamaño de Almacenamiento de datos SQL y de la [clase de recursos](#resource-classes) de la consulta.

La siguiente tabla describe los límites de consultas simultáneas y espacios de simultaneidad.

### Límites de simultaneidad

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| N.º máximo de consultas simultáneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| N.º máximo de ranuras de simultaneidad | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |

Cuando se alcanza uno de estos umbrales, las nuevas consultas se ponen en cola. Las consultas en cola se ejecutan siguiendo el modelo "el primero en entrar es el primero en salir", al irse completando las consultas, el número de las mismas así como de los espacios cae por debajo de los límites.

> [AZURE.NOTE]  Las consultas SELECT que se ejecutan exclusivamente en vistas de administración dinámica (DMV) o vistas de catálogo **no** están reguladas por las clases de recursos. Esto permite a los usuarios supervisar el sistema incluso cuando todas los espacios de simultaneidad están en uso.

## Clases de recursos

Las clases de recursos son una parte esencial de la administración de cargas de trabajo de Almacenamiento de datos SQL, ya que permiten asignar más memoria y ciclos de CPU a las consultas realizadas por un usuario determinado. Existen cuatro clases de recursos, cada uno en forma de un **rol de base de datos**. Las cuatro clases de recursos son **smallrc, mediumrc, largerc y xlargerc**. Los usuarios en smallrc tienen una menor cantidad de memoria y, por ello en esta clase se permite una mayor simultaneidad. Por el contrario, los usuarios asignados a xlargerc reciben grandes cantidades de memoria y, por tanto, la cantidad de estas consultas que está autorizada ejecutarse simultáneamente es menor.

Hay algunos tipos de consultas que no se benefician de una mayor asignación de memoria y por eso ignoran la asignación de recursos de su clase y se ejecutan en su lugar en una clase de recursos pequeña. Forzar estas consultas para que siempre se ejecuten en la clase de recursos pequeña, permite que se ejecuten cuando los espacios de simultaneidad están bajo presión y evita que consuman más espacios de los necesarios. Estas [excepciones de clases de recursos](#resource-class-exceptions) se tratan más adelante en este artículo.

De forma predeterminada, cada usuario es miembro de la clase de recursos pequeña: smallrc. El procedimiento `sp_addrolemember` se utiliza para aumentar la clase de recursos y `sp_droprolemember` se utiliza para reducir la clase de recursos. Por ejemplo, este comando aumentaría la clase de recursos de loaduser a largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Es una práctica recomendada crear usuarios que estén permanentemente asignados a una clase de recursos, en lugar de cambiar la clase de recursos de un usuario. Por ejemplo, las cargas a tablas de almacén de columnas de clúster crean índices de mayor calidad cuando se les asigna más memoria. Para asegurarse de que las cargas tienen acceso a una memoria superior, cree un usuario específico para cargar datos y asigne este usuario de forma permanente a una clase de recursos más alta.

Más detalles en la clase de recursos:

- El permiso `ALTER ROLE` es necesario para cambiar la clase de recursos de un usuario.
- Mientras que un usuario puede agregarse a una o varias de las clases de recursos más altas, los usuarios tomarán los atributos de la clase de recursos más alta de todas a las que está asignado. Es decir, si un usuario está asignado a mediumrc y largerc, la clase de recursos más alta, largerc, es la clase de recursos que se aplicará.
- No se puede cambiar la clase de recursos del usuario administrativo.
 
Encontrará más detalles y ejemplos de creación de usuarios y su asignación a las clases de recursos en la sección [Administración de usuarios](#Managing-users) al final de este artículo.

## Asignación de memoria

Aumentar clase de recursos de un usuario tiene ventajas y desventajas. Aunque el aumento de clase de recursos para un usuario puede hacer que sus consultas tengan acceso a más memoria y se ejecuten más rápidamente, también reduce el número de consultas simultáneas que puede ejecutar. Esto es consecuencia del principio de mantener el equilibrio entre asignar grandes cantidades de memoria a una sola consulta y permitir que otras consultas simultáneas, que también necesita asignaciones de memoria, se ejecuten al mismo tiempo. Si un usuario tiene más memoria para una consulta, otros usuarios no tendrán memoria disponible para ejecutar una consulta.

La tabla siguiente presenta un esquema de la memoria asignada a cada distribución por DWU y clases de recursos. En Almacenamiento de datos SQL existen 60 distribuciones por base de datos, por lo tanto, una consulta que se ejecuta en un DW2000 en la clase de recursos xlargerc, tendría acceso 6.400 MB en cada una de las 60 bases de datos.

### Asignaciones de memoria por distribución (MB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| smallrc | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| mediumrc | 100 | 200 | 200 | 400 | 400 | 400 | 800 | 800 | 800 | 1600 | 1600 | 3\.200 |
| largerc | 200 | 400 | 400 | 800 | 800 | 800 | 1600 | 1600 | 1600 | 3\.200 | 3\.200 | 6\.400 |
| xlargerc | 400 | 800 | 800 | 1600 | 1600 | 1600 | 3\.200 | 3\.200 | 3\.200 | 6\.400 | 6\.400 | 12\.800 |


Utilizando el mismo ejemplo anterior en todo el sistema, a una consulta que se ejecuta en un DW2000 en la clase de recursos xlargerc se le asignan 375 GB de memoria total (60 distribuciones de 6.400 MB * / 1.024 para convertir a GB).

### Asignaciones de memoria en todo el sistema (GB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
|smallrc | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
|mediumrc | 6 | 12 | 12 | 23 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 188 |
|largerc | 12 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 375 |
|xlargerc | 23 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 188 | 375 | 375 | 750 |

## Consumo de ranuras de simultaneidad

Como ya se ha comentado aquí, cuanto mayor sea la clase de recursos más memoria se concede. Puesto que la memoria es un recurso fijo, cuanta más memoria se asigne por consulta, menos simultaneidad se admite. La siguiente tabla reitera el número de espacios de simultaneidad disponibles por DWU, así como los espacios consumidas por cada clase de recursos.

### Asignación y consumo de espacios de simultaneidad

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :---------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| **Asignación** | | | | | | | | | | | | |
| N.º máximo de consultas simultáneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| N.º máximo de ranuras de simultaneidad | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 80 | 80 |
| **Consumo de datos** | | | | | | | | | | | | |
| smallrc | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 32 |
| largerc | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 64 |
| xlargerc | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 | 64 | 128 |

En esta tabla puede ver que una instancia de Almacenamiento de datos SQL que se ejecute como DW100 permite 4 consultas simultáneas smallrc o 2 consultas simultáneas largerc.

## Importancia de las consultas

En segundo plano hay un total de ocho grupos de carga de trabajo que controlan el comportamiento de las clases de recursos. Sin embargo, solo cuatro de los ocho grupos se utilizan en cualquiera de los DWU. Esto tiene sentido ya que cada grupo de carga de trabajo está asignado a smallrc, mediumrc, largerc o xlargerc. La importancia de comprender estos grupos de carga de trabajo en segundo plano se cifra en que algunos de estos grupos se establecen con un nivel de **IMPORTANCIA** más alto. El nivel de importancia se usa para la programación de la CPU. Las consultas que se ejecutan con importancia alta obtendrá 3 veces más ciclos de CPU que aquellas con importancia media. Por lo tanto, las asignaciones de espacio de simultaneidad también determinan la importancia en la CPU. Si una consulta utiliza 16 o más espacios, se ejecuta con importancia alta.

A continuación se muestran las asignaciones de importancia para cada grupo de carga de trabajo.

| Grupos de carga de trabajo | Asignación de la ranura de simultaneidad | Asignación de importancia |
| :------------------  | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | Mediano |
| SloDWGroupC01 | 2 | Mediano |
| SloDWGroupC02 | 4 | Mediano |
| SloDWGroupC03 | 8 | Mediano |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Para una instancia de Almacenamiento de datos SQL de DW500 los grupos de cargas de trabajo activos se asignarán a las clases de recursos de la siguiente manera:

| Clase de recursos | Grupo de cargas de trabajo | Ranuras de simultaneidad usadas | Importancia |
| :--------------- | :------------- | :--------------------:   | :--------- |
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

## Excepciones de clase de recursos

La mayoría de las consultas respetan las clases de recursos, sin embargo, existen algunas excepciones. Esto suele ocurrir cuando los recursos necesarios para llevar a cabo una acción son bajos. Es decir, las excepciones son generalmente casos donde una consulta nunca utilizará la mayor memoria asignada por las clases de recursos más altas. En estos casos siempre se usa la clase de recursos predeterminada o pequeña (smallrc) con independencia de la clase de recursos asignada al usuario. Por ejemplo, `CREATE LOGIN` se ejecutará siempre en la clase smallrc. Los recursos necesarios para llevar a cabo esta operación son muy bajos y por lo tanto no tendría sentido incluir la consulta en el modelo de ranura de simultaneidad. Sería una pérdida de tiempo asignar previamente grandes cantidades de memoria para esta acción. Al excluir `CREATE LOGIN` del modelo de ranura de simultaneidad el Almacenamiento de datos SQL puede ser mucho más eficaz.

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

### Consultas que respetan los límites de simultaneidad

Es importante recordar que es probable que la mayoría de las consultas de usuario final se rijan por clases de recursos. La regla general es que la carga de trabajo de consultas activas debe tener cabida tanto en el umbral de ranuras simultáneas como en el de consultas simultáneas a menos que se haya excluido específicamente por la plataforma. Como usuario final no puede elegir excluir una consulta del modelo de ranura de simultaneidad. Una vez que se ha superado el umbral, las consultas comenzarán a ponerse en cola. Las consultas en cola se tratarán en orden de prioridad, seguido por la hora de envío.

Las siguientes instrucciones **respetan** las clases de recursos:

- INSERT-SELECT
- UPDATE
- DELETE
- SELECT (al consultar las tablas de usuario)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT
- Carga de datos
- Operaciones de movimiento de datos llevadas a cabo por el servicio de movimiento de datos (DMS)


## Administración de usuarios

1. **Cree un inicio de sesión:** abra una conexión con la base de datos **maestra** para Almacenamiento de datos SQL y ejecute los siguientes comandos:
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Es recomendable crear usuarios para los inicios de sesión en la base de datos maestra en Base de datos SQL de Azure y en Almacenamiento de datos SQL de Azure. Hay dos roles de servidor disponibles en este nivel que requieren que el inicio de sesión tenga un usuario en la base de datos maestra para poder concederle la pertenencia. Los roles son `Loginmanager` y `dbmanager`. Tanto en Base de datos SQL como en Almacenamiento de datos SQL de Azure, estos roles conceden derechos para administrar los inicios de sesión y crear bases de datos. Esto es diferente de SQL Server. Para conocer más detalles, consulte el artículo [Autorización y autenticación de Base de datos SQL: concesión de acceso][].

2. **Cree una cuenta de usuario**: abra una conexión con la** base de datos de Almacenamiento de datos SQL** y ejecute el siguiente comando:

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **Otorgue permisos:** el ejemplo siguiente concede `CONTROL` sobre la base de datos de Almacenamiento de datos SQL. `CONTROL` en el nivel de base de datos es el equivalente de db\_owner en SQL Server.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **Aumente la clase de recursos:** para agregar un usuario a un rol de administración de cargas de trabajo con privilegios elevados, use la consulta siguiente:

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **Reduzca la clase de recursos:** para eliminar un usuario de un rol de administración de cargas de trabajo, use la consulta siguiente:

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] No se puede quitar un usuario de smallrc.

## Detección de consulta en cola y otras DMV

La DMV `sys.dm_pdw_exec_requests` puede usarse para identificar las consultas que están a la espera en una cola de simultaneidad.

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

<!---HONumber=AcomDC_0713_2016-->