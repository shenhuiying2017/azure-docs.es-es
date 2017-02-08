---
title: Comandos de red comunes de PowerShell para VM | Microsoft Docs
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
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandos de red comunes de Azure PowerShell para máquinas virtuales
Si desea crear una máquina virtual, debe crear una [red virtual](../virtual-network/virtual-networks-overview.md) o conocer una red virtual existente en la que pueda agregar la máquina virtual. Normalmente, cuando se crea una máquina virtual, también hay que plantearse crear los recursos que se describen en este artículo.

Consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que desea usar e iniciar sesión en su cuenta.

## <a name="create-network-resources"></a>Crear recursos de red
| Tarea | Comando |
| --- | --- |
| Crear configuraciones de subred |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "nombre_subred" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "nombre_subred" -AddressPrefix XX.X.X.X/XX<BR><BR>Una red típica podría tener una subred para un [equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-internet-overview.md) y una subred independiente para un [equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md). |
| Crear una red virtual |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "nombre_red_virtual" -ResourceGroupName "nombre_grupo_recursos" -Location "nombre_ubicación" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Prueba para un nombre de dominio único |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName "nombre_dominio"-ubicación "nombre_ubicación"<BR><BR>Puede especificar un nombre de dominio DNS para un [recurso de dirección IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md), lo que crea una asignación para nombrededominio.ubicación.cloudapp.azure.com en la dirección IP pública de los servidores DNS que administra Azure. El nombre solo puede contener letras, números y guiones. El primer y último carácter debe ser una letra o un número y el nombre de dominio debe ser único dentro de su ubicación de Azure. Si se devuelve **True** , significa que el nombre propuesto es único globalmente. |
| Crear una dirección IP pública |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "nombre_dirección_ip" -ResourceGroupName "nombre_grupo_recursos" -DomainNameLabel "nombre_dominio" -Location "nombre_ubicación" -AllocationMethod Dynamic<BR><BR>La dirección IP pública utiliza el nombre de dominio que probó anteriormente y la emplea la configuración de front-end del equilibrador de carga. |
| Crear una configuración de direcciones IP de front-end |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "nombre_dirección_io_front-end" - PublicIpAddress $pip<BR><BR>La configuración de front-end incluye la dirección IP pública que creó anteriormente para el tráfico de red entrante. |
| Crear un grupo de direcciones de back-end |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "grupo_direcciones_deback-end"<BR><BR>Proporciona direcciones internas para el back-end del equilibrador de carga al que se accede mediante una interfaz de red. |
| Elaboración de un sondeo |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "nombre_sondeo" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contiene las sondas de estado utilizadas para comprobar la disponibilidad de las instancias de máquina virtual del grupo de direcciones de back-end. |
| Crear una regla de equilibrio de carga |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contiene reglas que asignan un puerto público del equilibrador de carga a un puerto del grupo de direcciones de back-end. |
| Crear una regla NAT entrante |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "nombre_regla" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contiene reglas de asignación de un puerto público del equilibrador de carga a un puerto de una máquina virtual específica del grupo de direcciones de back-end. |
| Crear un equilibrador de carga |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "nombre_equilibrador_carga" -Location "nombre_ubicación" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Crear una interfaz de red |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "nombre_grupo_recursos" -Name "network_interface_name" -Location "nombre_ubicación" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Cree una interfaz de red empleando la dirección IP pública y la subred de red virtual que creó anteriormente. |

## <a name="get-information-about-network-resources"></a>Obtención de información sobre recursos de red
| Tarea | Comando |
| --- | --- |
| Enumerar redes virtuales |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Enumera todas las redes virtuales del grupo de recursos. |
| Obtener información sobre una red virtual |Get-AzureRmVirtualNetwork -Name "nombre_red_virtual" -ResourceGroupName "nombre_grupo_recursos" |
| Enumerar subredes de una red virtual |Get-AzureRmVirtualNetwork -Name "nombre_red_virtual" -ResourceGroupName "nombre_grupo_recursos" &#124; Select Subnets |
| Obtener información sobre una subred |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "nombre_subred" -VirtualNetwork $vnet<BR><BR>Obtiene información sobre la subred de la red virtual especificada. El valor $vnet representa el objeto devuelto por Get-AzureRmVirtualNetwork. |
| Enumerar direcciones IP |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Enumera las direcciones IP públicas del grupo de recursos. |
| Enumerar equilibradores de carga |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Enumera todos los equilibradores de carga del grupo de recursos. |
| Enumerar interfaces de red |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Enumera todas las interfaces de red del grupo de recursos. |
| Obtener información sobre una interfaz de red |Get-AzureRmNetworkInterface -Name "nombre_interfaz_red" -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Obtiene información sobre una interfaz de red específica. |
| Obtener la configuración de dirección IP de una interfaz de red |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "nombre_configuración_dirección_ip" -NetworkInterface $nic<BR><BR>Obtiene información sobre la configuración de dirección IP de la interfaz de red especificada. El valor $nic representa el objeto devuelto por Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Administración de recursos de red
| Tarea | Comando |
| --- | --- |
| Agregar una red a una red virtual |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "nombre_subred" -VirtualNetwork $vnet<BR><BR>Agrega una subred a una red virtual existente. El valor $vnet representa el objeto devuelto por Get-AzureRmVirtualNetwork. |
| Eliminar una red virtual |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "nombre_red_virtual" -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Quita la red virtual especificada del grupo de recursos. |
| Eliminar una interfaz de red |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "nombre_interfaz_red" -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Quita la interfaz de red especificada del grupo de recursos. |
| Eliminar un equilibrador de carga |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "nombre_equilibrador de carga" -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Quita el equilibrador de carga especificado del grupo de recursos. |
| Eliminar una dirección IP pública |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "nombre_dirección_ip" -ResourceGroupName "nombre_grupo_recursos"<BR><BR>Quita la dirección IP pública especificada del grupo de recursos. |

## <a name="next-steps"></a>Pasos siguientes
* Utilice la interfaz de red que acaba de crear cuando [cree una VM](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Obtenga información sobre cómo puede [crear una máquina virtual con varias interfaces de red](../virtual-network/virtual-networks-multiple-nics.md).




<!--HONumber=Dec16_HO2-->


