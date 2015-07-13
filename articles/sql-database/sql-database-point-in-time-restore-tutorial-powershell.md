<properties 
   pageTitle="Restaurar una base de datos SQL de Azure mediante la restauración a un momento dado en Azure PowerShell" 
   description="Restauración a un momento dado, base de datos SQL de Microsoft Azure, restaurar base de datos, recuperar base de datos, Azure PowerShell" 
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
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Restaurar una base de datos SQL de Azure mediante la restauración a un momento dado en Azure PowerShell

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md) 

## Información general

Este tutorial muestra cómo restaurar una base de datos SQL de Azure mediante la restauración a un momento dado en [Azure PowerShell](../powershell-install-configure.md). La base de datos SQL de Azure tiene copias de seguridad integradas para admitir sin intervención la restauración a un momento dado para los niveles de servicio Basic, Standard y Premium.

La restauración a un momento dado crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [servicio de soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

Consulte [Restaurar una base de datos SQL de Azure mediante la Restauración a un momento dado en el portal de Azure](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Cómo: Restaurar una base de datos SQL de Azure mediante la restauración a un momento dado en Azure PowerShell

> [AZURE.VIDEO restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell]

Debe utilizar la autenticación basada en certificados para ejecutar los cmdlets siguientes. Para más información, consulte la sección *Uso del método del certificado* en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md#use-the-certificate-method).

1. Para obtener la base de datos que desea restaurar, use el cmdlet [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx). Especifique los parámetros siguientes:
	* **ServerName** (nombre de servidor) donde se encuentra la base de datos.
	* **DatabaseName** (nombre) de la base de datos que desea restaurar.	

	`PS C:\>$Database = Get-AzureSqlDatabase -ServerName "myserver" –DatabaseName “mydb”`

2. Comience la restauración usando el cmdlet [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Especifique los parámetros siguientes:
	* **SourceDatabase** (base de datos de origen) desde la que desea realizar la restauración.
	* **TargetDatabaseName** (nombre de base de datos de destino) de la base de datos en la que va a restaurar.
	* **PointInTime** (momento dado) en el que desea restaurar.

	Almacene lo obtenido en una variable denominada **$RestoreRequest**. Esta variable contiene el identificador de solicitud de restauración usado para supervisar el estado de una restauración.

	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “myrestoredDB” –PointInTime “2015-01-01 06:00:00”`

Una restauración puede tardar algún tiempo en completarse. Para supervisar el estado de la restauración, utilice el cmdlet [Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) y especifique los siguientes parámetros:

* **ServerName** (nombre del servidor) de la base de datos en la que va a restaurar.
* **OperationGuid**, que es el identificador de solicitud de restauración almacenado en la variable **$RestoreRequest** en el paso 2.

	`PS C:\>Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

Los campos **State** (estado) y **PercentComplete** (porcentaje finalizado) muestran el estado de la restauración.

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL Database Point in Time Restore](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/) (Restauración a un momento dado de Base de datos SQL de Azure, blog)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=62-->