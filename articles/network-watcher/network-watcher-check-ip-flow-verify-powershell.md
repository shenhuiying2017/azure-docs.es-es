---
title: "Comprobación del tráfico con la Comprobación del flujo de IP de Azure Network Watcher con PowerShell | Microsoft Docs"
description: "En este artículo se describe cómo comprobar si se permite o se deniega el tráfico hacia o desde una máquina virtual mediante PowerShell"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 5257a70aa2dbc25bfe4eca5e2e0db87ca5e6b6fe
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Comprobar si se permite o se deniega el tráfico hacia o desde una máquina virtual con la Comprobación del flujo de IP, un componente de Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal de Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API de REST de Azure](network-watcher-check-ip-flow-verify-rest.md)


La Comprobación del flujo de IP es una característica de Network Watcher que permite comprobar si se permite el tráfico hacia o desde una máquina virtual. Este escenario es útil para averiguar si una máquina virtual puede comunicarse con un recurso externo o con un back-end. Comprobación del flujo de IP se puede usar para comprobar si las reglas de grupos de seguridad de red (NSG) se han configurado correctamente y solucionar los problemas de flujos que las reglas de NSG bloquean. Otra razón para usar la Comprobación del flujo de IP es asegurarse de que el NSG está bloqueando correctamente el tráfico que quiere bloquear.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create a Network Watcher](network-watcher-create.md) (Creación de una instancia de Network Watcher) o que ya tiene una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="scenario"></a>Escenario

Este escenario usa la comprobación del flujo de IP para comprobar si una máquina virtual pueden comunicarse con una dirección IP de Bing conocida. Si se deniega el tráfico, devuelve la regla de seguridad que está denegando ese tráfico. Para más información sobre la Comprobación del flujo de IP, vea [Introducción a la comprobación del flujo de IP](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Recuperación de Network Watcher

El primer paso consiste en recuperar la instancia de Network Watcher. La variable `$networkWatcher` pasa al cmdlet Comprobación del flujo de IP.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obtención de una máquina virtual

La Comprobación del flujo de IP comprueba el tráfico hacia o desde la dirección IP de una máquina virtual, hacia o desde un destino remoto. Se necesita el identificador de una máquina virtual para ejecutar el cmdlet. Si ya conoce el identificador de la máquina virtual que se va a usar, puede omitir este paso.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Obtención de las NIC

Se necesita la dirección IP de una NIC en la máquina virtual; en este ejemplo, se recuperan las NIC de una máquina virtual. Si ya conoce la dirección IP que desea probar en la máquina virtual, puede omitir este paso.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Ejecución de la Comprobación del flujo de IP

Ahora que tenemos la información necesaria para ejecutar el cmdlet `Test-AzureRmNetworkWatcherIPFlow`, lo ejecutamos para comprobar el tráfico. En este ejemplo, usamos la primera dirección IP en la primera NIC.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> La comprobación del flujo de IP requiere que el recurso de máquina virtual esté asignado para ejecución.

## <a name="review-results"></a>Revisión de los resultados

Después de ejecutar `Test-AzureRmNetworkWatcherIPFlow`, se devuelven los resultados. Los siguientes son los resultados devueltos por el paso anterior.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Pasos siguientes

Si se está bloqueando el tráfico y no debería ser así, vea [Administración de grupos de seguridad de red](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento de las reglas del grupo de seguridad de red y de seguridad definidas.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













