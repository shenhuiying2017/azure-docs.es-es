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
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Información general: restauración de una Base de datos SQL de Azure eliminada

> [AZURE.SELECTOR]
- [Información general](sql-database-restore-deleted-database.md)
- [Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Puede restaurar una base de datos eliminada durante el periodo de retención de las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md). Puede usar el [Portal de Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o la [API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Si elimina una base de datos, se conserva la copia de seguridad final para el período de retención normal, lo que le permite restaurar la base de datos hasta el momento de su eliminación.

## Restauración de una base de datos recientemente eliminada

Para las bases de datos eliminadas, el punto de restauración se fija en la hora de la eliminación de la base de datos. Al restaurar una base de datos eliminada, solo puede restaurarse en el servidor que contenía la base de datos original. Tenga en cuenta esto al eliminar un servidor, ya no podrá restaurar las bases de datos ubicadas anteriormente en ese servidor una vez eliminado.

> [AZURE.IMPORTANT] Si elimina una instancia de servidor de Base de datos SQL de Azure, todas sus bases de datos también se eliminan y no se pueden recuperar.

## Resumen

Las copias de seguridad automáticas evitan que las bases de datos se eliminen accidentalmente. Esta solución sin administración y sin costo está disponible con todas las bases de datos SQL.

## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Restauración de una base de datos eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [Restauración de una base de datos eliminada con PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Restauración de una base de datos eliminada con la API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)

## Recursos adicionales

- [Restauración a un momento dado](sql-database-point-in-time-restore.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->