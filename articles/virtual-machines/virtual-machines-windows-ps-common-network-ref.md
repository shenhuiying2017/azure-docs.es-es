<properties
	pageTitle="Comandos de red comunes de PowerShell para máquinas virtuales | Microsoft Azure"
	description="Comandos de red comunes de PowerShell para empezar a crear una red virtual y sus recursos asociados para máquinas virtuales."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="davidmu"/>

# Comandos de red comunes de Azure PowerShell para máquinas virtuales

Si desea crear una máquina virtual, debe crear una [red virtual](../virtual-network/virtual-networks-overview.md) o conocer una red virtual existente en la que pueda agregar la máquina virtual. Normalmente, cuando se crea una máquina virtual, también hay que plantearse crear los recursos que se describen en este artículo.

## Creación de recursos con Azure PowerShell

Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar la versión más reciente de Azure PowerShell, seleccionar la suscripción que quiere usar e iniciar sesión en su cuenta de Azure.

Recurso | Comando 
-------------- | -------------------------
Subred | $internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>Una red típica podría tener una subred para un [equilibrador de carga accesible desde Internet](../load-balancer/load-balancer-internet-overview.md) y una independiente para un [equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md). |
Red virtual | Creación de una red virtual:<BR><BR>$rgName = "[nombre-grupo-recursos](../powershell-azure-resource-manager.md)"<BR>$locName = "[nombre-ubicación](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "nombre-red-virtual"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>Obtención de una lista de redes virtuales:<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName &#124; Sort Name &#124; Select Name<BR><BR>Enumeración de las subredes de una red virtual:<BR><BR>Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName &#124; Select Subnets<BR><BR>Debe ver un mensaje similar a este que enumera las subredes:<BR><BR>Subredes<BR>-------<BR>{internetSubnet, internalSubnet}<BR><BR>El índice de subred de internetSubnet es 0 y el de internalSubnet, 1.
Etiqueta de nombre de dominio | $domName = "domain-name"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>Puede especificar una etiqueta de nombre de dominio DNS para un [recurso de IP pública](../virtual-network/virtual-network-ip-addresses-overview-arm.md), lo que crea una asignación para etiquetadenombrededominio.ubicación.cloudapp.azure.com a la dirección IP pública de los servidores DNS que administra Azure. La etiqueta solo puede contener letras, números y guiones. El primer y último carácter debe ser una letra o un número, y la etiqueta de nombre de dominio debe ser única dentro de su ubicación de Azure. Siempre debe probar si la etiqueta de nombre de dominio es única globalmente. Si se devuelve **True**, significa que el nombre propuesto es único globalmente.
Dirección IP pública | $ipName = "nombre-dirección-IP-pública"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>La dirección IP pública utiliza la etiqueta de nombre de dominio que creó anteriormente y se emplea la configuración de front-end del equilibrador de carga.
Configuración de direcciones IP de front-end | $feConfigName = "nombre-config-ip-front-end"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>La configuración de front-end incluye la dirección IP pública que creó anteriormente para el tráfico de red entrante.
Grupo de direcciones de back-end | $bePoolName = "nombre-grupo-back-end"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>Proporciona direcciones internas para el back-end del equilibrador de carga al que se accede mediante una interfaz de red.
Sondeo | $probeName = "nombre-sonda-estado"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contiene las sondas de estado utilizadas para comprobar la disponibilidad de las instancias de máquina virtual del grupo de direcciones de back-end.
Regla de equilibrio de carga | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contiene reglas de asignación de un puerto público del equilibrador de carga a un puerto del grupo de direcciones de back-end.
Regla NAT de entrada | $ruleName = "nombre-regla-NAT"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contiene reglas de asignación de un puerto público del equilibrador de carga a un puerto de una máquina virtual específica del grupo de direcciones de back-end.
Equilibrador de carga | $lbName = "nombre-equilibrador-carga"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe
Interfaz de red | $vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "network-interface-name"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Cree una interfaz de red empleando la dirección IP pública y la subred de red virtual que creó anteriormente.
	
## Pasos siguientes

- Utilice la interfaz de red que acaba de crear cuando [cree una máquina virtual](virtual-machines-windows-ps-create.md).
- Obtenga información sobre cómo puede [crear una máquina virtual con varias interfaces de red](../virtual-network/virtual-networks-multiple-nics.md).

<!---HONumber=AcomDC_0629_2016-->