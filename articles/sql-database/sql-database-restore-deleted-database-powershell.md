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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restauración de una Base de datos SQL de Azure eliminada mediante PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

En este artículo se muestra cómo restaurar una Base de datos SQL de Azure eliminada.

En caso de que se elimine una base de datos, Base de datos SQL de Azure le permite restaurar la base de datos eliminada en el momento en que se eliminó. Base de datos SQL de Azure almacena la copia de seguridad de la base de datos eliminada durante el período de retención de la base de datos.

El período de retención de una base de datos eliminada lo determinan el nivel de servicio de la base de datos mientras esta existe, o bien el número de días en que existe la base de datos, el menor de estos dos valores. Para más información acerca de la retención de la base de datos, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md).


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

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)



## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->