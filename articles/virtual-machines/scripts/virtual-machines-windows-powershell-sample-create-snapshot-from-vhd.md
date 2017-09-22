---
title: "Ejemplo de script de Azure PowerShell: creación de una instantánea desde un VHD para crear varios discos administrados idénticos en poco tiempo | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: creación de una instantánea desde un VHD para crear varios discos administrados idénticos en poco tiempo"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: ae21e8ab5b718cc8cedb33aa31a399ebf7a80379
ms.contentlocale: es-es
ms.lasthandoff: 09/20/2017

---

# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Creación de una instantánea desde un VHD para crear varios discos administrados idénticos en poco tiempo con PowerShell

Este script crea una instantánea desde un archivo VHD en una cuenta de almacenamiento en una misma suscripción o en una suscripción distinta. Use este script para importar un VHD especializado (no generalizado ni preparado con SysPrep) a una instantánea y luego usar esta instantánea para crear varios discos administrados idénticos en poco tiempo. Además, úsela para importar un VHD de datos a una instantánea y luego use esta instantánea para crear varios discos administrados en poco tiempo. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>Explicación del script

Este script utiliza los comandos siguientes para crear un disco administrado desde un archivo VHD en una suscripción distinta. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Crea la configuración de disco que se usa para la creación del disco. Incluye tipo de almacenamiento, ubicación, identificador de recurso de la cuenta de almacenamiento donde se almacena el VHD principal y el URI de VHD del VHD principal. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crea un disco mediante la configuración de disco, el nombre del disco y el nombre del grupo de recursos que se pasan como parámetros. |

## <a name="next-steps"></a>Pasos siguientes

[Creación de un disco administrado a partir de una instantánea](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Creación de una máquina virtual conectando un disco administrado como disco del SO](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de Azure PowerShell de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
