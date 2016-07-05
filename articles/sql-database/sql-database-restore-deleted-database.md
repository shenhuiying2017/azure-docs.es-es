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

# Información general: restauración de una Base de datos SQL de Azure eliminada

> [AZURE.SELECTOR]
- [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md)
- [Información general: restauración a un momento dato de Base de datos SQL](sql-database-point-in-time-restore.md)
- [Restaurar la base de datos eliminada](sql-database-restore-deleted-database.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Escenarios de continuidad del negocio](sql-database-business-continuity-scenarios.md)


Puede restaurar una base de datos eliminada durante el período de retención de las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md) de su [nivel de servicio](sql-database-service-tiers.md). Puede usar el [Portal de Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## Restauración de una base de datos recientemente eliminada

La base de datos eliminada se puede restaurar con el mismo nombre que el servidor lógico que contenía la base de datos original, u otro diferente. Para las bases de datos eliminadas, el punto de restauración se fija en la hora de la eliminación de la base de datos.

> [AZURE.IMPORTANT] Si elimina una instancia de servidor de Base de datos SQL de Azure, todas sus bases de datos también se eliminan y no se pueden recuperar.

## Tiempo de restauración

El tiempo necesario para restaurar una base de datos depende de muchos factores, como el tamaño de la base de datos, el número de registros de transacciones, el momento seleccionado y la cantidad de actividad que debe reproducirse para reconstruir el estado en el momento seleccionado. Para una base de datos muy grande o activa, la restauración puede tardar varias horas. La restauración de una base de datos siempre crea una nueva base de datos en el mismo servidor que la base de datos original, por lo que se debe proporcionar un nuevo nombre a la base de datos restaurada. La mayoría de las restauraciones de base de datos se completa en 12 horas.

## Resumen

Las copias de seguridad automáticas evitan que las bases de datos se eliminen accidentalmente. Esta solución sin administración y sin costo está disponible con todas las bases de datos SQL.

## Pasos siguientes

- Para más información sobre cómo restaurar una base de datos eliminada con el Portal de Azure, consulte [Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md).
- Para más información sobre cómo restaurar una base de datos eliminada con PowerShell, consulte [Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell](sql-database-restore-deleted-database-powershell.md).
- Para más información sobre cómo restaurar una base de datos eliminada, consulte [Crear o actualizar la base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Para más información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Overview: SQL Database automated backups](sql-database-automated-backups.md) (Información general: copias de seguridad automatizadas de Base de datos SQL).

## Recursos adicionales

- [Información general: restauración a un momento dato de Base de datos SQL](sql-database-point-in-time-restore.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->