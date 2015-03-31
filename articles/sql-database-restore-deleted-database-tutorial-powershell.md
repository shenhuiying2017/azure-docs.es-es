<properties 
   pageTitle="Restaurar una base de datos SQL de Azure eliminada en Azure PowerShell" 
   description="Base de datos SQL de Microsoft Azure, restaurar base de datos eliminada, recuperar base de datos eliminada, Azure PowerShell" 
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

# Restaurar una base de datos SQL de Azure eliminada en Azure PowerShell

> [AZURE.SELECTOR]
- [Restaurar la base de datos eliminada: portal](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [Restaurar la base de datos eliminada: API de REST](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-rest/)

## Información general

Este tutorial muestra cómo restaurar una base de datos SQL de Azure eliminada en [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Puede restaurar una base de datos que se eliminó durante su período de retención en el punto en que se eliminó. El período de retención determina el nivel de servicio de la base de datos.

Al restaurar una base de datos de SQL Azure eliminada, se crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

Vea [Restaurar una base de datos de SQL Azure eliminada en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/).

## Cómo: Restaurar una base de datos SQL de Azure eliminada en Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-Deleted-SQL-Database-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Debe utilizar la autenticación basada en certificados para ejecutar los cmdlets siguientes. Para más información, vea la sección  *Uso del método del certificado* en [Instalar y configurar Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Obtenga la lista de bases de datos recuperables mediante el cmdlet [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx).
	* Utilice el modificador **RestorableDropped** y especifique el **ServerName** (nombre del servidor) del servidor del que se ha eliminado la base de datos.
	* Al ejecutar el comando siguiente, los resultados se almacenan en una variable denominada **$RecoverableDBs**.
	
	`PS C:\>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" -RestorableDropped`

2. Elija la base de datos eliminada que desea restaurar de la lista de bases de datos eliminadas.

	* Escriba el número de la base de datos eliminada de la lista **$RecoverableDBs**.  

	`PS C:\>$Database = $RecoverableDBs[<deleted database number>]`

	* Para obtener más información acerca de cómo obtener un objeto de base de datos eliminada, consulte [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx).

3. Comience la restauración usando el cmdlet [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Especifique los parámetros siguientes:	
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName** (nombre de base de datos de destino) de la base de datos en la que va a restaurar.

	Almacene lo obtenido en una variable denominada **$RestoreRequest**. Esta variable contiene el identificador de solicitud de restauración usado para supervisar el estado de una restauración.
	
	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database -TargetDatabaseName "myrestoredDB"`

Una restauración puede tardar algún tiempo en completarse. Para supervisar el estado de la restauración, utilice el cmdlet [Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) y especifique los siguientes parámetros:

* **ServerName** (nombre del servidor) de la base de datos en el que va a restaurar.
* **OperationGuid**, que es el identificador de solicitud de restauración almacenado en la variable **$RestoreRequest** en el paso 3.

	`PS C:\>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

Los campos **State** y **PercentComplete** muestran el estado de la restauración.

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)
<!--HONumber=47-->
