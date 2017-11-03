---
title: "Administración de datos históricos en tablas temporales con directivas de retención |Microsoft Docs"
description: "Aprenda a usar la directiva de retención temporal para mantener los datos históricos bajo su control."
services: sql-database
documentationcenter: 
author: bonova
manager: drasumic
editor: 
ms.assetid: 76cfa06a-e758-453e-942c-9f1ed6a38c2a
ms.service: sql-database
ms.custom: develop databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 10/12/2016
ms.author: bonova
ms.openlocfilehash: b4e1524008837094b57a3df469439ceaebf9c166
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Administración de datos históricos en tablas temporales con directivas de retención
Las tablas temporales pueden aumentar el tamaño de la base de datos más que las tablas normales, especialmente si conserva datos históricos durante un período de tiempo más largo. De ahí que las directivas de retención de datos históricos sean un aspecto importante del planeamiento y la administración del ciclo de vida de cada tabla temporal. Las tablas temporales de Azure SQL Database incluyen un mecanismo de retención fácil de usar que ayuda a realizar esta tarea.

Se puede configurar la retención temporal de datos históricos en el nivel de tabla individual, lo que permite a los usuarios crea directivas de antigüedad flexibles. Aplicar la retención temporal es simple: solo es necesario establecer un parámetro durante la creación de tablas o el cambio de esquemas.

Después de definir la directiva de retención, Azure SQL Database comenzará a comprobar periódicamente si hay filas históricas que son aptas para la limpieza automática de datos. La identificación de filas coincidentes y su eliminación de la tabla de historial se producen de forma transparente, en la tarea en segundo plano programada y ejecutada por el sistema. El estado de antigüedad de las filas de la tabla de historial se compruebe en función de la columna que representa el final del período de SYSTEM_TIME. Si, por ejemplo, el período de retención está establecido en seis meses, las filas de tabla aptas para limpieza satisfacen la siguiente condición:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

En el ejemplo anterior se supone que la columna **ValidTo** corresponde al final del período de SYSTEM_TIME.

## <a name="how-to-configure-retention-policy"></a>¿Cómo configurar la directiva de retención?
Antes de configurar la directiva de retención para una tabla temporal, primero compruebe si está habilitada la retención temporal de datos históricos *en el nivel de base de datos*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

La marca de la base de datos **is_temporal_history_retention_enabled** está establecida en ON de forma predeterminada, pero los usuarios pueden cambiarla con la instrucción ALTER DATABASE. También se establece automáticamente en OFF después de la operación de [restauración a un momento dado](sql-database-recovery-using-backups.md). Para habilitar la limpieza de la retención temporal de datos históricos, ejecute la siguiente instrucción:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [!IMPORTANT]
> Puede configurar la retención para tablas temporales aunque **is_temporal_history_retention_enabled** esté establecida en OFF, pero en ese caso no se desencadenará la limpieza automática de las filas de datos antiguos.
> 
> 

La directiva de retención se configura durante la creación de una tabla mediante la especificación del valor del parámetro HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Azure SQL Database le permite especificar el período de retención mediante distintas unidades de tiempo: DÍAS, SEMANAS, MESES y AÑOS. Si se omite HISTORY_RETENTION_PERIOD, se supone una retención INFINITA. También puede usar explícitamente la palabra clave INFINITO.

En algunos escenarios, puede que quiera configurar la retención después de la creación de una tabla, o cambiar el valor configurado anteriormente. En ese caso use la instrucción ALTER TABLE:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [!IMPORTANT]
> El establecimiento de SYSTEM_VERSIONING en OFF *no conserva* el valor del período de retención. El establecimiento de SYSTEM_VERSIONING en ON sin especificar HISTORY_RETENTION_PERIOD da lugar explícitamente al período de retención INFINITO.
> 
> 

Para revisar el estado actual de la directiva de retención, use la siguiente consulta que combina la marca de habilitación de retención temporal en el nivel de base de datos con períodos de retención para tablas individuales:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


## <a name="how-sql-database-deletes-aged-rows"></a>¿Cómo SQL Database elimina las filas antiguas?
El proceso de limpieza depende del diseño del índice de la tabla de historial. Es importante tener en cuenta que *solo las tablas de historial con un índice agrupado (árbol B o almacén de columnas) pueden tener configurada una directiva de retención finita*. Se crea una tarea en segundo plano para realizar una limpieza de datos antiguos de todas las tablas temporales con el período de retención finito.
La lógica de limpieza del índice agrupado de almacén de filas (árbol B) elimina la fila antigua en fragmentos más pequeños (hasta 10 K), lo que reduce la presión en el registro de base de datos y el subsistema de E/S. A pesar de que la lógica de limpieza usa el índice de árbol B necesario, el orden de las eliminaciones de filas más antiguas que el período de retención no se puede garantizar completamente. Por lo tanto, *no adquiera ninguna dependencia en el orden de limpieza de sus aplicaciones*.

La tarea de limpieza del almacén de columnas agrupado quita [grupos de filas](https://msdn.microsoft.com/library/gg492088.aspx) enteros a la vez (normalmente contiene 1 millón de filas cada uno), lo que resulta muy eficaz, especialmente cuando los datos históricos se generan a un ritmo elevado.

![Retención de almacén de columnas agrupado](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

La excelente compresión de datos y la eficaz limpieza de retención convierten al índice del almacén de columnas agrupado en una perfecta opción en escenarios en los que la carga de trabajo genera rápidamente gran cantidad de datos históricos. Este patrón es típico de [cargas de trabajo intensivas de procesamiento de transacciones que usan tablas temporales](https://msdn.microsoft.com/library/mt631669.aspx) para el seguimiento de cambios y la auditoría, el análisis de tendencias o la ingesta de datos de IoT.

## <a name="index-considerations"></a>Consideraciones sobre el índice
La tarea de limpieza de tablas con índice agrupado de almacén de filas requiere que el índice se inicie con la columna correspondiente al final del período de SYSTEM_TIME. Si este tipo de índice no existe, no se puede configurar un periodo de retención finito:

*Mens. 13765, Nivel 16, Estado 1 <br></br> Error al establecer un período de retención finito en la tabla temporal con versiones del sistema 'temporalstagetestdb.dbo.WebsiteUserInfo' porque la tabla Historial 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' no contiene el índice agrupado requerido. Puede crear un índice agrupado de almacén de columnas o de árbol en B que empiece por la columna que coincide con el final del período SYSTEM_TIME en la tabla Historial.*

Es importante tener en cuenta que la tabla de historial predeterminada creada mediante Azure SQL Database ya tiene el índice agrupado, lo que es compatible con la directiva de retención. Si intenta quitar ese índice en una tabla con el período de retención finito, se produce un error en la operación:

*Mens. 13766, Nivel 16, Estado 1 <br></br> No se puede anular el índice agrupado 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' porque se está usando para la limpieza automática de datos antiguos. Si necesita anular este índice, considere establecer HISTORY_RETENTION_PERIOD en INFINITE en la correspondiente tabla temporal con versiones del sistema.*

La limpieza en el índice de almacén de columnas agrupado funciona de manera óptima si las filas históricas se insertan en orden ascendente (ordenadas por la columna de fin de período), que suele ser el caso cuando la tabla de historial se rellena exclusivamente mediante el mecanismo SYSTEM_VERSIONIOING. Si las filas de la tabla de historial no están ordenadas de este modo (lo que puede ser el caso si ha migrado datos históricos existentes), debe volver a crear el índice de almacén de columnas agrupado encima del índice de almacén de filas de árbol B que está ordenado correctamente, para lograr un rendimiento óptimo.

Evite volver a generar el índice de almacén de columnas agrupado en la tabla de historial con el período de retención finito, ya que puede cambiar el orden de los grupos de filas impuesto naturalmente por la operación de control de versiones. Si necesita volver a generar el índice de almacén de columnas agrupado en la tabla de historial, hágalo creándolo de nuevo encima del índice de árbol B compatible, así se conserva el orden en los grupos de filas necesarios para la limpieza normal de datos. El mismo enfoque se debe adoptar si crea una tabla temporal con una tabla de historial existente que tiene un índice de columnas agrupado sin orden de datos garantizado:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Cuando la tabla de historial con el índice de almacén de columnas agrupado tiene configurado el período de retención finito, no puede crear índices de árbol B no agrupados adicionales en esa tabla:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Se intenta ejecutar la instrucción anterior se producirá el siguiente error:

*Mens. 13772, Nivel 16, Estado 1 <br></br> No se puede crear un índice no agrupado en la tabla de historial temporal 'WebsiteUserInfoHistory' porque tiene definido un período de retención finito y un índice agrupado de almacén de columnas.*

## <a name="querying-tables-with-retention-policy"></a>Consulta de tablas con directiva de retención
Todas las consultas en la tabla temporal filtran automáticamente las filas históricas que coinciden con la directiva de retención finita, a fin de evitar resultados imprevisibles e incoherentes, ya que las filas antiguas se pueden eliminar mediante la tarea de limpieza, *en cualquier momento en el tiempo y en orden aleatorio*.

La siguiente imagen muestra el plan de consulta para una consulta simple:

````
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
````

El plan de consulta incluye un filtro adicional que se aplica a la columna de fin de período (ValidTo) en el operador Examen de índice clúster de la tabla de historial (resaltada). En este ejemplo se supone que se ha establecido el período de retención de 1 MES en la tabla WebsiteUserInfo.

![Filtro de consulta de retención](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Sin embargo, si consulta la tabla de historial directamente, puede ver filas con una antigüedad mayor que el período de retención especificado, pero sin garantía de resultados de consulta repetibles. La siguiente imagen muestra el plan de ejecución de consultas de la consulta en la tabla de historial sin aplicar filtros adicionales:

![Consulta del historial sin filtro de retención](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

No se fíe de la lógica empresarial al leer la tabla de historial más allá del período de retención, ya que puede obtener resultados inesperados o incoherentes. Se recomienda usar consultas temporales con la cláusula FOR SYSTEM_TIME para analizar los datos de tablas temporales.

## <a name="point-in-time-restore-considerations"></a>Consideraciones sobre la restauración a un momento dado
Cuando crea una nueva base de datos mediante la [restauración de una base de datos existente a un momento específico en el tiempo](sql-database-recovery-using-backups.md), tiene una retención temporal deshabilitada en el nivel de base de datos. (la marca **is_temporal_history_retention_enabled** establecida en OFF). Esta funcionalidad permite examinar todas las filas históricas tras la restauración, sin preocuparse de que las filas antiguas se quiten antes de conseguir consultarlas. Puede usarla para *inspeccionar los datos históricos más allá del período de retención configurado*.

Supongamos que una tabla temporal tiene especificado un período de retención de un MES. Si la base de datos se creó en el nivel de servicio Premium, podrá crear una copia de ella con el estado de hasta 35 días hacia atrás en el pasado. Eso permitiría analizar de forma eficaz las filas históricas que tienen hasta 65 días de antigüedad consultando la tabla de historial directamente.

Si desea activar la limpieza de retención temporal, ejecute la siguiente instrucción de Transact-SQL después de la restauración a un momento dado:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo usar las tablas temporales en las aplicaciones, consulte [Introducción a las tablas temporales de Azure SQL Database](sql-database-temporal-tables.md).

Visite Channel 9 para escuchar un [caso real de éxito de implementación](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) y vea una[demostración en directo](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Para más información sobre las tablas temporales, revise la [documentación de MSDN](https://msdn.microsoft.com/library/dn935015.aspx).

