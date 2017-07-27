---
title: "Ejemplo de script de CLI de Azure: copia (transferencia) de discos administrados en la misma suscripción o en otra | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: copia (transferencia) de discos administrados en la misma suscripción o en otra"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 90228d66150cf961557b6dbeed11308932677d83
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017

---

# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copia de discos administrados en la misma suscripción o en otra con CLI

Este script copia un disco administrado en la misma suscripción o en otra, pero dentro de la misma región. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/storage/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copiar discos administrados")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un nuevo disco administrado en la suscripción de destino mediante el identificador de disco administrado de origen. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | Obtiene todas las propiedades de un disco administrado usando las propiedades de nombre y grupo de recursos del disco administrado. La propiedad del identificador se usa para copiar el disco administrado en otra suscripción.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Copia un disco administrado mediante la creación de un nuevo disco administrado en otra suscripción con el identificador y nombre el disco administrado primario.  |

## <a name="next-steps"></a>Pasos siguientes

[Crear una máquina virtual desde un disco administrado](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Linux](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

