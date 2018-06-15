---
title: 'Ejemplo de PowerShell: equilibrio de carga entre varios sitios web con Azure PowerShell | Microsoft Docs'
description: En este ejemplo de script de Azure PowerShell se muestra cómo equilibrar la carga entre varios sitios web con la misma máquina virtual
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: f0058c8c4c4b170b12d2e888073aa0c731e87725
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181242"
---
# <a name="azure-powershell-script-example-load-balance-multiple-websites"></a>Ejemplo de script de Azure PowerShell: equilibrio de carga entre varios sitios web

En este ejemplo de script de Azure PowerShell se crea una red virtual con dos máquinas virtuales que son miembros de un conjunto de disponibilidad. Un equilibrador de carga dirige el tráfico de dos direcciones IP independientes a las dos máquinas virtuales. Después de ejecutar el script, podría implementar software de servidor web en las máquinas virtuales y hospedar varios sitios web, cada uno con su propia dirección IP.

Si es necesario, instale Azure PowerShell con la instrucción que se encuentra en la [Guía de Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) y, luego, ejecute `Login-AzureRmAccount` para crear una conexión con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una red virtual, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Crea un conjunto de disponibilidad de Azure para proporcionar alta disponibilidad. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crea una configuración de subred. Esta configuración se utiliza con el proceso de creación de la red virtual. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crea una red virtual. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea una dirección IP pública. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Crea una configuración de dirección IP de front-end para un equilibrador de carga. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Crea una configuración de grupo de direcciones de back-end para un equilibrador de carga. |
| [New-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Crea un sondeo de NLB. Se utiliza una prueba de NLB para supervisar cada máquina virtual en el conjunto de NLB. Si alguna máquina virtual deja de estar accesible, el tráfico no se enruta a la máquina virtual. |
| [New-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Crea una regla de NLB. En este ejemplo, se crea una regla para el puerto 80. Según va llegando el tráfico HTTP a NLB, se enruta al puerto 80 de una de las máquinas virtuales del conjunto de NLB. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Crea un equilibrador de carga. |
| [New-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/new-azurermnetworkinterfaceipconfig) | Define las características avanzadas para una interfaz de red virtual. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crea una interfaz de red. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crea una configuración de máquina virtual. Esta configuración incluye diversa información, como el nombre de la máquina virtual, sistema el operativo y las credenciales administrativas. La configuración se utiliza durante la creación de las máquinas virtuales. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Cree una máquina virtual. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Quita un grupo de recursos y todos los recursos incluidos en él. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

En la [documentación de la información general de redes de Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json) puede encontrar ejemplos adicionales de script de PowerShell de redes.
