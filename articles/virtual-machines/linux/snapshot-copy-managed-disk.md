---
title: "Creación de una instantánea de un disco duro virtual en Azure | Microsoft Docs"
description: Aprenda a crear una copia de un disco duro virtual en Azure como copia de seguridad o para solucionar problemas.
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 152c5a1103d32af27f689086cfcc9cc1a7acc5d3
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-snapshot"></a>Crear una instantánea 

Tome una instantánea de un disco duro virtual de un disco de datos o del sistema operativo para realizar una copia de seguridad o para solucionar problemas de la máquina virtual. Una instantánea es una copia completa de solo lectura de un disco duro virtual. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Uso de la CLI de Azure 2.0 para realizar una instantánea

En el ejemplo siguiente, es necesario que la CLI de Azure 2.0 esté instalada y que haya iniciado sesión en la cuenta de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

Los pasos siguientes muestran cómo realizar una instantánea mediante el comando `az snapshot create` con el parámetro `--source-disk`. En el siguiente ejemplo se supone que hay una VM denominada `myVM` creada con un disco administrado del SO en el grupo de recursos `myResourceGroup`.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

La salida debe tener un aspecto similar al siguiente:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

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

Si tiene previsto utilizar la instantánea para crear un disco administrado y conectarle una VM que precisa de un alto rendimiento, use el parámetro `--sku Premium_LRS` con el comando `az snapshot create`. De esta forma, se crea la instantánea para que se almacene como un disco administrado premium. Los Managed Disks Premium tienen un rendimiento mayor porque son discos de estado sólido (SSD), pero cuestan más que los discos estándar (HDD).


## <a name="next-steps"></a>pasos siguientes

 Cree una máquina virtual a partir de una instantánea; para ello, cree primero un disco administrado con la instantánea y asocie el nuevo disco administrado como disco del sistema operativo. Para más información, consulte el script que se incluye en [Creación de una máquina virtual a partir de una instantánea](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

