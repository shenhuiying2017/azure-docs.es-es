<properties
	pageTitle="Administración y solución de problemas de Stretch Database | Microsoft Azure"
	description="Obtenga información sobre la administración y solución de problemas de Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# Administración y solución de problemas de Stretch Database

Para administrar y solucionar problemas de Stretch Database, use las herramientas y los métodos que se describen en este tema.

## Administración de dispositivos locales

### <a name="LocalInfo"></a>Obtención de información sobre las bases de datos y tablas locales habilitadas para Stretch Database
Abra las vistas de catálogo **sys.databases** y **sys.tables** para ver información sobre las bases de datos y tablas de SQL Server habilitadas para Stretch. Para obtener más información, consulte [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) y [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Para ver cuánto espacio está utilizando una tabla habilitada para Stretch en SQL Server, ejecute la siguiente instrucción.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## Administración de la migración de datos

### Comprobación de la función de filtro aplicada a una tabla
Abra la vista de catálogo **sys.remote\_data\_archive\_tables** y compruebe el valor de la columna **filter\_predicate** para identificar la función que está utilizando Stretch Database para seleccionar las filas que se van a migrar. Si el valor es null, la tabla completa es elegible para la migración. Para obtener más información, consulte [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) y [Select rows to migrate by using a filter function](sql-server-stretch-database-predicate-function.md) (Uso de una función de filtro para seleccionar las filas que se migrarán).

### <a name="Migration"></a>Comprobación del estado de la migración de datos
Seleccione **Tareas | Stretch | Supervisar** para que una base de datos de SQL Server Management Studio supervise la migración de datos en Supervisión de Stretch Database. Para obtener más información, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

También puede abrir la vista de administración dinámica **sys.dm\_db\_rda\_migration\_status** para comprobar cuántos lotes y filas de datos se han migrado.

### <a name="Firewall"></a>Solución de problemas de migración de datos
Para obtener sugerencias para la solución de problemas, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

## Administración de datos remotos

### <a name="RemoteInfo"></a>Obtención de información sobre las bases de datos y tablas remotas que utiliza Stretch Database
Abra las vistas de catálogo **sys.remote\_data\_archive\_databases** y **sys.remote\_data\_archive\_tables** para ver información sobre las tablas y bases de datos remotas en las que se almacenan los datos migrados. Para obtener más información, consulte [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) y [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Para ver cuánto espacio está utilizando una tabla habilitada para Stretch en Azure, ejecute la siguiente instrucción.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### Eliminación de los datos migrados  
Si quiere eliminar los datos que ya se han migrado a Azure, siga los pasos descritos en [sys.sp\_rda\_reconcile\_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## Administración de esquemas de tabla

### No cambiar el esquema de la tabla remota
No cambie el esquema de una tabla remota de Azure asociada con una tabla de SQL Server configurada para Stretch Database. En particular, no modifique el nombre o el tipo de datos de una columna. La característica de Stretch Database realiza distintos supuestos acerca del esquema de la tabla remota en relación con el esquema de la tabla de SQL Server. Si cambia el esquema remoto, Stretch Database deja de funcionar para la tabla modificada.

### Conciliación de las columnas de tabla  
Si eliminó accidentalmente las columnas de la tabla remota, ejecute **sp\_rda\_reconcile\_columns** para agregar columnas a la tabla remota que hay en la tabla de SQL Server habilitada para Stretch, pero no en la tabla remota. Para obtener más información, consulte [sys.sp\_rda\_reconcile\_columns](https://msdn.microsoft.com/library/mt707765.aspx).

  > [!IMPORTANTE] Cuando **sp\_rda\_reconcile\_columns** vuelve a crear las columnas que eliminó accidentalmente de la tabla remota, no restaura los datos que contenían anteriormente las columnas eliminadas.

**sp\_rda\_reconcile\_columns** no elimina las columnas de la tabla remota que hay en la tabla remota, pero no en la tabla de SQL Server habilitada para Stretch. Si hay columnas en la tabla de Azure remota que ya no están en la tabla de SQL Server habilitada para Stretch, estas columnas adicionales no impiden que Stretch Database funcione con normalidad. Si lo desea, puede quitar manualmente las columnas adicionales.

## Administración del rendimiento y los costes  

### Solución de problemas del rendimiento de las consultas
Se espera que las consultas que incluyen tablas habilitadas para Stretch se realicen más lentamente de lo que lo hacían antes de que las tablas se habilitaran para Stretch. Si se reduce significativamente el rendimiento de las consultas, vea los siguientes problemas posibles.

-   ¿El servidor de Azure está en una región geográfica diferente de la de SQL Server? Configure el servidor de Azure para que esté en la misma región geográfica que su servidor SQL para reducir la latencia de red.

-   Las condiciones de red pueden reducirse. Póngase en contacto con el administrador de red para obtener información sobre los problemas recientes o interrupciones.

### Aumento del nivel de rendimiento de Azure para operaciones que consumen muchos recursos, como la indexación
Al generar, volver a generar o reorganizar un índice en una tabla grande que está configurada para Stretch Database (y si prevé que habrá un gran volumen de consultas de los datos migrados en Azure durante este tiempo), considere la posibilidad de aumentar el nivel de rendimiento de la base de datos remota correspondiente para la duración de la operación. Para obtener más información sobre los niveles de rendimiento y los precios, consulte [Precios SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### No se puede pausar el servicio de SQL Server Stretch Database de Azure  
 Asegúrese de seleccionar el rendimiento y el nivel de precios adecuados. Si aumenta el nivel de rendimiento temporalmente para una operación que consume muchos recursos, puede restaurarlo al nivel anterior cuando finalice la operación. Para obtener más información sobre los niveles de rendimiento y los precios, consulte [SQL Server Stretch Database Pricing](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) (Precios de SQL Server Stretch Database).

## Modificación del ámbito de las consultas  
 Las consultas en tablas habilitadas para Stretch devuelven datos locales y remotos de forma predeterminada. Puede cambiar el ámbito de las consultas en todas las consultas que realicen todos los usuarios o solo en una única consulta que lleve a cabo un administrador.

### Modificación del ámbito de las consultas de todas las consultas que realizan todos los usuarios  
 Para cambiar el ámbito de todas las consultas que realizan todos los usuarios, ejecute el procedimiento almacenado **sys.sp\_rda\_set\_query\_mode**. Puede reducir el ámbito para consultar solo datos locales, deshabilitar todas las consultas o restaurar la configuración predeterminada. Para obtener más información, consulte [sys.sp\_rda\_set\_query\_mode](https://msdn.microsoft.com/library/mt703715.aspx).

### <a name="queryHints"></a>Modificación del ámbito de las consultas de una única consulta que lleva a cabo un administrador  
 Para cambiar el ámbito de una sola consulta que realice un miembro del rol db\_owner, agregue la sugerencia de consulta **WITH (REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *valor* )** a la instrucción SELECT. La sugerencia de consulta REMOTE\_DATA\_ARCHIVE\_OVERRIDE puede tener los valores siguientes.
 -   **LOCAL\_ONLY**. Solo se consultan datos locales.

 -   **REMOTE\_ONLY**. Solo se consultan datos remotos.

 -   **STAGE\_ONLY**. Solo se consultan los datos de la tabla donde pueden migrarse filas de fases de Stretch Database. Además, se conservan las filas migradas durante el periodo especificado después de la migración. Esta sugerencia de consulta constituye la única manera de consultar la tabla de almacenamiento provisional.

Por ejemplo, la siguiente consulta solo devuelve los resultados locales.

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Realización de eliminaciones y actualizaciones administrativas  
 De forma predeterminada, no podrá ACTUALIZAR ni ELIMINAR filas que se hayan migrado o que sean aptas para la migración en una tabla habilitada para Stretch. Cuando tenga que solucionar un problema, un miembro del rol db\_owner puede ejecutar una operación UPDATE o DELETE agregando la sugerencia de consulta **WITH (REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *valor* )** a la instrucción. La sugerencia de consulta REMOTE\_DATA\_ARCHIVE\_OVERRIDE puede tener los valores siguientes.
 -   **LOCAL\_ONLY**. Solo se actualizan o eliminan datos locales.

 -   **REMOTE\_ONLY**. Solo se actualizan o eliminan datos remotos.

 -   **STAGE\_ONLY**. Solo se actualizan o eliminan los datos de la tabla donde pueden migrarse filas de fases de Stretch Database. Además, se conservan las filas migradas durante el periodo especificado después de la migración.

## Consulte también

[Supervisión de la base de datos de Stretch](sql-server-stretch-database-monitor.md)

[Copia de seguridad de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

[Restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-restore.md)

<!---HONumber=AcomDC_0629_2016-->