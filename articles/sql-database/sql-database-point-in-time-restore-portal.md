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

- Para obtener más información sobre la restauración a un momento dado mediante PowerShell, consulte [Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell](sql-database-point-in-time-restore-powershell.md).
- Para obtener más información sobre la restauración a un momento dado mediante la API de REST, consulte [Crear o actualizar la base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Para ver información general de la función de restauración a un momento dado, consulte [Restauración a un momento dado](sql-database-point-in-time-restore.md).
- Para ver un análisis completo de cómo recuperarse de un error de aplicación o de usuario, consulte [Recuperar una base de datos SQL de Azure de un error de usuario](sql-database-user-error-recovery.md).

## Recursos adicionales

- [Escenarios de continuidad del negocio](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->