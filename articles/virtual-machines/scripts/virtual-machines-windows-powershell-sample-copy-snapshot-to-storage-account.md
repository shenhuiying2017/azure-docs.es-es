---
title: "Ejemplo de script de Azure PowerShell: exportación o copia de instantánea como un VHD a una cuenta de almacenamiento en otra región | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: exportación o copia de instantánea como un VHD a una cuenta de almacenamiento en otra región"
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
ms.openlocfilehash: be21a891121df1d645b430d87b572cde6c945d61
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>Exportación o copia de instantáneas administradas como VHD a una cuenta de almacenamiento en otra región con PowerShell

Este script exporta una instantánea administrada a una cuenta de almacenamiento en otra región. En primer lugar, genera el identificador URI de SAS de la instantánea y, luego, lo usa para copiarlo en una cuenta de almacenamiento en una región diferente. Use este script para mantener una copia de seguridad de los discos administrados en una región distinta para la recuperación ante desastres.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para generar el identificador URI de SAS para una instantánea administrada y copia la instantánea en una cuenta de almacenamiento usando el URI de SAS. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [Grant-AzureRmSnapshotAccess](/powershell/module/azurerm.compute/New-AzureRmDisk) | Genera el URI de SAS de una instantánea que se usa para copiarla en una cuenta de almacenamiento. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Crea un contexto de cuenta de almacenamiento con el nombre de cuenta y la clave. Este contexto se puede usar para realizar operaciones de lectura y escritura en la cuenta de almacenamiento. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | Copia el VHD subyacente de una instantánea en una cuenta de almacenamiento. |

## <a name="next-steps"></a>Pasos siguientes

[Crear un disco administrado a partir de un VHD](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[Crear una máquina virtual a partir de un disco administrado](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de Azure PowerShell de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).