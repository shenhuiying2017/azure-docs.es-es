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
    ms.date="05/10/2016"
    ms.author="sstein"/>

# Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior mediante PowerShell.

[**Restauración a un momento dado**](sql-database-point-in-time-restore.md) es una funcionalidad autoservicio que le permite restaurar una base de datos a partir de las copias de seguridad automáticas que realizamos de todas ellas a cualquier momento de su período de retención. Más información sobre las copias de seguridad automáticas y los períodos de retención de las bases de datos, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md).

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

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->