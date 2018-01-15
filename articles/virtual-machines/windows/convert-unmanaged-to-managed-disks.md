---
title: "Conversión de una máquina virtual Windows con discos no administrados a discos administrados - Azure Managed Disks | Microsoft Docs"
description: "Conversión de una máquina virtual Windows con discos no administrados a discos administrados mediante PowerShell en el modelo de implementación de Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: dd9ebaf9a1c8b3112623af4228efa0d9063c1e52
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Conversión de máquina virtual Windows con discos no administrados en discos administrados

Si ya dispone de máquinas virtuales Windows que usan discos no administrados, puede convertirlas para usar discos administrados mediante el servicio [Azure Managed Disks](managed-disks-overview.md). Este proceso convierte el disco del SO y los discos de datos conectados.

En este artículo se explica cómo convertir máquinas virtuales con Azure PowerShell. Si necesita instalarlas o actualizarlas, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="before-you-begin"></a>Antes de empezar


* Revise [Planeación de la migración a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Revise las [preguntas frecuentes sobre la migración a Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Conversión de máquinas virtuales de instancia única
En esta sección se explica cómo convertir máquinas virtuales de Azure de instancia única de Unmanaged Disks a Managed Disks. (Si las máquinas virtuales se encuentran en un conjunto de disponibilidad, consulte la sección siguiente). 

1. Desasigne la máquina virtual con el cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convierta la máquina virtual a discos administrados con el cmdlet [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). El proceso siguiente convierte la VM anterior, incluidos el disco del SO y todos los discos de datos:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Inicie la máquina virtual después de realizar la conversión a discos administrados con [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). En el ejemplo siguiente se reinicia el VM anterior:

  ```azurepowershell-interactive
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Conversión de VM de un conjunto de disponibilidad

Si las VM que desea convertir en discos administrados se encuentran en un conjunto de disponibilidad, primero debe convertir el conjunto de disponibilidad en un conjunto de disponibilidad administrado.

1. Convierta el conjunto de disponibilidad con el cmdlet [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). En el ejemplo siguiente se actualiza el conjunto de disponibilidad denominado `myAvailabilitySet` en el grupo de recursos con nombre `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Si la región en que se encuentra el conjunto de disponibilidad solo tiene 2 dominios de error administrados, pero el número de dominios de error no administrados es 3, este comando muestra un error similar a "El número de dominios de error especificado 3 debe estar en el intervalo de 1 a 2". Para resolver el error, actualice el dominio predeterminado a 2 y actualice `Sku` a `Aligned` como sigue:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Desasigne y convierta las máquinas virtuales del conjunto de disponibilidad. El siguiente script desasigna cada máquina virtual mediante el cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la convierte con [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) y la reinicia con [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name
  }
  ```


## <a name="troubleshooting"></a>solución de problemas

Si se produce un error durante la conversión, o si una máquina virtual presenta un estado de error debido a errores en una conversión anterior, ejecute el cmdlet `ConvertTo-AzureRmVMManagedDisk` de nuevo. Normalmente, un simple reintento desbloquea la situación.
Antes de realizar la conversión, asegúrese de que todas las extensiones de máquina virtual se encuentran en el estado de "Aprovisionamiento realizado correctamente". De lo contrario, se producirá un error de conversión con el código de error 409.


## <a name="next-steps"></a>pasos siguientes

[Conversión de Managed Disks estándar a premium](convert-disk-storage.md)

Realice una copia de solo lectura de una máquina virtual mediante [instantáneas](snapshot-copy-managed-disk.md).

