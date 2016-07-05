<properties
	pageTitle="Restauración de una Base de datos SQL de Azure eliminada (PowerShell) | Microsoft Azure"
	description="Restauración de una Base de datos SQL de Azure eliminada (PowerShell)"
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


# Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell

> [AZURE.SELECTOR]
- [Información general](sql-database-restore-deleted-database.md)
- [Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]


## Restauración de la base de datos eliminada en una base de datos independiente

1. Obtenga la copia de seguridad de la base de datos eliminada que desee restaurar mediante el cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Inicie la restauración a partir de la copia de seguridad eliminada mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## Restauración de la base de datos eliminada en un grupo de bases de datos elásticas

1. Obtenga la copia de seguridad de la base de datos eliminada que desee restaurar mediante el cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Inicie la restauración a partir de la copia de seguridad eliminada mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## Pasos siguientes

- Para más información sobre cómo restaurar una base de datos eliminada con el Portal de Azure, consulte [Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure](sql-database-restore-deleted-database-portal.md).
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