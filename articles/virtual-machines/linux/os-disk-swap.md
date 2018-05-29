---
title: Intercambiar el disco del sistema operativo por una máquina virtual de Azure mediante la CLI | Microsoft Docs
description: Intercambiar el disco del sistema operativo que usa una máquina virtual de Azure mediante la CLI.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 1732b60ee135b765cdeea43f981bcef9575ff32c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195978"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Intercambiar el disco del sistema operativo que usa una máquina virtual de Azure mediante la CLI


Si ya tiene una máquina virtual, pero quiere intercambiar el disco por una copia de seguridad u otro disco del sistema operativo, puede usar la CLI de Azure para intercambiar los discos del sistema operativo. No es necesario eliminar ni volver a crear la máquina virtual. Incluso puede utilizar un disco administrado en otro grupo de recursos, siempre y cuando no esté en uso.

Es necesario que la máquina virtual esté detenida o sin asignar; a continuación, el identificador de recurso del disco administrado se puede reemplazar con el identificador de recurso de otro disco administrado. 

Asegúrese de que el tipo de almacenamiento y el tamaño de la máquina virtual son compatibles con el disco que quiere adjuntar. Por ejemplo, si el disco que quiere usar está en Premium Storage, la máquina virtual debe ser compatible con Premium Storage (por ejemplo, debe tener un tamaño de la serie DS).

Para poder realizar los pasos de este artículo, debe tener la versión 2.0.25 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 


Use [az disk list](/cli/azure/disk#list) para obtener una lista de los discos del grupo de recursos.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Use [az vm stop](/cli/azure/vm#stop) para detener o desasignar la máquina virtual antes de intercambiar los discos.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Use [az vm update](/cli/azure/vm#az-vm-update) con el id. de recurso completo del nuevo disco para el parámetro `--osdisk`. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Reinicie la máquina virtual con [az vm start](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Pasos siguientes**

Para crear una copia de un disco, consulte [Snapshot a disk](snapshot-copy-managed-disk.md) (Hacer una instantánea de un disco).