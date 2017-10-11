---
title: "Mover una máquina virtual (clásica) o una instancia de rol de Cloud Services a una subred distinta (Azure PowerShell) | Microsoft Docs"
description: "Obtenga información sobre cómo mover máquinas virtuales (clásicas) e instancias de rol de Cloud Services a una subred diferente mediante PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b094f8338394ef2e84cad3070936d715411326a4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Mover una máquina virtual (clásica) o una instancia de rol de Cloud Services a una subred diferente mediante PowerShell
Puede usar PowerShell para mover las máquinas virtuales (clásicas) de una subred a otra en la misma red virtual (VNet). Las instancias de rol pueden moverse si se edita el archivo CSCFG en lugar de utilizar PowerShell.

> [!NOTE]
> En este artículo se explica cómo mover máquinas virtuales implementadas solo a través del modelo de implementación clásico.
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
