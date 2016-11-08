---
title: Mover una máquina virtual Linux | Microsoft Docs
description: Mueva una máquina virtual Linux a otro grupo de recursos o suscripción de Azure en el modelo de implementación de Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cynthn

---
# Traslado de una máquina virtual Linux a otra suscripción o grupo de recursos
Este artículo le guiará en el procedimiento para mover una máquina virtual Linux entre suscripciones o grupos de recursos. Mover una máquina virtual entre suscripciones puede ser útil si ha creado una máquina virtual en una suscripción personal y ahora quiere moverla a la suscripción de su compañía.

> [!NOTE]
> Como parte de esta operación, se crean nuevos identificadores de recurso. Cuando la máquina virtual se ha movido, debe actualizar sus herramientas y scripts para usar los nuevos identificadores de recursos.
> 
> 

## Usar la CLI de Azure para mover una máquina virtual
Para mover correctamente una máquina virtual, debe mover la máquina virtual y los recursos de apoyo. Use el comando **azure group show** para enumerar todos los recursos de un grupo de recursos y sus identificadores. Ayuda a canalizar la salida de este comando a un archivo para que pueda copiar y pegar los identificadores en comandos posteriores.

    azure group show <resourceGroupName>

Para mover una máquina virtual y sus recursos a otro grupo de recursos, use el comando de CLI **azure resource move**. En el ejemplo siguiente se muestra cómo mover una máquina virtual y los recursos más comunes que necesita. Usamos el parámetro **-i** que pasa una lista separada por comas (sin espacios) de identificadores de los recursos que se van a mover.

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

Si quiere mover la máquina virtual y sus recursos a una suscripción diferente, agregue el parámetro **--destination-subscriptionId &#60;destinationSubscriptionID&#62;** para especificar la suscripción de destino.

Si está trabajando desde el símbolo del sistema en un equipo Windows, debe agregar **$** delante de los nombres de variable al declararlas. Esto no es necesario en Linux.

Se le pedirá que confirme que quiere mover el recurso especificado. Escriba **Y** para confirmar que quiere mover los recursos.

[!INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## Pasos siguientes
Puede mover muchos tipos diferentes de recursos entre suscripciones y grupos de recursos. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->