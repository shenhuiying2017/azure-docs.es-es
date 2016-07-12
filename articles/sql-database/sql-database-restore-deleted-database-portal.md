<properties
	pageTitle="Restauración de una base de datos SQL de Azure eliminada (Portal de Azure) | Microsoft Azure"
	description="Restauración de una base de datos SQL de Azure eliminada (Portal de Azure)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure

> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [Restauración de bases de datos eliminadas: PowerShell](sql-database-restore-deleted-database-powershell.md)

## Selección de la base de datos que se va a restaurar 

Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1.	Abra el [Portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **EXAMINAR** > **Servidores SQL Server**.
3.  Vaya al servidor con la base de datos que desea restaurar y selecciónelo.
4.  Desplácese hacia abajo hasta la sección **Operaciones** de la hoja del servidor y seleccione **Bases de datos eliminadas**: ![Restauración de una base de datos SQL de Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Seleccione la base de datos eliminada que desee restaurar.
6.  Especifique un nombre de base de datos y haga clic en Aceptar:

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## Pasos siguientes

- Para ver una introducción a la continuidad empresarial, consulte [Información general de continuidad empresarial](sql-database-business-continuity.md).
- Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
- Para obtener información sobre los escenarios de recuperación y diseño de la continuidad empresarial, consulte [Escenarios de continuidad](sql-database-business-continuity-scenarios.md).
- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md) (Restauración bases de datos a partir de las copias de seguridad iniciadas por el servicio).
- Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0629_2016-->