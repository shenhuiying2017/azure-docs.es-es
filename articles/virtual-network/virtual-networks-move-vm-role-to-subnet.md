---
title: "Traslado de una máquina virtual o una instancia de rol a una subred diferente"
description: "Obtenga información sobre cómo mover las máquinas virtuales y las instancias de rol a una subred diferente"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 264e02fa48486acd7a9701c497c4e1fa95a1ce4e


---
# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Traslado de una máquina virtual o una instancia de rol a una subred diferente
Puede usar PowerShell para mover las máquinas virtuales de un subred a otra en la misma red virtual. Para mover instancias de rol, puede editar el archivo CSCFG en lugar de usar PowerShell.

> [!NOTE]
> Este artículo contiene información relativa a implementaciones clásicas de Azure únicamente.
> 
> 

¿Por qué mover máquinas virtuales a otra subred? La migración de subred es útil cuando la antigua subred se queda demasiado pequeña y no se puede expandir debido a que existen máquinas virtuales en ejecución en esa subred. En ese caso, puede crear una subred más grande y migrar las máquinas virtuales a esa nueva subred; a continuación, una vez finalizada la migración, puede eliminar la subred antigua vacía.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Cómo mover una máquina virtual a otra subred
Para mover una máquina virtual, ejecute el cmdlet de PowerShell Set-AzureSubnet, utilizando el ejemplo siguiente como plantilla. En este ejemplo, movemos TestVM (máquina virtual de prueba) desde la subred actual a Subnet-2 (subred 2). Asegúrese de editar el ejemplo para reflejar su entorno. Tenga en cuenta que siempre que ejecute el cmdlet Update-AzureVM como parte de un procedimiento, se reiniciará la máquina virtual como parte del proceso de actualización.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Si especificó una dirección IP privada interna estática para la máquina virtual, tendrá que borrar esa configuración para poder mover la máquina virtual a una nueva subred. En ese caso, utilice lo siguiente:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Para mover una instancia de rol a otra subred
Para mover una instancia de rol, edite el archivo CSCFG. En este ejemplo, se mueve "Role0" (rol 0) en la red virtual *VNETName* de la subred actual a *Subnet-2* (subred 2). Como ya se ha implementado la instancia de rol, solo tendrá que cambiar el nombre de la subred = Subnet-2 (subred 2). Asegúrese de editar el ejemplo para reflejar su entorno.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 



<!--HONumber=Nov16_HO3-->


