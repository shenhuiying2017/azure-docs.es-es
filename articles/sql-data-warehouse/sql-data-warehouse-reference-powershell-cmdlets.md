<properties
   pageTitle="Introducción a los cmdlets de Almacenamiento de datos SQL"
   description="Suspender y reiniciar el Almacenamiento de datos SQL mediante cmdlets de PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sidneyh"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="twounder;sidneyh;barbkess"/>

# Introducción a los cmdlets de Almacenamiento de datos de Azure y a las API de REST

El Almacenamiento de datos SQL se puede administrar mediante los cmdlets de PowerShell de Azure o las API de REST.

Los comandos definidos para **Base de datos SQL de Azure** también se usan para **Almacenamiento de datos SQL**. Para obtener una lista actualizada, consulte [Cmdlets SQL de Azure](https://msdn.microsoft.com/library/azure/dn546726.aspx) Los cmdlets **Suspend-AzureSqlDatabase** y **Resume-AzureSqlDatabase** (a continuación) son elementos adicionales diseñados para Almacenamiento de datos SQL.

De forma similar, las API de REST para **Base de datos de SQL Azure** también pueden usarse para las instancias de **Almacenamiento de datos SQL**. Para obtener la lista actualizada, consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Obtención y ejecución de los cmdlets de Azure PowerShell.

1. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). 
2. Para ejecutar el módulo, en la ventana de inicio, escriba **Microsoft Azure PowerShell**.
3. Si no ha agregado todavía su cuenta a la máquina, ejecute el siguiente cmdlet. (Para obtener más información, consulte [Instalación y configuración de Azure PowerShell]():

		Add-AzureAccount
3. Cambie el modo con este cmdlet:

		Switch-AzureMode AzureResourceManager

## Suspend-AzureSqlDatabase
### Ejemplo 1: Pausar una base de datos por su nombre en un servidor

Este ejemplo pausa una base de datos denominada "Database02" que está hospedada en un servidor llamado "Server01." El servidor está en un grupo de recursos de Azure denominado "ResourceGroup1."

    Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
    "Server01" –DatabaseName "Database02"

### Ejemplo 2: Pausar un objeto de base de datos

Este ejemplo recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" incluido en un grupo de recursos denominado "ResourceGroup1." Canaliza el objeto recuperado a **Suspend-AzureSqlDatabase**. El resultado es que se pausa la base de datos.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Suspend-AzureSqlDatabase

## Resume-AzureSqlDatabase

### Ejemplo 1: Reanudar una base de datos por su nombre en un servidor

Este ejemplo reanuda la operación de una base de datos denominada "Database02" que está hospedada en un servidor llamado "Server01." El servidor está en un grupo de recursos denominado "ResourceGroup1."

	Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"

### Ejemplo 2: Reanudar un objeto de base de datos

Este ejemplo recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" que está incluido en un grupo de recursos denominado "ResourceGroup1." El objeto se canaliza a **Resume-AzureSqlDatabase**.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$resultDatabase = $database | Resume-AzureSqlDatabase

## Get-AzureSqlDatabaseRestorePoints

Este cmdlet enumera los puntos de restauración de copia de seguridad de una base de datos SQL de Azure. Los puntos de restauración se utilizan para restaurar la base de datos. Las propiedades del objeto devuelto son las siguientes.

Propiedad|Descripción
---|---
RestorePointType|DISCRETO Y CONTINUO. Los puntos de restauración de tipo DISCRETE (discreto) indican las posibles fechas a las que se puede restaurar una base de datos SQL de Azure. Los puntos de restauración de tipo CONTINUOUS (continuo) indican las fechas más tempranas a las que se puede restaurar una base de datos SQL de Azure. La base de datos se puede restaurar a cualquier fecha posterior a la fecha más temprana.
EarliestRestoreDate|Primera hora de restauración (se rellena cuando restorePointType = CONTINUO)
RestorePointCreationDate |Hora de la instantánea de copia de seguridad (se rellena cuando restorePointType = DISCRETO)

### Ejemplo 1: Recuperar los puntos de restauración de una base de datos con nombre en un servidor
Este ejemplo recupera los puntos de restauración de una base de datos denominada "Database02" desde un servidor llamado "Server01" incluido en un grupo de recursos denominado "ResourceGroup1."

	$restorePoints = Get-AzureSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"



### Ejemplo 2: Reanudar un objeto de base de datos

Este ejemplo recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" incluido en un grupo de recursos denominado "ResourceGroup1." El objeto de base de datos se canaliza a **Get-AzureSqlDatabase** y el resultado obtenido son los puntos de restauración de la base de datos.

	$database = Get-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
	$restorePoints = $database | Get-AzureSqlDatabaseRestorePoints



> [AZURE.NOTE]Tenga en cuenta que si el servidor es foo.database.windows.net, use "foo" como -ServerName en los cmdlets de powershell.


## Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general de referencia de Almacenamiento de datos SQL]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=Sept15_HO4-->