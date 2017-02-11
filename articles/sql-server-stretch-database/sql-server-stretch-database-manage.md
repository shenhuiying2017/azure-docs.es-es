---
title: "Administración y solución de problemas de Stretch Database | Microsoft Docs"
description: "Obtenga información sobre la administración y solución de problemas de Stretch Database."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 8a43c0fc-64d3-4042-8921-a36542aa8933
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 681ad472e53a17600b589d8354d9fdb5c9c3c574


---
# <a name="manage-and-troubleshoot-stretch-database"></a>Administración y solución de problemas de Stretch Database
Para administrar y solucionar problemas de Stretch Database, use las herramientas y los métodos que se describen en este tema.

## <a name="manage-local-data"></a>Administración de dispositivos locales
### <a name="a-namelocalinfoaget-info-about-local-databases-and-tables-enabled-for-stretch-database"></a><a name="LocalInfo"></a>Obtención de información sobre las bases de datos y tablas locales habilitadas para Stretch Database
Abra las vistas de catálogo **sys.databases** y **sys.tables** para ver información sobre las bases de datos y tablas de SQL Server habilitadas para Stretch. Para más información, consulte [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) y [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Para ver cuánto espacio está utilizando una tabla habilitada para Stretch en SQL Server, ejecute la siguiente instrucción.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Administración de la migración de datos
### <a name="check-the-filter-function-applied-to-a-table"></a>Comprobación de la función de filtro aplicada a una tabla
Abra la vista de catálogo **sys.remote\_data\_archive\_tables** y compruebe el valor de la columna **filter\_predicate** para identificar la función que está utilizando Stretch Database para seleccionar las filas que se van a migrar. Si el valor es null, la tabla completa es elegible para la migración. Para más información, consulte [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) y [Seleccionar las filas que se van a migrar mediante una función de filtro](sql-server-stretch-database-predicate-function.md).

### <a name="a-namemigrationacheck-the-status-of-data-migration"></a><a name="Migration"></a>Comprobación del estado de la migración de datos
Seleccione **Tareas | Stretch | Supervisar** para que una base de datos de SQL Server Management Studio supervise la migración de datos en Supervisión de Stretch Database. Para obtener más información, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

También puede abrir la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status** para comprobar cuántos lotes y filas de datos se han migrado.

### <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Solución de problemas de migración de datos
Para obtener sugerencias para la solución de problemas, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Administración de datos remotos
### <a name="a-nameremoteinfoaget-info-about-remote-databases-and-tables-used-by-stretch-database"></a><a name="RemoteInfo"></a>Obtención de información sobre las bases de datos y tablas remotas que utiliza Stretch Database
Abra las vistas de catálogo **sys.remote\_data\_archive\_databases** y **sys.remote\_data\_archive\_tables** para ver información sobre las tablas y bases de datos remotas en las que se almacenan los datos migrados. Para más información, consulte [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) y [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Para ver cuánto espacio está utilizando una tabla habilitada para Stretch en Azure, ejecute la siguiente instrucción.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Eliminación de los datos migrados
Si quiere eliminar los datos que ya se han migrado a Azure, siga los pasos descritos en [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Administración de esquemas de tabla
### <a name="dont-change-the-schema-of-the-remote-table"></a>No cambiar el esquema de la tabla remota
No cambie el esquema de una tabla remota de Azure asociada con una tabla de SQL Server configurada para Stretch Database. En particular, no modifique el nombre o el tipo de datos de una columna. La característica de Stretch Database realiza distintos supuestos acerca del esquema de la tabla remota en relación con el esquema de la tabla de SQL Server. Si cambia el esquema remoto, Stretch Database deja de funcionar para la tabla modificada.

### <a name="reconcile-table-columns"></a>Conciliación de las columnas de tabla
Si eliminó accidentalmente las columnas de la tabla remota, ejecute **sp_rda_reconcile_columns** para agregar columnas a la tabla remota que hay en la tabla de SQL Server habilitada para Stretch pero no en la tabla remota. Para más información, consulte [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

> [!IMPORTANT]
> Cuando **sp_rda_reconcile_columns** vuelve a crear las columnas que eliminó accidentalmente de la tabla remota, no restaura los datos que contenían anteriormente las columnas eliminadas.
> 
> 

**sp_rda_reconcile_columns** no elimina las columnas de la tabla remota que hay en la tabla remota pero no en la tabla de SQL Server habilitada para Stretch. Si hay columnas en la tabla de Azure remota que ya no están en la tabla de SQL Server habilitada para Stretch, estas columnas adicionales no impiden que Stretch Database funcione con normalidad. Si lo desea, puede quitar manualmente las columnas adicionales.  

## <a name="manage-performance-and-costs"></a>Administración del rendimiento y los costes
### <a name="troubleshoot-query-performance"></a>Solución de problemas del rendimiento de las consultas
Se espera que las consultas que incluyen tablas habilitadas para Stretch se realicen más lentamente de lo que lo hacían antes de que las tablas se habilitaran para Stretch. Si se reduce significativamente el rendimiento de las consultas, vea los siguientes problemas posibles.

* ¿El servidor de Azure está en una región geográfica diferente de la de SQL Server? Configure el servidor de Azure para que esté en la misma región geográfica que su servidor SQL para reducir la latencia de red.
* Las condiciones de red pueden reducirse. Póngase en contacto con el administrador de red para obtener información sobre los problemas recientes o interrupciones.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Aumento del nivel de rendimiento de Azure para operaciones que consumen muchos recursos, como la indexación
Al generar, volver a generar o reorganizar un índice en una tabla grande que está configurada para Stretch Database (y si prevé que habrá un gran volumen de consultas de los datos migrados en Azure durante este tiempo), considere la posibilidad de aumentar el nivel de rendimiento de la base de datos remota correspondiente para la duración de la operación. Para obtener más información sobre los niveles de rendimiento y los precios, consulte [Precios SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>No se puede pausar el servicio de SQL Server Stretch Database de Azure
 Asegúrese de seleccionar el rendimiento y el nivel de precios adecuados. Si aumenta el nivel de rendimiento temporalmente para una operación que consume muchos recursos, puede restaurarlo al nivel anterior cuando finalice la operación. Para obtener más información sobre los niveles de rendimiento y los precios, consulte [Precios SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Modificación del ámbito de las consultas
 Las consultas en tablas habilitadas para Stretch devuelven datos locales y remotos de forma predeterminada. Puede cambiar el ámbito de las consultas en todas las consultas que realicen todos los usuarios o solo en una única consulta que lleve a cabo un administrador.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Modificación del ámbito de las consultas de todas las consultas que realizan todos los usuarios
 Para cambiar el ámbito de todas las consultas que realizan todos los usuarios, ejecute el procedimiento almacenado **sys.sp_rda_set_query_mode**. Puede reducir el ámbito para consultar solo datos locales, deshabilitar todas las consultas o restaurar la configuración predeterminada. Para más información, consulte [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="a-namequeryhintsachange-the-scope-of-queries-for-a-single-query-by-an-administrator"></a><a name="queryHints"></a>Modificación del ámbito de las consultas de una única consulta que lleva a cabo un administrador
 Para cambiar el ámbito de una sola consulta que realice un miembro del rol db_owner, agregue la sugerencia de consulta **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** a la instrucción SELECT. La sugerencia de consulta REMOTE_DATA_ARCHIVE_OVERRIDE puede tener los valores siguientes.  

* **LOCAL_ONLY**. Solo se consultan datos locales.  
* **REMOTE_ONLY**. Solo se consultan datos remotos.  
* **STAGE_ONLY**. Solo se consultan los datos de la tabla donde pueden migrarse filas de fases de Stretch Database. Además, se conservan las filas migradas durante el periodo especificado después de la migración. Esta sugerencia de consulta constituye la única manera de consultar la tabla de almacenamiento provisional.  

Por ejemplo, la siguiente consulta solo devuelve los resultados locales.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="a-nameadminhintsamake-administrative-updates-and-deletes"></a><a name="adminHints"></a>Realización de eliminaciones y actualizaciones administrativas
 De forma predeterminada, no podrá actualizar ni eliminar (UPDATE o DELETE) filas que se hayan migrado o que sean aptas para la migración en una tabla habilitada para Stretch. Cuando tenga que solucionar un problema, un miembro del rol db_owner puede ejecutar una operación UPDATE o DELETE agregando la sugerencia de consulta **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *valor* \)** a la instrucción. La sugerencia de consulta REMOTE_DATA_ARCHIVE_OVERRIDE puede tener los valores siguientes.  

* **LOCAL_ONLY**. Solo se actualizan o eliminan datos locales.  
* **REMOTE_ONLY**. Solo se actualizan o eliminan datos remotos.  
* **STAGE_ONLY**. Solo se actualizan o eliminan los datos de la tabla donde pueden migrarse filas de fases de Stretch Database. Además, se conservan las filas migradas durante el periodo especificado después de la migración.  

## <a name="see-also"></a>Consulte también
[Supervisión de la base de datos de Stretch](sql-server-stretch-database-monitor.md)

[Copia de seguridad de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

[Restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-restore.md)




<!--HONumber=Nov16_HO3-->


