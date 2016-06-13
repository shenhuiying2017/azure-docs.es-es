<properties
	pageTitle="Uso de PowerShell tanto para realizar copias de seguridad de aplicaciones del Servicio de aplicaciones como para restaurarlas"
	description="Aprenda a usar PowerShell para realizar copias de seguridad de las aplicaciones del Servicio de aplicaciones de Azure y restaurarlas"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="nicking"/>
# Uso de PowerShell tanto para realizar copias de seguridad de aplicaciones del Servicio de aplicaciones como para restaurarlas

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API DE REST](../app-service-web/websites-csm-backup.md)

Aprenda a usar Azure PowerShell tanto para realizar copias de seguridad de [aplicaciones del Servicio de aplicaciones](https://azure.microsoft.com/services/app-service/web/) como para restaurarlas Para más información sobre copias de seguridad de aplicaciones web, incluidos los requisitos y las restricciones, consulte [Hacer copia de seguridad de una aplicación web en el servicio de aplicaciones de Azure](../app-service-web/web-sites-backup.md).

## Requisitos previos
Para usar PowerShell para administrar las copias de seguridad de las aplicaciones, necesitará lo siguiente:

- **Una dirección URL de SAS** que permita el acceso de lectura y escritura a un contenedor de Almacenamiento de Azure. Para más información sobre las direcciones URL de SAS, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)
- **Una cadena de conexión de base de datos** si desea realizar una copia de seguridad de una base de datos junto con la aplicación web.

##Instalación de Azure PowerShell 1.3.2, o superior

Para obtener instrucciones sobre la instalación y uso de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## Creación de una copia de seguridad

Use el cmdlet New-AzureRmWebAppBackup para crear una copia de seguridad de una aplicación web.

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Así se creará una copia de seguridad con un nombre generado automáticamente. Si desea especificar un nombre para la copia de seguridad, utilice el parámetro opcional BackupName.

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Si desea incluir una base de datos como parte de la copia de seguridad, cree antes una configuración de la copia de seguridad de bases de datos mediante el cmdlet New-AzureRmWebAppDatabaseBackupSetting y, a continuación, especifique dicha configuración el parámetro Databases del cmdlet New-AzureRmWebAppBackup. El parámetro Databases acepta una matriz de la configuración de la base de datos, lo que le permite realizar copias de seguridad de más de una base de datos.

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## Obtención de copias de seguridad

El cmdlet Get-AzureRmWebAppBackupList devolverá una matriz de todas las copias de seguridad de una aplicación web. Debe especificar el nombre de la aplicación web y su grupo de recursos.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Para obtener una copia de seguridad específica, use el cmdlet Get-AzureRmWebAppBackup.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

También puede canalizar un objeto de aplicación web en cualquiera de los cmdlets de administración de copias de seguridad.

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## Programación de copias de seguridad automáticas

Puede programar se realicen automáticamente copias de seguridad a intervalos especificados. Para configurar una programación de copias de seguridad, use el cmdlet Edit-AzureRmWebAppBackupConfiguration. Este cmdlet acepta varios parámetros:

- **Name**: el nombre de la aplicación web.
- **ResourceGroupName**: el nombre del grupo de recursos que contiene la aplicación web.
- **Slot**: opcional. El nombre de la ranura de la aplicación web.
- **StorageAccountUrl**: la dirección URL de SAS del contenedor de Almacenamiento de Azure que se usa para almacenar las copias de seguridad.
- **FrequencyInterval**: el valor numérico de la frecuencia con la que deben realizarse las copias de seguridad. Debe ser un entero positivo.
- **FrequencyUnit**: unidad de tiempo de la frecuencia con la que deben realizarse las copias de seguridad. Las opciones son por hora y por día.
- **RetentionPeriodInDays**: número de días que las copias de seguridad automáticas deben guardarse antes de eliminarse automáticamente.
- **StartTime**: opcional. La hora a la que deben iniciarse las copias de seguridad automáticas. Si el valor es null, las copias de seguridad se iniciarán de inmediato. Debe ser una fecha y hora.
- **Bases de datos**: opcional. Una matriz de DatabaseBackupSettings para las bases de datos de las que se va a realizar una copia de seguridad.
- **KeepAtLeastOneBackup**: parámetro conmutado opcional. Especifíquelo si se debe mantener una copia de seguridad en la cuenta de almacenamiento en todo momento, independientemente de su antigüedad.

A continuación verá un ejemplo de cómo usar este cmdlet.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Para obtener la programación de copias de seguridad actual, use el cmdlet Get-AzureRmWebAppBackupConfiguration cmdlet. Puede resultar útil para modificar una programación que ya se ha configurado.

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## Restauración de una aplicación web desde una copia de seguridad

Para restaurar una aplicación web desde una copia de seguridad, use el cmdlet Restore-AzureRmWebAppBackup. La manera más fácil de usar este cmdlet es canalizarlo en un objeto de copia de seguridad recuperado del cmdlet Get-AzureRmWebAppBackup o del cmdlet Get-AzureRmWebAppBackupList.

Una vez que tenga un objeto de copia de seguridad, puede canalizarlo al cmdlet Restore-AzureRmWebAppBackup. Debe especificar el parámetro Overwrite para indicar que desea sobrescribir el contenido de la aplicación web con el contenido de la copia de seguridad. Si la copia de seguridad contiene bases de datos, estás también se restaurarán.

		$backup | Restore-AzureRmWebAppBackup -Overwrite

A continuación se muestra un ejemplo de cómo utilizar Restore-AzureRmWebAppBackup especificando todos los parámetros.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## Eliminación de una copia de seguridad

Para eliminar una copia de seguridad, use el cmdlet Remove-AzureRmWebAppBackup. Así se eliminará la copia de seguridad de la cuenta de almacenamiento. Debe especificar el nombre de la aplicación, su grupo de recursos y el identificador de la copia de seguridad que desea eliminar.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

También puede canalizar un objeto de copia de seguridad en el cmdlet Remove-AzureRmWebAppBackup para eliminarlo.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0601_2016-->