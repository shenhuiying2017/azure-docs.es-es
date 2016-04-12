<properties
   pageTitle="Uso de las API de REST y los cmdlets de PowerShell con Almacenamiento de datos SQL"
   description="Suspender y reiniciar el Almacenamiento de datos SQL mediante cmdlets de PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="barbkess;mausher;sonyama"/>

# Uso de las API de REST y los cmdlets de PowerShell con Almacenamiento de datos SQL

El Almacenamiento de datos SQL se puede administrar mediante los cmdlets de PowerShell de Azure o las API de REST.

Los comandos definidos para **Base de datos SQL de Azure** también se usan para **Almacenamiento de datos SQL**. Para obtener una lista actualizada, consulte [Cmdlets SQL de Azure](https://msdn.microsoft.com/library/mt574084.aspx) Los cmdlets **Suspend-AzureRmSqlDatabase** y **Resume-AzureRmSqlDatabase** (a continuación) son elementos adicionales diseñados para Almacenamiento de datos SQL.

De forma similar, las API de REST para **Base de datos de SQL Azure** también pueden usarse para las instancias de **Almacenamiento de datos SQL**. Para obtener la lista actualizada, consulte [Operaciones para Bases de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Obtención y ejecución de los cmdlets de Azure PowerShell.

1. Para descargar el módulo Azure PowerShell, ejecute el [Instalador de plataforma web de Microsoft](http://aka.ms/webpi-azps). Para más información sobre este instalador, consulte [Cómo instalar y configurar Azure PowerShell][].
2. Para ejecutar el módulo, en la ventana de inicio, escriba **Windows PowerShell**.
3. Ejecute este cmdlet para iniciar sesión en el Administrador de recursos de Azure.

```PowerShell
Login-AzureRmAccount
```

3. Seleccione la suscripción para la base de datos que desea suspender o reanudar. Esta acción selecciona la suscripción llamada "MySubscription".

```Powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## Suspend-AzureRmSqlDatabase

Para ver la referencia de comandos, consulte [Suspend-AzureRmSQLDatabase](https://msdn.microsoft.com/library/mt619337.aspx).

### Ejemplo 1: Pausar una base de datos por su nombre en un servidor

Este ejemplo pausa una base de datos denominada "Database02" que está hospedada en un servidor llamado "Server01." El servidor está en un grupo de recursos de Azure denominado "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

### Ejemplo 2: Pausar un objeto de base de datos

Este ejemplo recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" incluido en un grupo de recursos denominado "ResourceGroup1." Canaliza el objeto recuperado a **Suspend-AzureRmSqlDatabase**. El resultado es que se pausa la base de datos. El comando final muestra los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Resume-AzureSqlDatabase

Para la referencia de comandos, consulte [Resume-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619347.aspx).

### Ejemplo 1: Reanudar una base de datos por su nombre en un servidor

Este ejemplo reanuda la operación de una base de datos denominada "Database02" que está hospedada en un servidor llamado "Server01." El servidor está en un grupo de recursos denominado "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

### Ejemplo 2: Reanudar un objeto de base de datos

Este ejemplo recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" que está incluido en un grupo de recursos denominado "ResourceGroup1." El objeto se canaliza a **Resume-AzureRmSqlDatabase**.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

## Get-AzureRmSqlDatabaseRestorePoints

Este cmdlet enumera los puntos de restauración de copia de seguridad de una base de datos de Almacenamiento de datos SQL. Los puntos de restauración se utilizan para restaurar la base de datos. Las propiedades del objeto devuelto son las siguientes.

Propiedad|Descripción
---|---
RestorePointType|DISCRETO Y CONTINUO. Los puntos de restauración de tipo DISCRETE (discreto) indican las posibles fechas a las que se puede restaurar una base de datos de Almacenamiento de datos SQL de Azure. Los puntos de restauración de tipo CONTINUOUS (continuo) indican las fechas más tempranas a las que se puede restaurar una base de datos SQL de Azure. La base de datos se puede restaurar a cualquier fecha posterior a la fecha más temprana.
EarliestRestoreDate|Primera hora de restauración (se rellena cuando restorePointType = CONTINUO)
RestorePointCreationDate |Hora de la instantánea de copia de seguridad (se rellena cuando restorePointType = DISCRETO)

### Ejemplo 1: Recuperar los puntos de restauración de una base de datos con nombre en un servidor
Este ejemplo recupera los puntos de restauración de una base de datos denominada "Database02" desde un servidor llamado "Server01" incluido en un grupo de recursos denominado "ResourceGroup1."

```Powershell
$restorePoints = Get-AzureRmSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints
```


### Ejemplo 2: Reanudar un objeto de base de datos

Este ejemplo recupera una base de datos denominada "Database02" desde un servidor llamado "Server01" incluido en un grupo de recursos denominado "ResourceGroup1." El objeto de base de datos se canaliza a **Get-AzureRmSqlDatabase** y el resultado obtenido son los puntos de restauración de la base de datos. El comando final imprime los resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints = $database | Get-AzureRmSqlDatabaseRestorePoints
$retorePoints
```


> [AZURE.NOTE] Tenga en cuenta que si el servidor es foo.database.windows.net, debe usar "foo" como -ServerName en los cmdlets de Powershell.


## Pasos siguientes
Para obtener más información de referencia, vea [Información general de referencia de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general de referencia de Almacenamiento de datos SQL]: sql-data-warehouse-overview-reference.md
[Cómo instalar y configurar Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0330_2016-->