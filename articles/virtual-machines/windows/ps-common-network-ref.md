---
title: Comandos comunes de PowerShell para redes virtuales de Azure | Microsoft Docs
description: "Comandos de red comunes de PowerShell para empezar a crear una red virtual y sus recursos asociados para máquinas virtuales."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 41a26e8b13fdb2531924ba441017c4c01e664995
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Comandos comunes de PowerShell para redes virtuales de Azure

Si desea crear una máquina virtual, debe crear una [red virtual](../../virtual-network/virtual-networks-overview.md) o conocer una red virtual existente en la que pueda agregar la máquina virtual. Normalmente, cuando se crea una máquina virtual, también hay que plantearse crear los recursos que se describen en este artículo.

Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview) para obtener más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta.

Algunas de las variables podrían serle útiles si ejecuta más de uno de los comandos de este artículo:

- $location: la ubicación de los recursos de red. Puede usar [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) para buscar una [región geográfica](https://azure.microsoft.com/regions/) que se adapte a sus necesidades.
- $myResourceGroup: el nombre del grupo de recursos donde se encuentran los recursos de red.

## <a name="create-network-resources"></a>Crear recursos de red

| Tarea | Comando |
| ---- | ------- |
| Crear configuraciones de subred |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Una red típica podría tener una subred para un [equilibrador de carga accesible desde Internet](../../load-balancer/load-balancer-internet-overview.md) y una subred independiente para un [equilibrador de carga interno](../../load-balancer/load-balancer-internal-overview.md). |
| Crear una red virtual |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Prueba para un nombre de dominio único |[Test-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Puede especificar un nombre de dominio DNS para un [recurso de dirección IP pública](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), lo que crea una asignación para nombrededominio.ubicación.cloudapp.azure.com en la dirección IP pública de los servidores DNS que administra Azure. El nombre solo puede contener letras, números y guiones. El primer y último carácter debe ser una letra o un número y el nombre de dominio debe ser único dentro de su ubicación de Azure. Si se devuelve **True** , significa que el nombre propuesto es único globalmente. |
| Crear una dirección IP pública |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>La dirección IP pública utiliza el nombre de dominio que probó anteriormente y la emplea la configuración de front-end del equilibrador de carga. |
| Crear una configuración de direcciones IP de front-end |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>La configuración de front-end incluye la dirección IP pública que creó anteriormente para el tráfico de red entrante. |
| Crear un grupo de direcciones de back-end |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Proporciona direcciones internas para el back-end del equilibrador de carga al que se accede mediante una interfaz de red. |
| Elaboración de un sondeo |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contiene las sondas de estado utilizadas para comprobar la disponibilidad de las instancias de máquina virtual del grupo de direcciones de back-end. |
| Crear una regla de equilibrio de carga |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contiene reglas que asignan un puerto público del equilibrador de carga a un puerto del grupo de direcciones de back-end. |
| Crear una regla NAT entrante |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contiene reglas de asignación de un puerto público del equilibrador de carga a un puerto de una máquina virtual específica del grupo de direcciones de back-end. |
| Crear un equilibrador de carga |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Crear una interfaz de red |$nic1= [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Cree una interfaz de red empleando la dirección IP pública y la subred de red virtual que creó anteriormente. |

## <a name="get-information-about-network-resources"></a>Obtención de información sobre recursos de red

| Tarea | Comando |
| ---- | ------- |
| Enumerar redes virtuales |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Enumera todas las redes virtuales del grupo de recursos. |
| Obtener información sobre una red virtual |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Enumerar subredes de una red virtual |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Obtener información sobre una subred |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Obtiene información sobre la subred de la red virtual especificada. El valor $vnet representa el objeto devuelto por Get-AzureRmVirtualNetwork. |
| Enumerar direcciones IP |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Enumera las direcciones IP públicas del grupo de recursos. |
| Enumerar equilibradores de carga |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Enumera todos los equilibradores de carga del grupo de recursos. |
| Enumerar interfaces de red |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Enumera todas las interfaces de red del grupo de recursos. |
| Obtener información sobre una interfaz de red |Get-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Obtiene información sobre una interfaz de red específica. |
| Obtener la configuración de dirección IP de una interfaz de red |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Obtiene información sobre la configuración de dirección IP de la interfaz de red especificada. El valor $nic representa el objeto devuelto por Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Administración de recursos de red

| Tarea | Comando |
| ---- | ------- |
| Agregar una red a una red virtual |[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Agrega una subred a una red virtual existente. El valor $vnet representa el objeto devuelto por Get-AzureRmVirtualNetwork. |
| Eliminar una red virtual |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Quita la red virtual especificada del grupo de recursos. |
| Eliminar una interfaz de red |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Quita la interfaz de red especificada del grupo de recursos. |
| Eliminar un equilibrador de carga |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" - ResourceGroupName $myResourceGroup<BR><BR>Quita el equilibrador de carga especificado del grupo de recursos. |
| Eliminar una dirección IP pública |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Quita la dirección IP pública especificada del grupo de recursos. |

## <a name="next-steps"></a>Pasos siguientes
* Utilice la interfaz de red que acaba de crear cuando [cree una VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Obtenga información sobre cómo puede [crear una máquina virtual con varias interfaces de red](../../virtual-network/virtual-networks-multiple-nics.md).

