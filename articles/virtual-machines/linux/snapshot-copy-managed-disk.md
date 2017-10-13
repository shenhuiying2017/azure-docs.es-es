---
title: Copia de un disco administrado de Azure para copias de seguridad | Microsoft Docs
description: "Obtenga información sobre cómo crear una copia de un disco administrado de Azure que se usará para copias de seguridad o solucionar problemas del disco."
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
ms.openlocfilehash: c91367ef11c9d531bebac7c069d2df586607ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Creación de una copia de un VHD almacenado como un disco administrado de Azure mediante instantáneas administradas
Cree una instantánea de un disco administrado para copias de seguridad o cree un disco administrado a partir de la instantánea y conéctelo a una máquina virtual de prueba para fines de solución de problemas. Una instantánea administrada es una copia completa a partir de un momento específico de un disco administrado de VM. Crea una copia de solo lectura del VHD y, de forma predeterminada, se almacena como un Disco administrado estándar. 

Para información sobre los precios, consulte [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/managed-disks/). <!--Add link to topic or blog post that explains managed disks. -->

Use Azure Portal o la CLI de Azure 2.0 para realizar una instantánea del disco administrado.

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Uso de la CLI de Azure 2.0 para realizar una instantánea

> [!NOTE] 
> En el ejemplo siguiente, es necesario que la CLI de Azure 2.0 esté instalada y que haya iniciado sesión en la cuenta de Azure.

En los pasos siguientes se explica cómo obtener y realizar una instantánea de un disco administrado del SO usando el comando `az snapshot create` con el parámetro `--source-disk`. En el siguiente ejemplo se supone que hay una VM denominada `myVM` creada con un disco administrado del SO en el grupo de recursos `myResourceGroup`.

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

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la parte superior izquierda, haga clic en **Nuevo** y busque **snapshot**.
3. En la hoja Instantánea, haga clic en **Crear**.
4. Escriba un **nombre** para la instantánea.
5. Seleccione un valor de [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno. 
6. Seleccione una ubicación del centro de datos de Azure.  
7. Como **disco de origen**, seleccione el disco administrado para la instantánea.
8. Seleccione el **tipo de cuenta** que se usará para almacenar la instantánea. Se recomienda **Standard_LRS**, a menos que necesite almacenarla en un disco de alto rendimiento.
9. Haga clic en **Create**(Crear).

Si tiene previsto utilizar la instantánea para crear un disco administrado y conectarle una VM que precisa de un alto rendimiento, use el parámetro `--sku Premium_LRS` con el comando `az snapshot create`. De esta forma, se crea la instantánea para que se almacene como un disco administrado premium. Los Managed Disks Premium tienen un rendimiento mayor porque son discos de estado sólido (SSD), pero cuestan más que los discos estándar (HDD).


