<properties 
    pageTitle="Restauración de una base de datos SQL de Azure a un momento dado anterior (PowerShell) | Microsoft Azure" 
    description="Restauración de una base de datos SQL de Azure a un momento dado anterior" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior mediante PowerShell a partir de las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]

## Restauración de la base de datos a un momento dado como una base de datos independiente

1. Obtenga la base de datos que desea restaurar mediante el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaure la base de datos a un momento dado mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Restauración de la base de datos a un momento dato en un grupo de bases de datos elásticas
   
1. Obtenga la base de datos que desea restaurar mediante el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaure la base de datos a un momento dado mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Restauración a un momento dado](sql-database-point-in-time-restore.md)
- [Restauración a un momento dado mediante el Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [Restauración a un momento dado mediante la API de REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)

## Recursos adicionales

- [Restauración de una base de datos eliminada](sql-database-restore-deleted-database.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->