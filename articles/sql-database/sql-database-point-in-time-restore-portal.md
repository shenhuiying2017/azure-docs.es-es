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
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-point-in-time-restore.md)
- [Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

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

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Restauración a un momento dado](sql-database-point-in-time-restore.md)
- [Restauración a un momento dado mediante la API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)

## Recursos adicionales

- [Restauración de una base de datos eliminada](sql-database-restore-deleted-database.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->