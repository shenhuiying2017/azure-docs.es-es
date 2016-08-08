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
    ms.workload="NA" 
    ms.date="07/17/2016"
    ms.author="sstein"/>

# Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante PowerShell


> [AZURE.SELECTOR]
- [Información general](sql-database-recovery-using-backups.md)
- [Restauración geográfica: Portal de Azure](sql-database-geo-restore-portal.md)

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la funcionalidad de restauración geográfica con PowerShell.

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

- Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
- Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
- Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte cómo [restaurar una base de datos a partir de las copias de seguridad iniciadas por el servicio](sql-database-recovery-using-backups.md).
- Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
- Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md).

<!---HONumber=AcomDC_0727_2016-->