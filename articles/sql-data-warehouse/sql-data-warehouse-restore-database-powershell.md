<properties
   pageTitle="Restauración de instancias de Almacenamiento de datos SQL de Azure (PowerShell) | Microsoft Azure"
   description="Tareas de PowerShell para restaurar una instancia de Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restauración de instancias de Almacenamiento de datos SQL de Azure (PowerShell)

> [AZURE.SELECTOR]
- [Información general][]
- [Portal][]
- [PowerShell][]
- [REST][]

En este artículo, obtendrá información sobre cómo restaurar una instancia de Almacenamiento de datos SQL de Azure mediante PowerShell.

## Antes de empezar

### Compruebe su capacidad de DTU de Base de datos SQL. 

Cada instancia de Almacenamiento de datos SQL está hospedada en un servidor lógico de SQL Server. Este servidor lógico tiene un límite de capacidad expresado en DTU. Antes de poder restaurar una instancia de Almacenamiento de datos SQL, es importante asegurarse de que el servidor lógico de SQL Server que hospeda la base de datos tiene suficiente capacidad DTU para la base de datos que se está restaurando. Consulte este blog para obtener más información sobre [cómo ver y aumentar la cuota de DTU][].

### Instale PowerShell.

Para usar Azure Powershell con Almacenamiento de datos SQL, se necesita instalar Azure PowerShell versión 1.0 o superior. Puede comprobar la versión ejecutando **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][]. Para obtener más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

## Restauración de una base de datos activa o en pausa

Para restaurar una base de datos a partir de una instantánea, use el cmdlet [Restore-AzureRmSqlDatabase][] de PowerShell.

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
4. Enumere los puntos de restauración de la base de datos.
5. Elija el punto de restauración deseado mediante RestorePointCreationDate.
6. Restaure la base de datos al punto de restauración deseado.
7. Compruebe que la base de datos restaurada está en línea.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo la guía [Finalización de una base de datos SQL de Azure recuperada][].

## Restauración de una base de datos eliminada

Para restaurar una base de datos eliminada, use el cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos eliminada que se va a restaurar.
4. Obtenga la base de datos eliminada específica.
5. Restaure la base de datos eliminada.
6. Compruebe que la base de datos restaurada está en línea.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

Una vez finalizada la restauración, puede configurar la base de datos recuperada siguiendo las instrucciones de la guía de [finalización de una base de datos recuperada][].

## Restauración dese una región geográfica de Azure

Para recuperar una base de datos, use el cmdlet [Restore-AzureRmSqlDatabase][].

1. Abra Windows PowerShell.
2. Conéctese a su cuenta de Azure y enumere todas las suscripciones asociadas a su cuenta.
3. Seleccione la suscripción que contiene la base de datos que se va a restaurar.
4. Obtenga la base de datos que desea recuperar.
5. Cree la solicitud de recuperación para la base de datos.
6. Compruebe el estado de la base de datos restaurada geográficamente.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

### Configuración de la base de datos después de realizar una restauración geográfica

Esta es una lista de comprobación que le ayudará a tener preparada la producción de la base de datos recuperada.

1. **Actualización de cadenas de conexión**: compruebe que las cadenas de conexión de las herramientas del cliente apuntan a la base de datos recién recuperada.
2. **Modificación de las reglas de firewall**: compruebe las reglas de firewall en el servidor de destino y asegúrese de que están habilitadas las conexiones desde los equipos cliente, o bien Azure al servidor y a la base de datos recién recuperada.
3. **Comprobación de los inicios de sesión del servidor y los usuarios de la base de datos**: compruebe si todos los inicios de sesión que usa la aplicación existen en el servidor que hospeda la base de datos recuperada. Vuelva a crear los inicios de sesión que falten y concédales los permisos adecuados en la base de datos recuperada. 
4. **Habilitar auditoría**: si se requiere una auditoría para tener acceso a una base de datos, será preciso habilitar Auditoría tras la recuperación de la base de datos.

La base de datos recuperada estará habilitada para TDE si la base de datos de origen está habilitada para TDE.


## Pasos siguientes
Para más información sobre las características de continuidad del negocio de las ediciones de Base de datos SQL de Azure, lea [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL][].

<!--Image references-->

<!--Article references-->
[Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL]: sql-database-business-continuity.md
[Finalización de una base de datos SQL de Azure recuperada]: sql-database-recovered-finalize.md
[finalización de una base de datos recuperada]: sql-database-recovered-finalize.md
[Cómo instalar y configurar Azure PowerShell]: powershell-install-configure.md
[Información general]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[cómo ver y aumentar la cuota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0615_2016-->