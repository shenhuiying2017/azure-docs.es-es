<properties 
   pageTitle="Creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. Si está buscando el modelo de implementación clásica de Azure, vaya a [Introducción a la creación de un equilibrador de carga orientado a Internet mediante la implementación clásica](load-balancer-get-started-internet-classic-portal.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Los pasos siguientes muestran cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure con PowerShell. Con el Administrador de recursos de Azure, los elementos para crear un equilibrador de carga orientado a Internet se configuran individualmente y después se colocan juntos para crear un recurso.

Esta página describiremos la secuencia de tareas individuales que debe realizarse para crear un equilibrador de carga y explicaremos con detalle cada una de ellas.

## ¿Qué se necesita para crear un equilibrador de carga orientado a Internet?

Para implementar un equilibrador de carga, debe crear y configurar los objetos siguientes:

- Configuración de direcciones IP front-end: contiene direcciones IP públicas para el tráfico de red entrante. 

- Grupo de direcciones de back-end: contiene interfaces de red (NIC) para recibir tráfico proveniente del equilibrador de carga.

- Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga a puertos en las NIC en el grupo de direcciones de back-end.

- Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto en una NIC individual en el grupo de direcciones de back-end.

- Sondeos: contiene los sondeos de estado que se utilizan para comprobar la disponibilidad de las máquinas virtuales vinculadas a las NIC en el grupo de direcciones de back-end.

Puede obtener más información acerca de los componentes del equilibrador de carga con el Administrador de recursos de Azure en [Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga](load-balancer-arm.md).


## Configurar PowerShell para que use el Administrador de recursos
Asegúrese de contar con la versión de producción más reciente del módulo de Azure para PowerShell y de tener configurado correctamente PowerShell para obtener acceso a su suscripción a Azure.

### Paso 1

1. Si es la primera vez que usa Azure PowerShell, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
2. En un símbolo del sistema de Azure PowerShell, ejecute el cmdlet **Switch-AzureMode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		Switch-AzureMode AzureResourceManager
	
	Resultado esperado:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.


>[AZURE.WARNING]El cmdlet Switch-AzureMode pronto estará en desuso. Cuando esto suceda, se cambiará el nombre de todos los cmdlets del Administrador de recursos.



### Paso 2

Inicio de sesión en la cuenta de Azure


    PS C:\> Add-AzureAccount

Se le pedirá autenticarse con sus credenciales.


### Paso 3

Elección de la suscripción de Azure que se va a usar.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver una lista de suscripciones disponibles, use el cmdlet 'Get-AzureSubscription'.

## Crear un grupo de recursos

Cree un grupo de recursos nuevo llamado *NRP-RG* en la ubicación *Oeste de EE. UU.* de Azure.

    PS C:\> New-AzureResourceGroup -Name NRP-RG -location "West US"

## Crear una red virtual y una dirección IP pública para el grupo de direcciones IP front-end

### Paso 1

Cree una subred y una red virtual.

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Paso 2

Cree una dirección IP pública (PIP) llamada *PublicIP* para que la use un grupo de direcciones IP front-end con el nombre DNS *loadbalancernrp.westus.cloudapp.azure.com*. El comando siguiente usa el tipo de asignación estática.

	$publicIP = New-AzurePublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]El equilibrador de carga usará la etiqueta de dominio de la dirección IP pública como su FQDN. Esto representa un cambio en el modelo de implementación clásica, que usa el servicio en la nube como el FQDN del equilibrador de carga. En este ejemplo, el FQDN será *loadbalancernrp.westus.cloudapp.azure.com*.

## Crear un grupo de direcciones IP front-end y un grupo de direcciones de back-end

### Paso 1 

Cree un grupo de direcciones IP front-end llamado *LB-Frontend* que use la dirección IP pública *PublicIp*.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Paso 2 

Cree un grupo de direcciones de back-end llamado *LB-backend*.

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## Crear reglas de equilibrador de carga, reglas NAT, un sondeo y un equilibrador de carga

En el ejemplo que aparece a continuación, se crean los elementos siguientes:

- Una regla NAT para trasladar todo el tráfico entrante del puerto 3441 al puerto 3389<sup>1</sup>.
- Una regla NAT para trasladar todo el tráfico entrante del puerto 3442 al puerto 3389.
- Una regla de equilibrador de carga para equilibrar todo el tráfico entrante del puerto 80 al puerto 80 en las direcciones del grupo de back-end.
- Una regla de sondeo que comprobará el estado de mantenimiento en una página llamada *HealthProbe.aspx*.
- Un equilibrador de carga que usa todos los objetos anteriores.


<sup>1</sup> Las reglas NAT están asociadas a una instancia de máquina virtual específica detrás del equilibrador de carga. En el ejemplo siguiente, el tráfico de red entrante al puerto 3341 se enviará a una máquina virtual específica en el puerto 3389 asociada a una regla NAT. Debe elegir un protocolo para la regla NAT, UDP o TCP. Los dos protocolos no se pueden asignar al mismo puerto.

### Paso 1

Cree las reglas NAT.

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Paso 2

Cree una regla de equilibrador de carga.

	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Paso 3

Cree un sondeo de estado.

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Paso 4

Cree el equilibrador de carga mediante los objetos creados anteriormente.

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## Cree tarjetas NIC

Debe crear tarjetas NIC (o modificar las existentes) y asociarlas a las reglas NAT, las reglas de equilibrador de carga y los sondeos.

### Paso 1 

Obtenga la red virtual y la subred donde se deben crear las tarjetas NIC.

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Paso 2

Cree una NIC llamada *lb-nic1-be* y asóciela con la primera regla NAT y el primer (y único) grupo de direcciones de back-end.
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Paso 3

Cree una NIC llamada *lb-nic2-be* y asóciela con la segunda regla NAT y el primer (y único) grupo de direcciones de back-end.

	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Paso 4

Compruebe las tarjetas NIC.


	PS C:\> $backendnic1

Resultado esperado:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Paso 5

Use el cmdlet `Add-AzureVMNetworkInterface` para asignar las tarjetas NIC a distintas máquinas virtuales.

Puede encontrar instrucciones sobre cómo crear una máquina virtual y asignar una NIC en [Creación y preconfiguración de una máquina virtual de Windows con el Administrador de recursos y Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) con la opción 5 del ejemplo.

## Actualización de un equilibrador de carga existente


### Paso 1

Con el equilibrador de carga del ejemplo anterior, asigne el objeto del equilibrador de carga a la variable $slb mediante Get-AzureLoadBalancer

	$slb=get-azureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Paso 2

En el ejemplo siguiente, agregará una nueva regla de NAT de entrada mediante el puerto 81 en el front-end y el puerto 8181 para el grupo de back-end a un equilibrador de carga existente

	$slb | Add-AzureLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### Paso 3

Guarde la nueva configuración mediante Set-AzureLoadBalancer

	$slb | Set-AzureLoadBalancer

## Elimine un equilibrador de carga

Use el comando Remove-AzureLoadBalancer para eliminar un equilibrador de carga creado previamente denominado "NRP-LB" en un grupo de recursos denominado "NRP-RG"

	Remove-AzureLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]Puede usar el conmutador opcional -Force para evitar la solicitud de eliminación.

## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-internal-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Nov15_HO3-->