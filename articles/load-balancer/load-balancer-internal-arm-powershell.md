<properties
   pageTitle="Introducción al equilibrador de carga interno mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="Cómo crear reglas de equilibrador de carga interno, reglas NAT y sondeo para el Administrador de recursos de Azure. Paso a paso que muestra el proceso completo de creación de un equilibrador de carga interno (ILB)."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/22/2015"
   ms.author="joaoma" />

# Creación de un equilibrador de carga mediante el Administrador de recursos de Azure


> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internal-getstarted.md)
- [Resource Manager Powershell steps](load-balancer-internal-arm-powershell.md)


Los pasos siguientes muestran cómo crear un equilibrador de carga interno con el Administrador de recursos de Azure PowerShell. Con el Administrador de recursos de Azure, los elementos para crear un equilibrador de carga interno se configuran individualmente y, después, se colocan juntos para crear un recurso.

En esta página describiremos la secuencia de tareas individuales que debe realizarse para crear un equilibrador de carga interno y explicaremos con detalle cada una de ellas.


## ¿Qué es necesario para crear un equilibrador de carga interno?

Antes de crear un equilibrador de carga interno hay que configurar los siguientes elementos:

- Configuración de direcciones IP front-end: va a configurar la dirección IP privada para el tráfico de red entrante 

- Grupo de direcciones de back-end: configurará las interfaces de red que recibirá el tráfico con equilibrio de carga proveniente del grupo de direcciones IP front-end.

- Reglas de equilibrio de carga: configuración del puerto de origen y el puerto local del equilibrador de carga.

- Sondeos: configura el sondeo del estado de mantenimiento de las instancias de máquinas virtuales.

- Reglas NAT de entrada: configura las reglas de puerto para obtener acceso directamente a una de las instancias de máquina virtual.

Para obtener más información acerca de los componentes del equilibrador de carga con el Administrador de recursos de Azure, consulte [Compatibilidad del Administrador de recursos de Azure con el equilibrador de carga](load-balancer-arm.md).

Los pasos siguientes muestran cómo configurar un equilibrador de carga para equilibrar la carga entre dos máquinas virtuales.


## Paso a paso con PowerShell


### Creación de un grupo de recursos para el equilibrador de carga


### Paso 1
Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del ARM. Hay más información disponible en [Uso de Windows PowerShell con el Administrador de recursos](powershell-azure-resource-manager.md).


    PS C:> Switch-AzureMode -Name AzureResourceManager

### Paso 2

Inicio de sesión en la cuenta de Azure


    PS C:> Add-AzureAccount

Se le pedirá autenticarse con sus credenciales.


### Paso 3

Elección de la suscripción de Azure que se va a usar.

    PS C:> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver una lista de suscripciones disponibles, use el cmdlet 'Get-AzureSubscription'.


### Paso 4

Creación de un grupo de recursos (omitir este paso si se usa un grupo de recursos existente)

    PS C:> New-AzureResourceGroup -Name NRP-RG -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear un equilibrador de carga usarán el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos llamado "NRP-RG" en la ubicación "West US".

## Creación de una red virtual y una dirección IP pública para el grupo de IP front-end


### Paso 1

Cree una red virtual:

	$backendSubnet = New-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Crea una subred para la red virtual y la asigna a la variable $backendSubnet

	$vnet= New-AzurevirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crea la red virtual y agrega lb-subnet-be para la subred a la red virtual NRPVNet y asigna a la variable $vnet



## Creación de un grupo de direcciones IP front-end y un grupo de direcciones back-end

Configure un grupo de IP front-end para el tráfico de red entrante del equilibrador de carga y un grupo de direcciones back-end para recibir el tráfico con equilibrio de carga.

### Paso 1 

Cree un grupo de IP front-end mediante la dirección IP privada 10.0.2.6 para la subred 10.0.2.0/24 que será el extremo de tráfico de red entrante.

	$frontendIP = New-AzureLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $backendSubnet.Id

### Paso 2 

Configure un grupo de direcciones back-end que se usará para recibir el tráfico entrante del grupo de IP front-end:

	$beaddresspool= New-AzureLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## Creación de reglas de equilibrio de carga, reglas NAT, sondeos y el equilibrador de carga

Después de crear el grupo de IP front-end y el grupo de direcciones back-end, deberá crear las reglas que pertenecerán al recurso de equilibrador de carga:

### Paso 1

	$inboundNATRule1= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

	$healthProbe = New-AzureLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

 	$lbrule = New-AzureLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


En el ejemplo anterior se crean los siguientes elementos:

- Regla de NAT por la que todo el tráfico entrante al puerto 3441 irá al puerto 3389.
- Una segunda regla de NAT por la que todo el tráfico entrante al puerto 3442 irá al puerto 3389.
- Una regla de equilibrador de carga que equilibrará la carga de todo el tráfico entrante en el puerto público 80 al puerto local 80 del grupo de direcciones back-end.
- Una regla de sondeo que comprobará el estado de mantenimiento de la ruta de acceso "HealthProbe.aspx".



### Paso 2

Reúna todos los objetos (reglas NAT, reglas del equilibrador de carga, configuraciones de sondeo) para crear el equilibrador de carga:

	$NRPLB = New-AzureLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 


## Creación de interfaces de red

Después de crear el equilibrador de carga interno, debe definir qué interfaces de red van a recibir el tráfico de red entrante con equilibrio de carga, las reglas NAT y el sondeo. En este caso, la interfaz de red se configura individualmente y se puede asignar a una máquina virtual más adelante.


### Paso 1 


Obtenga los recursos de red virtual y subred para crear las interfaces de red:

	$vnet = Get-AzureVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

	$backendSubnet = Get-AzureVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 


En este paso, vamos a crear una interfaz de red que pertenecerá al grupo back-end del equilibrador de carga y asociamos la primera regla NAT para RDP para esta interfaz de red:
	
	$backendnic1= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Paso 2

Cree una segunda interfaz de red llamada LB-Nic2-BE:

En este paso, creamos una segunda interfaz de red, la asignamos al mismo grupo back-end del equilibrador de carga y asociamos la segunda regla NAT creada para RDP:

 	$backendnic2= New-AzureNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]


A continuación se muestra el resultado final:


PS C:> $backendnic1


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



### Paso 3 

Use el comando Add-AzureVMNetworkInterface para asignar el NIC a una máquina virtual.

Encontrará instrucciones paso a paso para crear una máquina virtual y asignarla a un NIC en el artículo [Creación de una máquina virtual de Windows con el Administrador de recursos y Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)


## Otras referencias

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO5-->