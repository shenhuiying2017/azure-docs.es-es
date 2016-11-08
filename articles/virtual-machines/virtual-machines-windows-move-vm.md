---
title: Traslación de una máquina virtual Windows | Microsoft Docs
description: Mueva una máquina virtual Windows a otro grupo de recursos o suscripción de Azure en el modelo de implementación de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cynthn

---
# Movimiento de una máquina virtual Windows a otro grupo de recursos o suscripción de Azure
Este artículo le guiará en el procedimiento para mover una máquina virtual Windows entre suscripciones o grupos de recursos. Mover máquinas virtuales entre suscripciones puede ser útil si originalmente creó una en una suscripción personal y ahora quiere moverla a la suscripción de su compañía para seguir trabajando.

> [!NOTE]
> Como parte de esta operación, se crean nuevos id. de recurso. Cuando la máquina virtual se ha movido, debe actualizar sus herramientas y scripts para usar los nuevos id. de recursos.
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## Uso de PowerShell para mover una máquina virtual
Para mover una máquina virtual a otro grupo de recursos, debe asegurarse de trasladar todos los recursos dependientes. Para usar el cmdlet Move-AzureRMResource, necesita el nombre del recurso y el tipo. Puede obtener estos dos datos con el cmdlet Find-AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"


Para mover una máquina virtual, hay que trasladar varios recursos. Podemos crear variables independientes para cada recurso y, luego, enumerarlos. Este ejemplo incluye la mayoría de los recursos básicos de una máquina virtual, pero puede agregar más según sea necesario.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"

    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"

    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Para mover los recursos a otra suscripción, incluya el parámetro **-DestinationSubscriptionId**.

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Se le pedirá que confirme que desea mover los recursos especificados. Escriba **Y** para confirmar que quiere mover los recursos.

## Pasos siguientes
Puede mover muchos tipos diferentes de recursos entre suscripciones y grupos de recursos. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->