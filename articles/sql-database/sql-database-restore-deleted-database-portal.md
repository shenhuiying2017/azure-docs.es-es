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
- [Información general](sql-database-restore-deleted-database.md)
- [Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)


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

- Para más información sobre cómo restaurar una base de datos eliminada con PowerShell, consulte [Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell](sql-database-restore-deleted-database-powershell.md).
- Para más información sobre cómo restaurar una base de datos eliminada, consulte [Crear o actualizar la base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Para más información sobre cómo restaurar una base de datos eliminada, consulte [Overview: Restore a deleted Azure SQL database](sql-database-restore-deleted-database.md) (Información general: restauración de una base de datos SQL de Azure eliminada).
- Para más información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Overview: SQL Database automated backups](sql-database-automated-backups.md) (Información general: copias de seguridad automatizadas de Base de datos SQL).

## Recursos adicionales

- [Información general: restauración a un momento dato de Base de datos SQL](sql-database-point-in-time-restore.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->