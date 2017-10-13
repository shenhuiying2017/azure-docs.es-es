---
title: "Guía de inicio rápido de Azure: Crear una cuenta de almacenamiento con PowerShell | Microsoft Docs"
description: "Aprenda rápidamente a crear una nueva cuenta de almacenamiento con PowerShell"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Creación de una cuenta de almacenamiento mediante PowerShell

El módulo de Azure PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos de PowerShell o en scripts. En esta guía se brindan detalles sobre cómo usar PowerShell para crear una cuenta de Azure Storage. 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial de inicio rápido se requiere la versión 3.6 o superior del módulo de Azure PowerShell. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

Si no sabe qué ubicación desea usar, puede enumerar las ubicaciones disponibles. Cuando se muestre la lista, busque la que desee usar. Este ejemplo se usará **eastus**. Almacénelo en una variable y úsela para que pueda cambiar este valor en un solo lugar.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Creación de una cuenta de almacenamiento estándar de uso general

Hay varios tipos de cuentas de almacenamiento, en función de cómo se va a usar y para qué tipo de servicio (blobs, archivos, tablas o colas). En esta tabla se muestran todas las posibilidades.

|**Tipo de cuenta de almacenamiento**|**Uso general estándar**|**Uso general premium**|**Niveles de acceso frecuente y esporádico de Blob Storage**|
|-----|-----|-----|-----|
|**Servicios admitidos**| Blob service, File service, Table service y Queue service | Servicio BLOB | Servicio BLOB|
|**Tipos de blobs compatibles**|Blobs en bloques, en páginas y en anexos | Blobs en páginas | Blobs en bloques y blobs en anexos|

Use [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) para crear una cuenta de almacenamiento estándar de propósito general que pueda usar para los cuadro servicios. Asigne el nombre *contosomvcstandard* a la cuenta de almacenamiento y configúrela para que tenga habilitada la opción Almacenamiento con redundancia local y cifrado de blob.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no se necesiten, puede usar el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos relacionados. En este caso, quita la cuenta de almacenamiento que acaba de crear.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido creó una cuenta de almacenamiento estándar de propósito general. Para información sobre cómo cargar y descargar blobs en la cuenta de almacenamiento siga a la guía de inicio rápido de Blob Storage.
> [!div class="nextstepaction"]
> [Transferencia de objetos a y desde Azure Blob Storage mediante PowerShell](../blobs/storage-quickstart-blobs-powershell.md)