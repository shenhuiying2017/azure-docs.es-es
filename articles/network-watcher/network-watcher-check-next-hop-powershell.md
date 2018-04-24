---
title: "Búsqueda del próximo salto con Próximo salto de Azure Network Watcher (PowerShell) | Microsoft Docs"
description: "En este artículo se describe cómo encontrar el tipo del próximo salto y la dirección IP mediante la funcionalidad Próximo salto con PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Obtenga más información sobre el tipo del próximo salto con la funcionalidad Próximo salto de Azure Network Watcher mediante PowerShell.

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API de REST de Azure](network-watcher-check-next-hop-rest.md)

Próximo salto es una característica de Network Watcher que permite obtener el tipo del próximo salto y la dirección IP para una máquina virtual especificada. Esta característica es útil para determinar si el tráfico que sale de una máquina virtual atraviesa una puerta de enlace, Internet o redes virtuales para llegar a su destino.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se usa Azure Portal para averiguar el tipo de próximo salto y la dirección IP.

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo usa Next Hop, una característica de Network Watcher, para averiguar el tipo de próximo salto y la dirección IP de un recurso. Para más información sobre Próximo salto, vea [introducción a Próximo salto](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Recuperación de Network Watcher

El primer paso consiste en recuperar la instancia de Network Watcher. La variable `$networkWatcher` pasa al cmdlet de verificación del próximo salto.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Obtención de una máquina virtual

Próximo salto devuelve el próximo salto y la dirección IP del próximo salto de una máquina virtual. Se necesita el identificador de una máquina virtual para ejecutar el cmdlet. Si ya conoce el identificador de la máquina virtual que se va a usar, puede omitir este paso.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Un requisito del próximo salto es que el recurso de máquina virtual esté asignado para ejecutarse.

## <a name="get-the-network-interfaces"></a>Obtención de las interfaces de red

Se necesita la dirección IP de una NIC en la máquina virtual; en este ejemplo, se recuperan las NIC de una máquina virtual. Si ya conoce la dirección IP que desea probar en la máquina virtual, puede omitir este paso.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Obtención del próximo salto

Ahora se llama al cmdlet `Get-AzureRmNetworkWatcherNextHop`. Se pasa al cmdlet la instancia de Network Watcher, el identificador de la máquina virtual, la dirección IP de origen y la dirección IP de destino. En este ejemplo, la dirección IP de destino es una máquina virtual en otra red virtual. Hay una puerta de enlace de red virtual entre las dos redes virtuales.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Revisión de los resultados

Una vez finalizado, se proporcionan los resultados. Se devuelve la dirección IP del próximo salto y el tipo de recurso que es. En este escenario, es la dirección IP pública de la puerta de enlace de red virtual.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

En la lista siguiente se muestran los valores de NextHopType disponibles actualmente:

**Tipo de próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>Pasos siguientes

Aprenda cómo revisar la configuración del grupo de seguridad de red mediante programación en [NSG Auditing with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Auditoría de NSG con Network Watcher).

















