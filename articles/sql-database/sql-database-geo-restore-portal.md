<properties
	pageTitle="Restauración de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (Portal de Azure) | Microsoft Azure"
	description="Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (Portal de Azure)"
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


# Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure


> [AZURE.SELECTOR]
- [Información general](sql-database-geo-restore.md)
- [Portal de Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la restauración geográfica con el Portal de Azure.

## Selección de la base de datos que se va a restaurar

Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1.	Abra el [Portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **Nuevo** > **Datos y almacenamiento** > **Base de datos SQL**.
3.  Seleccione **Copia de seguridad** como origen y, después, la copia de seguridad con redundancia geográfica que desea recuperar.

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Especifique el nombre de la base de datos, el servidor en el que desee restaurar la base de datos y haga clic en Crear:

## Pasos siguientes

- Para obtener información detallada sobre cómo restaurar una instancia de Base de datos SQL de Azure mediante el Portal de Azure a partir de una copia de seguridad con redundancia geográfica, consulte [Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure](sql-database-geo-restore-portal.md).
- Para obtener información detallada sobre cómo restaurar una instancia de Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica, consulte [Información general: restauración geográfica de Base de datos SQL](sql-database-geo-restore.md).
- Para ver una explicación completa sobre cómo recuperarse ante una interrupción, consulte [Restauración geográfica de una base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante el Portal de Azure](sql-database-disaster-recovery.md).

## Recursos adicionales

- [Escenarios de continuidad empresarial](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->