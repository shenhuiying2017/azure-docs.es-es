---
title: Uso de PowerShell tanto para realizar copias de seguridad de aplicaciones del Servicio de aplicaciones como para restaurarlas
description: Aprenda a usar PowerShell para realizar copias de seguridad de las aplicaciones del Servicio de aplicaciones de Azure y restaurarlas
services: app-service
documentationcenter: 
author: NKing92
manager: erikre
editor: 
ms.assetid: 7ea8661e-aefb-4823-9626-6bff980cdebf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: nicking
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 6866a16e40ea7b6953b13ae72e207f3f69a3030c
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Uso de PowerShell tanto para realizar copias de seguridad de aplicaciones del Servicio de aplicaciones como para restaurarlas

Aprenda a usar Azure PowerShell para realizar copias de seguridad de [aplicaciones de App Service](https://azure.microsoft.com/services/app-service/web/) y restaurarlas. Para más información acerca de copias de seguridad de aplicaciones web, incluidos los requisitos y las restricciones, consulte [Realizar una copia de seguridad de la aplicación en Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Requisitos previos
Si quiere usar PowerShell para administrar las copias de seguridad de las aplicaciones, necesitará lo siguiente:

* **Una dirección URL de SAS** que permita el acceso de lectura y escritura a un contenedor de Almacenamiento de Azure. Para obtener más información sobre las direcciones URL de SAS, consulte [Descripción del modelo de firmas de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md) . Consulte [Usar Azure PowerShell con Almacenamiento de Azure](../storage/common/storage-powershell-guide-full.md) para ver ejemplos de administración de Almacenamiento de Azure mediante PowerShell.
* **Una cadena de conexión de base de datos** si quiere realizar una copia de seguridad de una base de datos junto con la aplicación web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Procedimiento para generar una dirección URL de SAS que vaya a usarse con los cmdlets de copia de seguridad de la aplicación web
Las direcciones URL de SAS pueden generarse con PowerShell. Este es un ejemplo de cómo generar una que pueda usarse con los cmdlets que se explican en este artículo.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Instalación de Azure PowerShell 1.3.2, o superior
Para obtener instrucciones sobre cómo instalar y utilizar Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) .

## <a name="create-a-backup"></a>Creación de una copia de seguridad
Use el cmdlet New-AzureRmWebAppBackup para crear una copia de seguridad de una aplicación web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

De esta forma, se crea una copia de seguridad con un nombre generado automáticamente. Si desea especificar un nombre para la copia de seguridad, utilice el parámetro opcional BackupName.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Para incluir una base de datos en la copia de seguridad, cree antes una configuración de copia de seguridad de bases de datos mediante el cmdlet New-AzureRmWebAppDatabaseBackupSetting y, después, especifique dicha configuración en el parámetro Databases del cmdlet New-AzureRmWebAppBackup. El parámetro Databases acepta una matriz de la configuración de la base de datos, lo que le permite realizar copias de seguridad de más de una base de datos.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Obtención de copias de seguridad
El cmdlet Get-AzureRmWebAppBackupList devuelve una matriz de todas las copias de seguridad de una aplicación web. Debe especificar el nombre de la aplicación web y su grupo de recursos.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Para obtener una copia de seguridad específica, use el cmdlet Get-AzureRmWebAppBackup.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

También puede canalizar un objeto de aplicación web en cualquiera de los cmdlets de administración de copias de seguridad.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Programación de copias de seguridad automáticas
Puede programar se realicen automáticamente copias de seguridad a intervalos especificados. Para configurar una programación de copias de seguridad, use el cmdlet Edit-AzureRmWebAppBackupConfiguration. Este cmdlet acepta varios parámetros:

* **Name** : el nombre de la aplicación web.
* **ResourceGroupName** : el nombre del grupo de recursos que contiene la aplicación web.
* **Slot** : este parámetro es opcional. El nombre de la ranura de la aplicación web.
* **StorageAccountUrl** : la dirección URL de SAS del contenedor de Almacenamiento de Azure que se usa para almacenar las copias de seguridad.
* **FrequencyInterval** : el valor numérico de la frecuencia con la que deben realizarse las copias de seguridad. Debe ser un entero positivo.
* **FrequencyUnit** : la unidad de tiempo de la frecuencia con la que deben realizarse las copias de seguridad. Las opciones son por hora y por día.
* **RetentionPeriodInDays** : el número de días que las copias de seguridad automáticas deben guardarse antes de eliminarse automáticamente.
* **StartTime** : este parámetro es opcional. La hora a la que deben iniciarse las copias de seguridad automáticas. Si el valor es null, las copias de seguridad se iniciarán de inmediato. Debe ser una fecha y hora.
* **Databases** : este parámetro es opcional. Una matriz de DatabaseBackupSettings para las bases de datos de las que se va a realizar una copia de seguridad.
* **KeepAtLeastOneBackup** : se trata de un parámetro conmutado opcional. Especifíquelo si se debe mantener una copia de seguridad en la cuenta de almacenamiento en todo momento, independientemente de su antigüedad.

A continuación, verá un ejemplo de cómo usar este cmdlet.

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

## <a name="restore-a-web-app-from-a-backup"></a>Restauración de una aplicación web desde una copia de seguridad
Para restaurar una aplicación web desde una copia de seguridad, use el cmdlet Restore-AzureRmWebAppBackup. La manera más fácil de usar este cmdlet es canalizarlo en un objeto de copia de seguridad recuperado del cmdlet Get-AzureRmWebAppBackup o del cmdlet Get-AzureRmWebAppBackupList.

Una vez que tenga un objeto de copia de seguridad, puede canalizarlo al cmdlet Restore-AzureRmWebAppBackup. Especifique el parámetro Overwrite para indicar que quiere sobrescribir el contenido de la aplicación web por el de la copia de seguridad. Si la copia de seguridad contiene bases de datos, también se restaurarán.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

A continuación, se muestra un ejemplo de cómo utilizar Restore-AzureRmWebAppBackup especificando todos los parámetros.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Eliminación de una copia de seguridad
Para eliminar una copia de seguridad, use el cmdlet Remove-AzureRmWebAppBackup. De esta forma, se elimina la copia de seguridad de la cuenta de almacenamiento. Especifique el nombre de la aplicación, su grupo de recursos y el identificador de la copia de seguridad que quiere eliminar.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

También puede canalizar un objeto de copia de seguridad en el cmdlet Remove-AzureRmWebAppBackup para eliminarlo.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite

