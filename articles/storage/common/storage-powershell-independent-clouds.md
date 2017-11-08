---
title: "Administración del almacenamiento en las nubes independientes de Azure mediante Azure PowerShell | Microsoft Docs"
description: "Administración del almacenamiento en las nubes de China, Alemania y de administración pública mediante Azure PowerShell"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: robinsh
ms.openlocfilehash: 08e1af929d7ddc30c7dc149f6305ca1ca0bc22ae
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Administración del almacenamiento en las nubes independientes mediante PowerShell

La mayoría de los usuarios utiliza la nube pública de Azure para una implementación global. También hay algunas implementaciones independientes de Microsoft Azure por motivos de soberanía, etc. Estas implementaciones independientes se conocen como "entornos". La siguiente lista detalla las nubes independientes disponibles actualmente.

* [Nube de administración pública de Azure](https://azure.microsoft.com/features/gov/)
* [Nube de China de Azure controlada por 21Vianet en China](http://www.windowsazure.cn/)
* [Nube de Alemania de Azure](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Uso de una nube independiente 

Para usar Azure Storage en una de las nubes independientes, conéctese a ella, en lugar de a la nube pública de Azure. Para usar una de las nubes independientes en lugar de la nube pública de Azure:

* Especifique el *entorno* al que se va a conectar.
* Puede determinar y usar las regiones disponibles.
* Utilice el sufijo de punto de conexión correcto, que es diferente en el caso de la nube pública de Azure.

Los ejemplos requieren la versión 4.4.0 o posterior del módulo de Azure PowerShell. En una ventana de PowerShell, ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión. Si no aparece ninguna o necesita una actualización, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Ejecute el cmdlet [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) para ver los entornos de Azure disponibles:
   
```powershell
Get-AzureRmEnvironment
```

Inicie sesión en la cuenta que tenga acceso a la nube a la que desea conectarse y establezca el entorno. Este ejemplo le muestra cómo iniciar sesión en una cuenta que utiliza la nube de administración pública de Azure.   

```powershell
Login-AzureRmAccount –Environment AzureUSGovernment
```

Para acceder a la nube de China, use el entorno **AzureChinaCloud**. Para acceder a la nube de Alemania, use **AzureGermanCloud**.

En este momento, si necesita la lista de ubicaciones para crear una cuenta de almacenamiento o algún otro recurso, puede consultar las ubicaciones disponibles de la nube seleccionada mediante [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

La siguiente tabla muestra las ubicaciones devueltas para la nube de Alemania.

|Ubicación | DisplayName |
|----|----|
| germanycentral | Centro de Alemania|
| germanynortheast | Noreste de Alemania | 


## <a name="endpoint-suffix"></a>Sufijo de punto de conexión

El sufijo de punto de conexión para cada uno de estos entornos es diferente del punto de conexión de la nube pública de Azure. Por ejemplo, el sufijo de punto de conexión de blobs para la nube pública de Azure es **blob.core.windows.net**. Para la nube de administración pública, el sufijo de punto de conexión de blobs es **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Obtención del punto de conexión mediante Get-AzureRMEnvironment 

Puede recuperar el sufijo del punto de conexión mediante [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). El punto de conexión es la propiedad *StorageEndpointSuffix* del entorno. Los siguientes fragmentos de código muestran cómo hacerlo. Todos estos comandos devuelven algo parecido a "core.cloudapp.net" o "core.cloudapi.de", etc. Adjunte esto al servicio de almacenamiento para acceder a ese servicio. Por ejemplo, "queue.core.cloudapi.de" accederá al servicio de cola en la nube de Alemania.

Este fragmento de código recupera todos los entornos y el sufijo de punto de conexión para cada uno de ellos.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Este comando devuelve los siguientes resultados.

| Nombre| StorageEndpointSuffix|
|----|----|
|AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgov.cloudapi.net |


Para recuperar todas las propiedades del entorno especificado, llame a **Get-AzureRmEnvironment** y especifique el nombre de la nube. Este fragmento de código devuelve una lista de propiedades. Busque **StorageEndpointSuffix** en la lista. El ejemplo siguiente es para la nube de Alemania.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Los resultados son similares al siguiente:

|Nombre de propiedad|Valor|
|----|----|
| Nombre | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Para recuperar solo la propiedad del sufijo de punto de conexión de almacenamiento, recupere la nube específica y pida solo esa propiedad.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Esto devuelve la siguiente información.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Obtención del punto de conexión desde una cuenta de almacenamiento

También puede examinar las propiedades de una cuenta de almacenamiento para recuperar los puntos de conexión. Esto puede resultar útil si ya está usando una cuenta de almacenamiento en el script de PowerShell. Solo puede recuperar el punto de conexión que necesita. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

En el caso de una cuenta de almacenamiento en la nube de administración pública, se devolvería lo siguiente: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Después de configurar el entorno

De aquí en adelante, puede usar el mismo PowerShell usado para administrar las cuentas de almacenamiento y acceder al plano de datos como se describe en el artículo [Uso de Azure PowerShell con Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha creado un nuevo grupo de recursos y una cuenta de almacenamiento para este ejercicio, puede quitar todos los recursos eliminando el grupo de recursos. Esto también elimina todos los recursos contenidos en el grupo. En este caso, se quita la cuenta de almacenamiento creada y el propio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

* [Conservación de inicios de sesión de usuario entre sesiones de PowerShell](/powershell/azure/context-persistence)
* [Almacenamiento de Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Guía para desarrolladores de Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Notas para desarrolladores para las aplicaciones de Azure China](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentación sobre Azure Alemania](../../germany/germany-welcome.md)