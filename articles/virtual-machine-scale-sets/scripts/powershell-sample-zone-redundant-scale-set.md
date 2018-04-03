---
title: 'Ejemplos de Azure PowerShell: Conjunto de escalado con redundancia de zona | Microsoft Docs'
description: Ejemplos de Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b121684b3b9a5d03fe89e0892179140e9f5de80f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>Creación de un conjunto de escalado de máquinas virtuales con redundancia de zona con PowerShell
Este script crea un conjunto de escalado de máquinas virtuales que ejecutan Windows Server 2016 en varias zonas de disponibilidad. Después de ejecutar el script, puede acceder a la máquina virtual a través de RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Ejecute el siguiente comando para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos para crear la implementación. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crea un objeto de configuración que define la subred de red virtual. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crea una red virtual y una subred. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea una dirección IP pública estática. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Crea un objeto de configuración que define el servidor front-end de equilibrador de carga, incluyendo la dirección IP pública. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Crea un objeto de configuración que define el servidor back-end de equilibrador de carga. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Crea un equilibrador de carga basado en los objetos de configuración de servidores front-end y back-end. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Crea un sondeo de estado del equilibrador de carga para supervisar el tráfico en el puerto TCP 80. Si se encuentran dos errores consecutivos en intervalos de 15 segundos, el punto de conexión se considera incorrecto. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Crea un objeto de configuración que define las reglas del equilibrador de carga para distribuir el tráfico en el puerto TCP 80 desde el grupo de servidores front-end hacia el grupo de servidores back-end. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Actualiza el equilibrador de carga con el estado de sondeo y las reglas del equilibrador de carga. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Crea un objeto de configuración que define una regla de grupo de seguridad de red para permitir el tráfico en el puerto TCP 80. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Crea una regla y un grupo de seguridad de red. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Actualiza la subred de la red virtual que se asocia con el grupo de seguridad de red. Las instancias de máquina virtual conectadas a la subred heredan las reglas del grupo de seguridad de red. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Actualiza la red virtual con la configuración de la subred y el grupo de seguridad de red. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Crea un objeto de configuración que define la información de las direcciones IP de máquina virtual del conjunto de escalado de máquinas virtuales para conectar las instancias de máquina virtual al grupo de servidores back-end de equilibradores de carga y al grupo de NAT de entrada.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Crea un objeto de configuración que define el número de instancias de máquina virtual en un conjunto de escalado, el tamaño de las instancias de máquina virtual y el modo de la directiva de actualización. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Crea un objeto de configuración que define la imagen de máquina virtual que se utilizará para las instancias de máquina virtual. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Crea un perfil de configuración que define las credenciales de usuario y el nombre de la instancia de máquina virtual. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Crea un objeto de configuración que define la tarjeta de interfaz de red virtual para cada instancia de máquina virtual. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Combina todos los objetos de configuración para crear el conjunto de escalado de máquinas virtuales. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Obtiene información sobre un conjunto de escalado de máquinas virtuales. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Agrega una extensión de máquina virtual para script personalizado para instalar una aplicación web básica. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Actualiza el modelo de conjunto de escalado de máquinas virtuales para aplicar la extensión de máquina virtual. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Obtiene información sobre la dirección IP pública asignada que usa el equilibrador de carga. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Quita un grupo de recursos y todos los recursos incluidos en él. |


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Se pueden encontrar otros ejemplos de scripts de PowerShell de conjunto de escalado de máquinas virtuales en la [documentación del conjunto de escalado de máquinas virtuales de Azure](../powershell-samples.md).