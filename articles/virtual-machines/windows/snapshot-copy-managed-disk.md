---
title: "Creación de una instantánea de un disco duro virtual en Azure | Microsoft Docs"
description: "Aprenda a crear una copia de una máquina virtual de Azure como copia de seguridad o para solucionar problemas."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: dba70db512d88dfc57107bade0df50d1834eb883
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Crear una instantánea

Tome una instantánea de un disco duro virtual de un disco de datos o del sistema operativo para realizar una copia de seguridad o para solucionar problemas de la máquina virtual. Una instantánea es una copia completa de solo lectura de un disco duro virtual. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Uso de Azure Portal para crear una instantánea 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la parte superior izquierda, haga clic en **Nuevo** y busque **instantánea**.
3. En la hoja Instantánea, haga clic en **Crear**.
4. Escriba un **nombre** para la instantánea.
5. Seleccione un valor de [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno. 
6. Seleccione una ubicación del centro de datos de Azure.  
7. Como **disco de origen**, seleccione el disco administrado para la instantánea.
8. Seleccione el **tipo de cuenta** que se usará para almacenar la instantánea. Se recomienda **Standard_LRS**, a menos que necesite almacenarla en un disco de alto rendimiento.
9. Haga clic en **Crear**.

## <a name="use-powershell-to-take-a-snapshot"></a>Uso de PowerShell para crear una instantánea
Los pasos siguientes le muestran cómo obtener el disco duro virtual que se copiará, crear las configuraciones de instantánea y crear una instantánea del disco con el cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Asegúrese de que tiene instalada la versión más reciente del módulo AzureRM.Compute de PowerShell. Ejecute el siguiente comando para realizar la instalación.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


1. Configure algunos parámetros. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Obtenga el disco VHD que se copiará.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Cree las configuraciones de la instantánea. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Tome la instantánea.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Si tiene previsto utilizar la instantánea para crear un disco administrado y conectarle una VM que precisa de un alto rendimiento, use el parámetro `-AccountType Premium_LRS` con el comando New-AzureRmSnapshot. El parámetro crea la instantánea para que se almacene como disco administrado Premium. Managed Disks Premium son más costosos que los Estándar. Por lo tanto, asegúrese de que realmente necesita discos Premium antes de usar el parámetro.

## <a name="next-steps"></a>Pasos siguientes

Cree una máquina virtual a partir de una instantánea; para ello, cree primero un disco administrado con la instantánea y conéctelo como disco del sistema operativo. Para más información, consulte el ejemplo [Creación de una máquina virtual a partir de una instantánea](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
