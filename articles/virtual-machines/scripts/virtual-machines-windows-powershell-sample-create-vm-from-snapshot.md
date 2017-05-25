---
title: "Ejemplo de script de Azure PowerShell: creación de una máquina virtual a partir de una instantánea | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: creación de una máquina virtual a partir de una instantánea"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: e481e2512f324733bb78f7d6f337b9522e78bea8
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017

---

# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell"></a>Creación de una máquina virtual a partir de una instantánea con PowerShell

Este script crea una máquina virtual a partir de una instantánea de un disco del SO. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Creación de una máquina virtual desde un disco del SO administrado")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para obtener las propiedades de las instantáneas, crear un disco administrado a partir de una instantánea y crear una máquina virtual. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/get-azurermsnapshot) | Obtiene una instantánea con el nombre de la instantánea. |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) | Crea una configuración de disco. Esta configuración se utiliza con el proceso de creación del disco. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) | Crea un disco administrado. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crea una configuración de máquina virtual. Esta configuración incluye diversa información, como el nombre de la máquina virtual, sistema el operativo y las credenciales administrativas. La configuración se utiliza durante la creación de las máquinas virtuales. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Conecta el disco administrado como disco del SO a la máquina virtual. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea una dirección IP pública. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crea una interfaz de red. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Crea una máquina virtual. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Quita un grupo de recursos y todos los recursos incluidos en él. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de Azure PowerShell de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
