---
title: "Creación de un disco administrado a partir de un VHD en Azure | Microsoft Docs"
description: "Cree un disco administrado a partir de un VHD que actualmente se encuentra en la cuenta de almacenamiento de Azure, mediante el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0a6e034da0445e94178e04cecc3ce09d86d2d29a
ms.lasthandoff: 04/27/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>Creación de discos administrados a partir de discos no administrados en una cuenta de almacenamiento

Se puede crear un disco administrado a partir de un disco de datos existente o de un disco del SO que actualmente se encuentra en la cuenta de almacenamiento de Azure. También puede crear un disco vacío que puede usarse como un nuevo disco de datos para una VM. 

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarla.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Creación de un disco administrado a partir de un VHD en una cuenta de almacenamiento de Azure

En el ejemplo se crea un disco a partir de un VHD como disco administrado y se asigna al parámetro **$disk1** para usarlo más adelante. 

El disco administrado se creará en la ubicación **Oeste de EE. UU.**, en un grupo de recursos denominado **myResourceGroup**. El disco se denominará **myDisk** y se creará a partir de un archivo de VHD denominado **myDisk.vhd** que se cargó previamente en una cuenta de almacenamiento llamada **mystorageaccount**. El disco administrado se creará en el almacenamiento Premium con redundancia local (LRS). StandardLRS y PremiumLRS son las únicas opciones de **-AccountType** disponibles para los discos administrados. 

1.  Configure algunos parámetros.

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. Conecte el disco de datos. 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>Creación de un disco de datos vacío como un disco administrado

En el ejemplo se crea un disco de datos vacío como disco administrado y se asigna al parámetro **$dataDisk2** para usarlo más adelante. Será necesario inicializar un disco de datos vacío iniciando sesión en la VM y usando diskmgmt.msc o [mediante el uso remoto de WinRM y de un script](attach-disk-ps.md#initialize-the-disk), una vez que se conecta a una VM en ejecución.

El disco de datos vacío se creará en la ubicación **Centro occidental de EE.UU.**, en un grupo de recursos denominado **myResourceGroup**. El disco se denominará **myEmptyDataDisk**. El disco vacío se creará en el almacenamiento Premium con redundancia local (LRS). StandardLRS y PremiumLRS son las únicas opciones de **-AccountType** disponibles para los discos administrados.

El tamaño del disco en este ejemplo es 128 GB, pero debe elegir un tamaño que satisfaga las necesidades de todas las aplicaciones que se ejecutan en la VM.

1.  Configure algunos parámetros.

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. Conecte el disco de datos.
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>Pasos siguientes    
- Si ya tiene una VM, puede [conectar un disco de datos](attach-disk-portal.md).

