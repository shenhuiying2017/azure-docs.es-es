---
title: "Mover un recurso de máquina virtual Windows en Azure | Microsoft Docs"
description: "Mueva una máquina virtual Windows a otro grupo de recursos o suscripción de Azure en el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: cynthn
ms.openlocfilehash: f4b739fd34cc0c85d47b97b7b42a70eb7f5f5ac7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Movimiento de una máquina virtual Windows a otro grupo de recursos o suscripción de Azure
Este artículo le guiará en el procedimiento para mover una máquina virtual Windows entre suscripciones o grupos de recursos. Mover máquinas virtuales entre suscripciones puede ser útil si originalmente creó una en una suscripción personal y ahora quiere moverla a la suscripción de su compañía para seguir trabajando.

> [!IMPORTANT]
>No se puede mover discos administrados en este momento. 
>
>Como parte de esta operación, se crean nuevos identificadores de recurso. Cuando la máquina virtual se ha movido, debe actualizar sus herramientas y scripts para usar los nuevos identificadores de recursos. 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Uso de PowerShell para mover una máquina virtual

Para mover una máquina virtual a otro grupo de recursos, debe asegurarse de trasladar todos los recursos dependientes. Para usar el cmdlet Move-AzureRMResource, necesita el valor de ResourceId de cada uno de los recursos. Puede obtener una lista de los valores de ResourceId mediante el cmdlet [AzureRMResource buscar](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
 Find-AzureRMResource -ResourceGroupNameContains <sourceResourceGroupName> | Format-table -Property ResourceId 
```

Para mover una máquina virtual, hay que trasladar varios recursos. Se puede usar la salida de Find-AzureRMResource para crear una lista separada por comas de valores de ResourceId y pasar esa lista a [Move-AzureRMResource](/powershell/module/azurerm.resources/move-azurermresource) para moverlos hasta el destino. 

```azurepowershell-interactive

Move-AzureRmResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
Para mover los recursos a otra suscripción, incluya el parámetro **-DestinationSubscriptionId** . 

```azurepowershell-interactive
Move-AzureRmResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Se le pedirá que confirme que desea mover los recursos especificados. 

## <a name="next-steps"></a>pasos siguientes
Puede mover muchos tipos diferentes de recursos entre suscripciones y grupos de recursos. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../resource-group-move-resources.md).    

