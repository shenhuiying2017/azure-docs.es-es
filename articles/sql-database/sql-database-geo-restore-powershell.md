<properties 
    pageTitle="Restauración de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica (PowerShell) | Microsoft Azure" 
    description="Restauración de una Base de datos SQL de Azure en un nuevo servidor a partir de una copia de seguridad con redundancia geográfica" 
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

# Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la restauración geográfica con PowerShell.

La [restauración geográfica](sql-database-geo-restore.md) ofrece la capacidad de restaurar una base de datos a partir de una copia de seguridad con redundancia geográfica para crear una base de datos nueva. La base de datos se puede crear en cualquier servidor y en cualquier región de Azure. Dado que usa una copia de seguridad con redundancia geográfica como su fuente, se puede usar para recuperar una base de datos aunque esta sea inaccesible debido a una interrupción. La restauración geográfica se habilita automáticamente para todos los niveles de servicio sin ningún costo adicional.

[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-database-powershell.md)]

## Restauración geográfica de la base de datos en una base de datos independiente

1. Obtenga la copia de seguridad con redundancia geográfica de la base de datos que desee restaurar usando el cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Inicie la restauración de la copia de seguridad con redundancia geográfica mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## Restauración geográfica de la base de datos en un grupo de bases de datos elásticas

1. Obtenga la copia de seguridad con redundancia geográfica de la base de datos que desee restaurar usando el cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Inicie la restauración de la copia de seguridad con redundancia geográfica mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx). Especifique el nombre del grupo que desea restaurar en la base de datos.
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)


## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->