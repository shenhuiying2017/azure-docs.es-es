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
	ms.workload="data-management"
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

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Restauración de una base de datos eliminada](sql-database-restore-deleted-database.md)
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