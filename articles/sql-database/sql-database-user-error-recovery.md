<properties 
   pageTitle="Recuperación de errores de usuario de base de datos SQL | Microsoft Azure" 
   description="Obtenga información acerca de cómo recuperarse de errores de los usuarios, datos dañados accidentalmente o una base de datos eliminada con la característica Restauración a un momento dado (PITR) de Base de datos SQL de Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Recuperación de una base de datos SQL de Azure tras un error

Base de datos SQL de Azure ofrece dos capacidades básicas para recuperase de los errores de usuario o de la modificación no intencionada de los datos.

- [Restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Restaurar la base de datos eliminada](sql-database-recovery-using-backups.md#deleted-database-restore)

Base de datos SQL de Azure siempre restaura una base de datos nueva al realizar la restauración a un momento dado, pero puede restaurar a una base de datos con el mismo nombre cuando este proceso de realice a partir de una base de datos eliminada. Estas capacidades de restauración se ofrecen para todas las bases de datos de los niveles Basic, Standard y Premium.

##Restauración a un momento dado

En caso de errores de los usuarios o modificaciones no intencionadas de los datos, se puede usar la restauración a un momento dado para restaurar la base de datos a cualquier punto dado del período de retención de la base de datos.

Las bases de datos de la versión Basic tienen 7 días de retención, las de la versión Standard disponen de 14 días de retención y las de la versión Premium tienen 35 días de retención. Para más información sobre la retención de copia de seguridad de base de datos, consulte [Overview: SQL Database automated backups](sql-database-automated-backups.md) (Información general: copias de seguridad automatizadas de base de datos).

Para llevar a cabo un punto de restauración a un momento dado, consulte:

- [Point in Time Restore with the Azure Portal](sql-database-point-in-time-restore-portal.md) (Restauración a un momento dado con el Portal de Azure)
- [Point in Time Restore with PowerShell](sql-database-point-in-time-restore-powershell.md) (Restauración a un momento dado con PowerShell)
- [Point in Time Restore with REST API (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restauración a un momento dado con la API de REST (createmode=PointInTimeRestore))


## Restauración de una base de datos eliminada

En caso de que se elimine una base de datos, Base de datos SQL de Azure le permite restaurar la base de datos eliminada en el momento en que se eliminó. Base de datos SQL de Azure almacena la copia de seguridad de la base de datos eliminada durante el período de retención de la base de datos.

El período de retención de una base de datos eliminada lo determinan el nivel de servicio de la base de datos mientras esta existe, o bien el número de días en que existe la base de datos, el menor de estos dos valores. Para obtener más información sobre la retención de la base de datos, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

Para restaurar una base de datos eliminada, consulte:

- [Restore a deleted database with the Azure Portal](sql-database-restore-deleted-database-portal.md) (Restauración de una base de datos eliminada con el Portal de Azure)
- [Restore a deleted database with PowerShell](sql-database-restore-deleted-database-powershell.md) (Restauración de una base de datos eliminada con PowerShell)
- [Restore a deleted database with REST API (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restauración de una base de datos eliminada con la API de REST (createmode=Restore))


## Pasos siguientes

- Para ver una introducción a la continuidad empresarial, consulte [Información general de continuidad empresarial](sql-database-business-continuity.md).
- Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
- Para obtener información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte [Escenarios de continuidad](sql-database-business-continuity-scenarios.md).
- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md) (Restauración bases de datos a partir de las copias de seguridad iniciadas por el servicio).
- Para obtener información sobre cómo usar la funcionalidad de replicación geográfica activa, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).

<!---HONumber=AcomDC_0629_2016-->