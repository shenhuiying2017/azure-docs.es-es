---
title: Usar Azure PowerShell con Azure Storage | Microsoft Docs
description: Aprenda a usar los cmdlets de Azure PowerShell para Azure Storage.
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: a116b4c15046e704e374ca67c5695ff3f01ba7fb
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>Usar Azure PowerShell con Almacenamiento de Azure

Azure PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos de PowerShell o en scripts. Para Azure Storage, estos cmdlets se dividen en dos categorías: el plano de control y el plano de datos. Los cmdlets del plano de control se usan para administrar la cuenta de almacenamiento: crear cuentas de almacenamiento, establecer las propiedades, eliminar las cuentas de almacenamiento, rotar las claves de acceso, etc. Los cmdlets del plano de datos se usan para administrar los datos almacenados *en* la cuenta de almacenamiento. Por ejemplo, cargar blobs, crear recursos compartidos de archivo y agregar mensajes a una cola.

En este artículo de ayuda se tratan las operaciones comunes con los cmdlets del plano de administración para administrar las cuentas de almacenamiento. Aprenderá a: 

> [!div class="checklist"]
> * Enumerar cuentas de almacenamiento
> * Obtener una referencia a una cuenta de almacenamiento existente
> * Crear una cuenta de almacenamiento 
> * Definir las propiedades de una cuenta de almacenamiento
> * Recuperar y regenerar las claves de acceso
> * Proteger el acceso a la cuenta de almacenamiento 
> * Habilitar Storage Analytics

También se proporcionan vínculos a otros artículos de PowerShell para Storage, que tratan, por ejemplo, sobre cómo habilitar Storage Analytics y acceder a este servicio y cómo usar los cmdlets del plano de datos.
<!-- also how to access the china and government clouds  -->

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Este ejercicio requiere la versión 3.6 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). 

Para este ejercicio, puede escribir los comandos en una ventana de PowerShell regular o puede usar [Windows PowerShell ISE](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) y escribir los comandos en un editor y, después, probar uno o varios comandos a la vez mientras revisa los ejemplos. Puede resaltar las filas que desea ejecutar y hacer clic en Ejecutar seleccionado para ejecutar solo esos comandos.

Para más información sobre las cuentas de almacenamiento, vea [Introducción a Microsoft Azure Storage](storage-introduction.md) y [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Enumeración de las cuentas de almacenamiento de la suscripción

Ejecute el cmdlet [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) para recuperar la lista de cuentas de almacenamiento de la suscripción actual. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obtener una referencia a una cuenta de almacenamiento

Después, necesita una referencia a una cuenta de almacenamiento. Puede crear una cuenta de almacenamiento u obtener una referencia a una cuenta de almacenamiento ya existente. En las secciones siguientes se muestran ambos métodos. 

### <a name="use-an-existing-storage-account"></a>Uso de una cuenta de almacenamiento existente 

Para recuperar una cuenta de almacenamiento existente, necesita el nombre del grupo de recursos y el nombre de la cuenta de almacenamiento. Establezca las variables para esos dos campos y después use el cmdlet [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount). 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Ahora ya tiene $storageAccount, que señala a una cuenta de almacenamiento existente.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento 

El siguiente script muestra cómo crear una cuenta de almacenamiento de uso general mediante [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Después de crear la cuenta, recuperar su contexto, que se puede usar en los siguientes comandos en lugar de especificar la autenticación con cada llamada.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard\_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

El script usa los siguientes cmdlets de PowerShell: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation): recupera una lista de las ubicaciones válidas. En el ejemplo se usa `eastus` para la ubicación.

*   [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup): crea un grupo de recursos. Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Se llama `teststoragerg`. 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount): crea la cuenta de almacenamiento real. En el ejemplo se utiliza `testpshstorage`.

El nombre de SKU indica el tipo de replicación para la cuenta de almacenamiento, como LRS (almacenamiento con redundancia local). Para más información sobre la replicación, vea [Replicación de almacenamiento de Azure](storage-redundancy.md).

> [!IMPORTANT]
> El nombre de la cuenta de almacenamiento debe ser único en Azure y estar en minúscula. Para las convenciones de nomenclatura y otras restricciones, vea [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Convenciones de nomenclatura y referencia de contenedores, blobs y metadatos).
> 

Ahora ya tiene una cuenta de almacenamiento nueva y una referencia a ella. 

## <a name="managing-the-storage-account"></a>Administración de la cuenta de almacenamiento

Ahora que tiene una referencia a una cuenta de almacenamiento o una cuenta de almacenamiento existente, en la sección siguiente se explican algunos de los comandos que puede usar para administrar la cuenta de almacenamiento.

### <a name="storage-account-properties"></a>Propiedades de una cuenta de almacenamiento

Para cambiar la configuración de una cuenta de almacenamiento, utilice [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Aunque no puede cambiar la ubicación de una cuenta de almacenamiento o el grupo de recursos en que reside, sí puede modificar muchas de las propiedades. A continuación, se enumeran algunas de las propiedades que se pueden cambiar con PowerShell.

* El **dominio personalizado** asignado a la cuenta de almacenamiento.

* Las **etiquetas** asignadas a la cuenta de almacenamiento. Las etiquetas se suelen usar para categorizar recursos de facturación.

* La **SKU** es la configuración de replicación de la cuenta de almacenamiento, como LRS (almacenamiento con redundancia local). Por ejemplo, puede cambiar de Standard\_LRS a Standard\_GRS o Standard\_RAGRS. Tenga en cuenta que no se puede cambiar ZRS estándar o LRS premium a otra SKU ni cambiar otras SKU a estos niveles. 

* El **nivel de acceso** para las cuentas de Blob Storage. El valor del nivel de acceso se establece en **frecuente** o **esporádico** y permite minimizar los costes gracias a la selección del nivel de acceso que mejor se adapta a la forma de utilizar la cuenta de almacenamiento. Para más información, vea [Niveles de almacenamiento de archivo, esporádico y frecuente](../blobs/storage-blob-storage-tiers.md).

* La configuración de cifrado del servicio de almacenamiento para Blob Storage o File Storage. Para más información sobre SSE, vea [Cifrado del servicio Storage](storage-service-encryption.md).

* Permitir solo el tráfico HTTPS. 

### <a name="managing-the-access-keys"></a>Administración de las claves de acceso

Una cuenta de almacenamiento de Azure incluye dos claves de cuenta. Para recuperar las claves, use [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Este ejemplo recupera la primera clave. Para recuperar otra, use `Value[1]` en lugar de `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Para regenerar la clave, use [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Para regenerar la otra clave, use `key2` como el nombre de clave en lugar de `key1`.

Regenere una de las claves y después vuelva a recuperarla para ver el nuevo valor.

> [!NOTE] 
> Debe realizar un planeamiento minucioso antes de regenerar la clave para una cuenta de almacenamiento de producción. La regeneración de una o ambas claves invalidará el acceso para alguna aplicación que use la clave regenerada. Para más información, vea [Nueva generación de las claves de acceso de almacenamiento](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento 

Para eliminar una cuenta de almacenamiento, utilice [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount). 

> [!IMPORTANT]
> Cuando se elimina una cuenta de almacenamiento, también se eliminan todos los recursos almacenados en ella. Si elimina accidentalmente una cuenta, llame al soporte técnico de inmediato y abra una incidencia para restaurar la cuenta de almacenamiento. No se garantiza la recuperación de los datos, pero a veces funciona. No cree una cuenta de almacenamiento con el mismo nombre que la anterior hasta que se resuelva el vale de soporte. 
>

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

### <a name="protecting-your-storage-account-using-vnets-and-firewalls"></a>Protección de la cuenta de almacenamiento mediante redes virtuales y firewall

De forma predeterminada, se puede acceder a todas las cuentas de almacenamiento a través de cualquier red que tenga acceso a Internet. Sin embargo, puede configurar reglas de red para permitir que solo las aplicaciones de redes virtuales específicas accedan a una cuenta de almacenamiento. Para más información, vea [Configuración de Firewalls y redes virtuales de Azure Storage](storage-network-security.md). 

En el artículo se explica cómo administrar esta configuración con los siguientes cmdlets de PowerShell:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="using-storage-analytics"></a>Uso de Storage Analytics  

[Azure Storage Analytics](storage-analytics.md) consta de [métricas de Storage Analytics](/rest/api/storageservices/about-storage-analytics-metrics) y [registros de Storage Analytics](/rest/api/storageservices/about-storage-analytics-logging). 

Las **métricas de Storage Analytics** se usan para recopilar métricas de las cuentas de almacenamiento de Azure que puede usar para supervisar el mantenimiento de una cuenta de almacenamiento. Las métricas pueden habilitarse para blobs, archivos, tablas y colas.

Los **registros de Storage Analytics** se dan en el lado servidor y le permiten grabar en la cuenta de almacenamiento detalles de solicitudes correctas e incorrectas. Estos registros le permiten no solo ver detalles de lectura y escritura, sino que también le permitirán eliminar operaciones de tablas, colas, blobs y las razones por las cuales las solicitudes fallaron. Los registros no están disponibles para Azure Files.

Puede configurar la supervisión a través de [Azure Portal](https://portal.azure.com), PowerShell o mediante programación a través de la biblioteca del cliente de Storage. 

> [!NOTE]
> Puede habilitar el análisis de minutos con PowerShell. Esta funcionalidad no está disponible en el portal.
>

* Para obtener información sobre cómo habilitar y ver los datos de las métricas de Storage con PowerShell, vea [Habilitar las métricas de Azure Storage y ver sus datos](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Para obtener información sobre cómo habilitar y recuperar los datos de registro del almacenamiento con PowerShell, vea [Cómo habilitar el registro de almacenamiento con PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) y [Buscar sus datos de registro de almacenamiento](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Para obtener información detallada sobre el uso de las Métricas de almacenamiento y los Registros de almacenamiento para solucionar los problemas de almacenamiento que surjan, consulte [Supervisar, diagnosticar y solucionar problemas en Almacenamiento de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="managing-the-data-in-the-storage-account"></a>Administración de los datos de la cuenta de almacenamiento

Ahora que sabe cómo administrar la cuenta de almacenamiento con PowerShell, en los artículos siguientes se explica cómo usar PowerShell para acceder a los objetos de datos de la cuenta de almacenamiento.

* [Administración de blobs con PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Administración de archivos con PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Administración de colas con PowerShell](../queues/storage-powershell-how-to-use-queues.md)

<!--## Government Cloud and China Cloud

ROBINROBINROBIN 

To access the Government cloud of the China datacenters, you have to use some special steps. The following article shows how to access these special cloud accounts using PowerShell.

* [How to manage storage accounts in Government Cloud and China](storage-powershell-govt-china.md)
-->

## <a name="next-steps"></a>Pasos siguientes

En este artículo de ayuda se tratan las operaciones comunes con los cmdlets del plano de administración para administrar las cuentas de almacenamiento. Aprenderá a: 

> [!div class="checklist"]
> * Enumerar cuentas de almacenamiento
> * Obtener una referencia a una cuenta de almacenamiento existente
> * Crear una cuenta de almacenamiento 
> * Definir las propiedades de una cuenta de almacenamiento
> * Recuperar y regenerar las claves de acceso
> * Proteger el acceso a la cuenta de almacenamiento 
> * Habilitar Storage Analytics

También se incluyen vínculos a otros artículos, como, por ejemplo, los que tratan sobre cómo administrar los objetos de datos y cómo habilitar Storage Analytics. A continuación encontrará algunos artículos relacionados y recursos de referencia: 
<!--, and how to access storage with PowerShell using the Government Cloud and the China Cloud.
-->

* [Cmdlets de PowerShell para el plano de control de Azure Storage](/powershell/module/AzureRM.Storage/)
* [Cmdlets de PowerShell para el plano de datos de Azure Storage](/powershell/module/azure.storage/)
* [Referencia de Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)