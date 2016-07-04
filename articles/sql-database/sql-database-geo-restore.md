<properties
   pageTitle="Continuidad del negocio en la nube: restauración geográfica| Microsoft Azure"
   description="Obtenga información acerca de cómo la Base de datos SQL de Azure permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando."
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
   ms.date="06/17/2016"
   ms.author="sstein"/>

# Información general: restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica

> [AZURE.SELECTOR]
- [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)
- [Restauración a un momento dado](sql-database-point-in-time-restore.md)
- [Restaurar la base de datos eliminada](sql-database-restore-deleted-database.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Escenarios de continuidad del negocio](sql-database-business-continuity-scenarios.md)


Gracias a la funcionalidad de restauración geográfica, podrá restaurar una base de datos SQL en cualquier servidor de cualquier región de Azure a partir de la última [copia de seguridad diaria automatizada](sql-database-automated-backups.md). La funcionalidad de restauración geográfica usa una copia de seguridad con redundancia geográfica como origen y se puede usar para recuperar una base de datos, aunque no se pueda acceder a dicha base de datos o al centro de datos debido a una interrupción. Puede usar el [Portal de Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Información general](sql-database-geo-restore.md)
- [Portal de Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

La funcionalidad de restauración geográfica proporciona la opción de recuperación predeterminada cuando la base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. La base de datos se puede crear en cualquier servidor y en cualquier región de Azure. La funcionalidad de restauración geográfica se basa en las [copias de seguridad automatizadas de bases de datos](sql-database-automated-backups.md) del almacenamiento con redundancia geográfica de Azure y realiza el proceso de restauración a partir de la copia de seguridad con replicación geográfica. Por tanto, es resistente a las interrupciones de almacenamiento que se produzcan en la región primaria.

## Detalles de la restauración geográfica

La funcionalidad de restauración geográfica usa la misma tecnología que la restauración a un momento dado, pero hay una diferencia importante. Restaura la base de datos a partir de una copia de la última copia de seguridad diaria en el almacenamiento de blobs con replicación geográfica (RA-GRS). Para cada base de datos activa, el servicio mantiene una cadena de copias de seguridad que incluye una copia de seguridad completa semanal, varias copias de seguridad diferenciales diarias y registros de transacciones que se guardan cada 5 minutos. Estos blobs tienen replicación geográfica, lo que garantiza que las copias de seguridad diarias están disponibles, incluso después de un error masivo en la región principal. A continuación, se muestra la replicación geográfica de las copias de seguridad diarias y semanales copiadas en los contenedores de almacenamiento.

![restauración geográfica](./media/sql-database-geo-restore/geo-restore-1.png)


Si un incidente a gran escala en los resultados de una región provoca la falta de disponibilidad de una aplicación de base de datos, puede utilizar la funcionalidad de restauración geográfica para restaurar una base de datos a partir de la copia de seguridad más reciente en un servidor de otra región. Todas las copias de seguridad tienen replicación geográfica y pueden tener un retraso entre el momento en que se realiza la copia de seguridad y el momento en que se realiza la replicación geográfica en el blob de Azure de otra región. Este retraso puede ser de hasta una hora, con el fin de que, en caso de desastre, puede haber una pérdida de datos de hasta una hora, es decir, un RPO de hasta una hora. A continuación se muestra la restauración de la base de datos desde la última copia de seguridad diaria.


![restauración geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Use la opción [Obtener base de datos recuperable](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obtener el último punto de restauración de replicación geográfica.

## Tiempo de recuperación de una restauración geográfica

El tiempo de recuperación se ve afectado por diversos factores: el tamaño y nivel de rendimiento de la base de datos y el número de solicitudes simultáneas de restauración que se procesan en la región de destino. Si hay una interrupción prolongada en una región, es posible que haya un gran número de solicitudes de restauración geográfica que se procesan en otras regiones. Si hay un gran número de solicitudes, puede aumentar el tiempo de recuperación de las bases de datos de dicha región. El tiempo necesario para restaurar una base de datos depende de varios factores como, por ejemplo, el tamaño de la base de datos, el número de registro de transacciones, el ancho de banda de red, etc. La mayoría de las restauraciones de bases de datos dura unas 12 horas.

## Resumen

Aunque la funcionalidad de restauración geográfica está disponible en todos los niveles de servicio, se trata de la más básica de las soluciones de recuperación ante desastres disponibles en Base de datos SQL con el RPO y el tiempo de recuperación estimado (ERT) más largos. En las bases de datos básicas con un tamaño máximo de 2 GB, la funcionalidad de restauración geográfica proporciona una solución de recuperación ante desastres razonable con un ERT de 12 horas. En bases de datos Standard o Premium más grandes, si se desean tiempos de recuperación más cortos o reducir la probabilidad de pérdida de datos, sería conveniente considerar la posibilidad de usar la replicación geográfica activa. La replicación geográfica activa ofrece RPO y ERT mucho menores, ya que solo es necesario iniciar una conmutación por error en un elemento secundario replicado continuamente. Para obtener más información, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).

## Pasos siguientes

- Para obtener información detallada sobre cómo restaurar una instancia de Base de datos SQL de Azure mediante el Portal de Azure a partir de una copia de seguridad con redundancia geográfica, consulte [Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure](sql-database-geo-restore-portal.md).
- Para obtener información detallada sobre cómo restaurar una instancia de Base de datos SQL de Azure mediante PowerShell a partir de una copia de seguridad con redundancia geográfica, consulte [Información general: restauración geográfica de Base de datos SQL](sql-database-geo-restore-powershell.md).
- Para ver una explicación completa sobre cómo recuperarse ante una interrupción, consulte [Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure](sql-database-disaster-recovery.md).

## Recursos adicionales

- [Escenarios de continuidad empresarial](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->