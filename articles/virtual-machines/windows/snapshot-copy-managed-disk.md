---
title: Creación de una instantánea de un disco duro virtual en Azure | Microsoft Docs
description: Aprenda a crear una copia de una máquina virtual de Azure como copia de seguridad o para solucionar problemas.
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d7315d3fb7fc156beb85271d0e5aa19ec6baa7a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-snapshot"></a>Crear una instantánea

Tome una instantánea de un disco duro virtual de un disco de datos o del sistema operativo para realizar una copia de seguridad o para solucionar problemas de la máquina virtual. Una instantánea es una copia completa de solo lectura de un disco duro virtual. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Uso de Azure Portal para crear una instantánea 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la parte superior izquierda, haga clic en **Crear un recurso** y busque **instantánea**.
3. En la hoja Instantánea, haga clic en **Crear**.
4. Escriba un **nombre** para la instantánea.
5. Seleccione un valor de [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno. 
6. Seleccione una ubicación del centro de datos de Azure.  
7. Como **disco de origen**, seleccione el disco administrado para la instantánea.
8. Seleccione el **tipo de cuenta** que se usará para almacenar la instantánea. Se recomienda **Standard_LRS**, a menos que necesite almacenarla en un disco de alto rendimiento.
9. Haga clic en **Create**(Crear).

## <a name="use-powershell-to-take-a-snapshot"></a>Uso de PowerShell para crear una instantánea

Los pasos siguientes le muestran cómo obtener el disco duro virtual que se copiará, crear las configuraciones de instantánea y crear una instantánea del disco con el cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Antes de comenzar, asegúrese de que tiene la última versión del módulo AzureRM.Compute de PowerShell. Para este artículo se requiere la versión 5.7.0 del módulo de AzureRM o una versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.

Configure algunos parámetros. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Obtenga la máquina virtual.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Cree la configuración de la instantánea. En este ejemplo, vamos a crear la instantánea del disco del sistema operativo.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Si desea almacenar la instantánea en un almacenamiento resistente a zonas, debe crearla en una región que admita [zonas de disponibilidad ](../../availability-zones/az-overview.md) e incluir el parámetro `-SkuName Standard_ZRS`.   

   
Tome la instantánea.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Pasos siguientes

Cree una máquina virtual a partir de una instantánea; para ello, cree primero un disco administrado con la instantánea y conéctelo como disco del sistema operativo. Para más información, consulte el ejemplo [Creación de una máquina virtual a partir de una instantánea](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
