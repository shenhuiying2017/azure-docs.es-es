<properties
   pageTitle="Continuidad empresarial en la nube: restauración de una de base de datos eliminada (Base de datos SQL) | Microsoft Azure"
   description="Obtenga información acerca de la restauración a un momento dado, que le permite revertir una Base de datos SQL de Azure a un momento dado anterior (hasta 35 días)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Recuperación de una Base de datos SQL de Azure mediante copias de seguridad automatizadas

Base de datos SQL proporciona tres opciones para la recuperación de bases de datos mediante [copias de seguridad automáticas de Base de datos SQL](sql-database-automated-backups.md). También puede restaurar una base de datos desde las copias de seguridad iniciadas por el servicio durante su [período de retención](sql-database-service-tiers.md) en:

- Una base de datos nueva en el mismo servidor lógico recuperada en un punto especificado en el tiempo durante el período de retención.
- Una base de datos en el mismo servidor lógico recuperada a la hora de eliminación de una base de datos eliminada.
- Una base de datos nueva en cualquier servidor lógico de cualquier región recuperada en las copias de seguridad diarias más recientes en el almacenamiento de blobs de replicación geográfica (RA-GRS).

También puede usar [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md) para crear una [copia de la base de datos](sql-database-copy.md) en cualquier servidor lógico de cualquier región que sea transaccionalmente coherente con la Base de datos SQL actual. Puede usar la copia de la base de datos y [exportar a un BACPAC](sql-database-export.md) para archivar una copia transaccionalmente coherente de una base de datos durante el almacenamiento a largo plazo más allá de su período de retención o para transferir una copia de la base de datos a una instancia de máquina virtual local o de Azure de SQL Server.

## Tiempo de recuperación

El tiempo de recuperación para restaurar una base de datos mediante copias de seguridad de base de datos automatizadas se ve afectado por una serie de factores:
 - El tamaño de la base de datos
 - El nivel de rendimiento de la base de datos
 - El número de registros de transacciones implicados
 - La cantidad de actividad que debe reproducirse para la recuperación hasta el punto de restauración
 - El ancho de banda de red si la restauración es a una región diferente
 - El número de solicitudes de restauración simultáneas que se están procesando en la región de destino.
 
 Para una base de datos muy grande o activa, la restauración puede tardar varias horas. Si hay una interrupción prolongada en una región, es posible que haya un gran número de solicitudes de restauración geográfica que se procesan en otras regiones. Si hay un gran número de solicitudes, puede aumentar el tiempo de recuperación de las bases de datos de dicha región. La mayoría de las restauraciones de bases de datos dura unas 12 horas.

 No existe ninguna funcionalidad integrada para restaurar de forma masiva. El script denominado [Base de datos SQL de Azure: recuperación completa del servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666), es un ejemplo de uno de los diferentes modos de realizar esta tarea.

## Restauración a un momento dado

La restauración a un momento dado le permite restaurar una base de datos existente como si fuera una nueva a un momento dado anterior en el mismo servidor lógico mediante [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md). No se puede sobrescribir la base de datos existente. Puede restaurar a un momento dado anterior mediante el [Portal de Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauración a un momento dado: Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [Restauración a un momento dado: PowerShell](sql-database-point-in-time-restore-powershell.md)

La base de datos se puede restaurar a cualquier nivel de rendimiento o grupo elástico. Debe asegurarse de que tiene una cuota de DTU suficiente en el servidor lógico o el grupo elástico. Tenga en cuenta que la restauración crea una nueva base de datos y que el nivel de servicio y el de rendimiento de la base de datos restaurada puede ser diferente del estado actual de la base de datos activa. Una vez finalizada la restauración, la base de datos restaurada es una base de datos normal, totalmente accesible en línea, que se cobra a las tasas normales en función de su nivel de rendimiento y servicio. No se incurre en gastos hasta que finaliza la restauración de la base de datos.

Por lo general, una base de datos se restaura a un punto anterior para fines de recuperación. Cuando lo haga, puede tratar la base de datos restaurada como sustituto de la base de datos original o utilizarla para recuperar datos y actualizar después la base de datos original.

- ***Sustituto de la base de datos:*** si la base de datos restaurada está pensada como sustituto de la base de datos original, debe comprobar que el nivel de rendimiento y el de servicio son adecuados y escalar la base de datos si es necesario. Puede cambiar el nombre de la base de datos original y después asignar a la base de datos restaurada el nombre original mediante el comando ALTER DATABASE en T-SQL.
- ***Recuperación de datos:*** si va a recuperar datos de la base de datos restaurada para recuperarse de un error de usuario o de aplicación, debe escribir y ejecutar por separado cualquier script de recuperación de datos necesario para extraer datos de la base de datos restaurada a la base de datos original. Aunque la operación de restauración puede tardar mucho tiempo en finalizar, la base de datos restaurada será visible en la lista de la base de datos completa. Si elimina la base de datos durante la restauración, se cancelará la operación y no se le cobrará por la base de datos que no terminó de restaurarse.

Para obtener información detallada acerca del uso de la restauración a un momento dado para recuperarse de errores de usuario y de aplicación, consulte [Recuperarse de un error de usuario](sql-database-user-error-recovery.md).

## Restauración de la base de datos eliminada

La restauración de una base de datos eliminada le permite restaurar una base de datos eliminada a la hora de su eliminación en el mismo servidor lógico mediante [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

> [AZURE.IMPORTANT] Si elimina una instancia de servidor de Base de datos SQL de Azure, todas sus bases de datos también se eliminan y no se pueden recuperar. En estos momentos no es posible restaurar un servidor eliminado.

Puede utilizar el mismo nombre de base de datos o un nuevo para la base de datos restaurada. Puede usar el [Portal de Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauración de una base de datos eliminada: Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [Restauración de una base de datos eliminada: PowerShell](sql-database-restore-deleted-database-powershell.md)

## Restauración geográfica

Gracias a la funcionalidad de restauración geográfica, podrá restaurar una Base de datos SQL en cualquier servidor de cualquier región de Azure a partir de la última [copia de seguridad diaria automatizada](sql-database-automated-backups.md) con replicación geográfica. La funcionalidad de restauración geográfica usa una copia de seguridad con redundancia geográfica como origen y se puede usar para recuperar una base de datos, aunque no se pueda acceder a dicha base de datos o al centro de datos debido a una interrupción. Puede usar el [Portal de Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)

> [AZURE.SELECTOR]
- [Restauración geográfica: Portal de Azure](sql-database-geo-restore-portal.md)
- [Restauración geográfica: PowerShell](sql-database-geo-restore-powershell.md)

La funcionalidad de restauración geográfica proporciona la opción de recuperación predeterminada cuando la base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Si un incidente a gran escala en los resultados de una región provoca la falta de disponibilidad de una aplicación de base de datos, puede utilizar la funcionalidad de restauración geográfica para restaurar una base de datos a partir de la copia de seguridad más reciente en un servidor de otra región. Todas las copias de seguridad tienen replicación geográfica y pueden tener un retraso entre el momento en que se realiza la copia de seguridad y el momento en que se realiza la replicación geográfica en el blob de Azure de otra región. Este retraso puede ser de hasta una hora, con el fin de que, en caso de desastre, puede haber una pérdida de datos de hasta una hora, es decir, un RPO de hasta una hora. A continuación se muestra la restauración de la base de datos desde la última copia de seguridad diaria.

![restauración geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Para obtener información detallada sobre cómo usar la restauración geográfica a fin de recuperarse de una interrupción, consulte la información para [recuperarse de una interrupción](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Aunque la funcionalidad de restauración geográfica está disponible en todos los niveles de servicio, se trata de la más básica de las soluciones de recuperación ante desastres disponibles en Base de datos SQL con el RPO y el tiempo de recuperación estimado (ERT) más largos. En las bases de datos básicas con un tamaño máximo de 2 GB, la funcionalidad de restauración geográfica proporciona una solución de recuperación ante desastres razonable con un ERT de 12 horas. En bases de datos Standard o Premium más grandes, si se desean tiempos de recuperación más cortos o reducir la probabilidad de pérdida de datos, sería conveniente considerar la posibilidad de usar la replicación geográfica activa. La replicación geográfica activa ofrece RPO y ERT mucho menores, ya que solo es necesario iniciar una conmutación por error en un elemento secundario replicado continuamente. Para más información, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).

## Recuperación mediante programación con copias de seguridad automatizadas

Como se dijo anteriormente, además del Portal de Azure, la recuperación de una base de datos se puede realizar mediante programación con Azure PowerShell y la API de REST. En las tablas siguientes se describe el conjunto de comandos disponibles.

### PowerShell

|Cmdlet|Descripción|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/es-ES/library/azure/mt603648.aspx)|Obtiene una o más bases de datos.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/es-ES/library/azure/mt693387.aspx)|Obtiene una base de datos eliminada que se puede restaurar.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtiene una copia de seguridad con redundancia geográfica de una base de datos.|
|[Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaura una Base de datos SQL.|
||||

### API de REST

|API|Descripción|
|---|-----------|
|[REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaura una base de datos.|
|[Obtener el estado de creación o actualización de la base de datos](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Devuelve el estado durante una operación de restauración.|
||||



## Resumen

Las copias de seguridad automáticas protegen las bases de datos de los errores de usuario y de aplicación, la eliminación accidental de la base de datos y las interrupciones prolongadas. Esta solución sin administración y sin costo está disponible con todas las bases de datos SQL.

## Pasos siguientes

- Para obtener una descripción general de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
- Para más información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
- Para más información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte [Escenarios de continuidad](sql-database-business-continuity-scenarios.md).
- Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo sobre [copia de bases de datos](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->