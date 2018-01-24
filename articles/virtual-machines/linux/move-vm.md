---
title: Movimiento de una VM de Linux en Azure | Microsoft Docs
description: "Mueva una máquina virtual Linux a otro grupo de recursos o suscripción de Azure en el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: 459e0d591e2279b63864a273f713e4c1df8c0858
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Traslado de una máquina virtual Linux a otra suscripción o grupo de recursos
Este artículo le guiará en el procedimiento para mover una máquina virtual Linux entre suscripciones o grupos de recursos. Mover una máquina virtual entre suscripciones puede ser útil si ha creado una máquina virtual en una suscripción personal y ahora quiere moverla a la suscripción de su compañía.

> [!IMPORTANT]
>No se puede mover discos administrados en este momento. 
>
>Como parte de esta operación, se crean nuevos identificadores de recurso. Cuando la máquina virtual se ha movido, debe actualizar sus herramientas y scripts para usar los nuevos identificadores de recursos. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Usar la CLI de Azure para mover una máquina virtual


Antes de poder mover la máquina virtual mediante la CLI, debe asegurarse de que las suscripciones de origen y de destino existen en el mismo inquilino. Para comprobar que ambas suscripciones tienen el mismo identificador de inquilino, use [az account show](/cli/azure/account#az_account_show).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Si los identificadores de inquilino para las suscripciones de origen y destino no son los mismos, debe ponerse en contacto con [soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para mover los recursos a un nuevo inquilino.

Para mover correctamente una máquina virtual, debe mover la máquina virtual y los recursos de apoyo. Use el comando [az resource list](/cli/azure/resource#az_resource_list) para mostrar todos los recursos del grupo de recursos y sus identificadores. Ayuda a canalizar la salida de este comando a un archivo para que pueda copiar y pegar los identificadores en comandos posteriores.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Para mover una máquina virtual y sus recursos a otro grupo de recursos, use [az resource move](/cli/azure/resource#az_resource_move). En el ejemplo siguiente se muestra cómo mover una máquina virtual y los recursos más comunes que necesita. Use el parámetro **-ids** y pase una lista separada por comas (sin espacios) de identificadores de los recursos que se van a mover.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageacountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Si quiere mover la máquina virtual y sus recursos a una suscripción diferente, agregue el parámetro **--destination-subscriptionId** para especificar la suscripción de destino.

Se le pedirá que confirme que quiere mover el recurso especificado. Escriba **Y** para confirmar que quiere mover los recursos.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>pasos siguientes
Puede mover muchos tipos diferentes de recursos entre suscripciones y grupos de recursos. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../resource-group-move-resources.md).    

