<properties 
   pageTitle="Recuperar una base de datos SQL de Azure mediante la restauración geográfica en Azure PowerShell" 
   description="Restauración geográfica, base de datos SQL de Microsoft Azure, restaurar base de datos, recuperar base de datos, Azure PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Recuperar una base de datos SQL de Azure mediante la restauración geográfica en Azure PowerShell

> [AZURE.SELECTOR]
- [Restauración geográfica: portal](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Restauración geográfica: API de REST](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-rest/)

## Información general

Este tutorial muestra cómo recuperar una base de datos SQL de Azure mediante la restauración geográfica en [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). La restauración geográfica es la principal protección de recuperación ante desastres incluida para todos los niveles de servicio de bases de datos Basic, Standard y Premium de Azure SQL.

## Restricciones y seguridad

Vea [Recuperar una base de datos de SQL Azure mediante la restauración geográfica en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/).

## Cómo: Recuperar una base de datos SQL de Azure con la restauración geográfica de Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Geo-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Debe utilizar la autenticación basada en certificados para ejecutar los cmdlets siguientes. Para más información, vea la sección  *Uso del método del certificado* en [Instalar y configurar Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Para obtener la lista de bases de datos recuperables, use el cmdlet [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Especifique el parámetro siguiente:
	* **ServerName**, donde se encuentra la base de datos.	

	`PS C:>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. Elija la base de datos de la que desea realizar la recuperación mediante el cmdlet [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Especifique los parámetros siguientes:
	* **ServerName**, donde se encuentra la base de datos.
	* **DatabaseName** de la base de datos desde la que va a realizar la recuperación.

	`PS C:>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" -DatabaseName "mydb"`
	 
3. Comience la recuperación mediante el uso del cmdlet [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx). Especifique los parámetros siguientes:	
	* **SourceDatabase** (base de datos de origen) que desea recuperar.
	* **TargetDatabaseName** (nombre de base de datos de destino) de la base de datos que va a recuperar.
	* **TargetServerName** (nombre del servidor de destino) en el que desea recuperar la base de datos.

	Almacene lo obtenido en una variable denominada **$RestoreRequest**. Esta variable contiene el identificador de solicitud de restauración usado para supervisar el estado de una restauración.

	`PS C:>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database -TargetDatabaseName "myrecoveredDB" -TargetServerName "mytargetserver"`
	
Puede que la recuperación de la base de datos tarde algún tiempo en completarse. Para supervisar el estado de la recuperación, use el cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) y especifique los siguientes parámetros:

* **ServerName** (nombre del servidor) de la base de datos en el que va a restaurar.
* **OperationGuid**, que es el identificador de solicitud de restauración almacenado en la variable **$RecoveryRequest** en el paso 3.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "mytargetserver" -OperationGuid $RecoveryRequest.ID`

Los campos **State** y **PercentComplete** muestran el estado de la restauración.

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:  

[Restaurar una base de datos de Azure SQL mediante la Restauración a un momento dado en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Restaurar una base de datos de SQL Azure eliminada en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauración geográfica de base de datos de Azure SQL (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
