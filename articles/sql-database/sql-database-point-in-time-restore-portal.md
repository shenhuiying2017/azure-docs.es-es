<properties
	pageTitle="Restauración de una base de datos SQL de Azure a un momento dado anterior (Portal de Azure) | Microsoft Azure"
	description="Restauración de una base de datos SQL de Azure a un momento dado anterior."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [Restauración a un momento: PowerShell](sql-database-point-in-time-restore-powershell.md)

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior mediante PowerShell a partir de las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md) mediante el Portal de Azure.

## Selección de una base de datos para restaurarla a un momento dado anterior

Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1.	Abra el [Portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **EXAMINAR** > **Bases de datos SQL**.
3.  Vaya a la base de datos que quiera restaurar y selecciónela.
4.  En la parte superior de la hoja de la base de datos, seleccione **Restaurar**:

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Especifique el nombre de base de datos y un momento dado y haga clic en Aceptar:

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)



## Pasos siguientes

- Para ver una introducción a la continuidad empresarial, consulte [Información general de continuidad empresarial](sql-database-business-continuity.md).
- Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
- Para obtener información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte [Escenarios de continuidad](sql-database-business-continuity-scenarios.md).
- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md) (Restauración bases de datos a partir de las copias de seguridad iniciadas por el servicio).
- Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0629_2016-->