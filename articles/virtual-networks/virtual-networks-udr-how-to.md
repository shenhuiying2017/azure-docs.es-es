<properties 
   pageTitle="Creación de rutas y habilitación del reenvío de IP en Azure"
   description="Creación de rutas y habilitación del reenvío de IP en Azure"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 

<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Creación de rutas y habilitación del reenvío de IP en Azure
Puede utilizar aplicaciones virtuales de Azure para controlar el tráfico de la red virtual de Azure. Sin embargo, es preciso crear rutas que permitan que las máquinas virtuales y servicios en la nube de la red virtual envíen paquetes a una aplicación virtual, en lugar de al destino deseado para el paquete. También es preciso habilitar el reenvío IP en la máquina virtual del dispositivo virtual para que pueda recibir y reenviar los paquetes que no vayan dirigidos a la máquina virtual de la aplicación virtual real.

##Administración de rutas
En Azure puede agregar, quitar y cambiar las rutas mediante PowerShell. Para poder crear una ruta, antes es preciso crear una tabla de rutas que hospede la ruta.

### Creación de una tabla de rutas
Para crear una tabla de rutas denominada *FrontEndSubnetRouteTable*, ejecute el siguiente comando de PowerShell:

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### Adición de una ruta a una tabla de rutas
Para agregar una ruta que establezca *10.1.1.10* como próximo salto de la subred *10.2.0.0/16* en la tabla de rutas creada anteriormente, ejecute el siguiente comando de PowerShell:

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### Asociación de una ruta a una subred
Para poder usar una tabla de rutas, esta debe estar asociada con una o varias subredes. Para asociar la tabla de rutas *FrontEndSubnetRouteTable* a una subred denominada *FrontEndSubnet* de la red virtual *ProductionVnet*, ejecute el siguiente comando de PowerShell:

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### Vista de las rutas aplicadas en una máquina virtual
Puede consultar Azure para ver las rutas reales aplicadas a una instancia de máquina virtual o de rol específica. Las rutas que se muestran incluyen las rutas predeterminadas que proporciona Azure, así como las rutas anunciadas por una puerta de enlace de VPN. El límite de rutas que se muestran es 800.

Para ver las rutas asociadas a la NIC principal de una máquina virtual denominada *FirewallAppliance1*, ejecute el siguiente comando de PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

Para ver las rutas asociadas a una NIC secundaria denominada *backendnic* de una máquina virtual denominada *FirewallAppliance1*, ejecute el siguiente comando de PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

Para ver las rutas asociadas a la NIC principal en una instancia de rol denominada *myRole* que forma parte de un servicio en la nube denominado *myservice*, ejecute el siguiente comando de PowerShell:

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## Administración del reenvío IP
Como ya se ha mencionado, es preciso habilitar el reenvío IP en todas las instancias de máquina virtual o de rol que vayan a actuar como aplicaciones virtuales.

### Habilitación del reenvío IP
Para habilitar el reenvío IP en una máquina virtual denominada *FirewallAppliance1*, ejecute el siguiente comando de PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

Para habilitar el reenvío IP en una instancia de rol denominada *FirewallAppliance1* de un servicio en la nube denominado *myService*, ejecute el siguiente comando de PowerShell:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### Deshabilitación del reenvío IP
Para deshabilitar el reenvío IP en una máquina virtual denominada *FirewallAppliance1*, ejecute el siguiente comando de PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

Para deshabilitar el reenvío de IP en una instancia de rol denominada *FirewallAppliance1* de un servicio en la nube denominado *myService*, ejecute el siguiente comando de PowerShell:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### Vista del estado del reenvío IP
Para ver el estado del reenvío IP en una máquina virtual denominada *FirewallAppliance1*, ejecute el siguiente comando de PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## Otras referencias

[Información del reenvío IP y de las rutas definidas por el usuario](../virtual-networks-udr-overview)

[Dirección IP pública de nivel de instancia (ILIP)](../virtual-networks-instance-level-public-ip)

[Información general sobre redes virtuales](https://msdn.microsoft.com/library/azure/jj156007.aspx) <!--HONumber=52-->
 