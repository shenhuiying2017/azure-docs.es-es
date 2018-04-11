---
title: Creación de una instantánea de un disco duro virtual en Azure | Microsoft Docs
description: Aprenda a crear una copia de un disco duro virtual en Azure como copia de seguridad o para solucionar problemas.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-snapshot"></a>Crear una instantánea 

Tome una instantánea de un disco de datos o del sistema operativo para realizar una copia de seguridad o para solucionar problemas de la máquina virtual. Una instantánea es una copia completa de solo lectura de un disco duro virtual. 

## <a name="use-azure-cli"></a>Uso de CLI de Azure 

En el ejemplo siguiente, es necesario que la CLI de Azure 2.0 esté instalada y que haya iniciado sesión en la cuenta de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

Los pasos siguientes muestran cómo realizar una instantánea mediante el comando `az snapshot create` con el parámetro `--source-disk`. En el siguiente ejemplo se supone que hay una máquina virtual denominada `myVM` en el grupo de recursos `myResourceGroup`.

Obtenga el identificador de disco.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Realice una instantánea denominada *osDisk-backup*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Si desea almacenar la instantánea en un almacenamiento resistente a zonas, debe crearla en una región que admita [zonas de disponibilidad ](../../availability-zones/az-overview.md) e incluir el parámetro `--sku Standard_ZRS`.

## <a name="use-azure-portal"></a>Usar Azure Portal 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la parte superior izquierda, haga clic en **Crear un recurso** y busque **instantánea**.
3. En la hoja Instantánea, haga clic en **Crear**.
4. Escriba un **nombre** para la instantánea.
5. Seleccione un valor de [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno. 
6. Seleccione una ubicación del centro de datos de Azure.  
7. Como **disco de origen**, seleccione el disco administrado para la instantánea.
8. Seleccione el **tipo de cuenta** que se usará para almacenar la instantánea. Se recomienda **Standard_LRS**, a menos que necesite almacenarla en un disco de alto rendimiento.
9. Haga clic en **Create**(Crear).


## <a name="next-steps"></a>Pasos siguientes

 Cree una máquina virtual a partir de una instantánea; para ello, cree primero un disco administrado con la instantánea y asocie el nuevo disco administrado como disco del sistema operativo. Para más información, consulte el script que se incluye en [Creación de una máquina virtual a partir de una instantánea](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

