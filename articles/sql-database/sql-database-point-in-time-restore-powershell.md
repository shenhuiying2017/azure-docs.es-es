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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell

> [AZURE.SELECTOR]
- [Información general](sql-database-point-in-time-restore.md)
- [Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior mediante PowerShell a partir de las [copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).

[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]

## Restauración de la base de datos a un momento dado como una base de datos independiente

1. Obtenga la base de datos que desea restaurar mediante el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaure la base de datos a un momento dato mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Restauración de la base de datos a un momento dato en un grupo de bases de datos elásticas
   
1. Obtenga la base de datos que desea restaurar mediante el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaure la base de datos a un momento dato mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Pasos siguientes

- Para obtener más información sobre la restauración a un momento dado mediante el Portal de Azure, consulte [Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure](sql-database-point-in-time-restore-portal.md).
- Para obtener más información sobre la restauración a un momento dado mediante la API de REST, consulte [Crear o actualizar la base de datos](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Para ver información general de la función de restauración a un momento dado, consulte [Restauración a un momento dado](sql-database-point-in-time-restore.md).
- Para ver un análisis completo de cómo recuperarse de un error de aplicación o de usuario, consulte [Recuperar una base de datos SQL de Azure de un error de usuario](sql-database-user-error-recovery.md).

## Recursos adicionales

- [Escenarios de continuidad del negocio](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->