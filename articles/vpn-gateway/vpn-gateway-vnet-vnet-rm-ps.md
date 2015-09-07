<properties
   pageTitle="Cree una conexión de red virtual a red virtual mediante el Administrador de recursos de Azure y PowerShell | Microsoft Azure"
	description="Este artículo le guiará para conectar redes virtuales entre sí por medio de PowerShell y el Administrador de recursos de Azure"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>

# Configurar una conexión de red virtual a red virtual mediante el Administrador de recursos de Azure y PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


La conexión de una red virtual a otra (VNet a VNet) es muy parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Las redes virtuales que se conecten pueden estar en regiones distintas. Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente.


![Diagrama de conectividad VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 

>[AZURE.NOTE]Actualmente, Azure tiene dos modelos de implementación: el modelo de implementación clásica y el modelo de implementación del Administrador de recursos de Azure. Los cmdlets y los pasos de configuración difieren entre los distintos modos de implementación. En este artículo se le guiará a través de los pasos necesarios para conectar redes virtuales creadas con el modo del Administrador de recursos de Azure. Si desea crear una conexión de red virtual a red virtual mediante el modo de implementación clásica, consulte [Configurar una conexión de red virtual a red virtual mediante el Portal de Azure](virtual-networks-configure-vnet-to-vnet-connection.md). Si desea conectar una red virtual creada en el modo clásico a una red virtual creada en el Administrador de recursos de Azure, consulte [Conexión de redes virtuales clásicas a redes virtuales nuevas](../virtual-network/virtual-networks-arm-asm-s2s.md).

## ¿Por qué debería conectarse a redes virtuales?

Puede que desee conectar redes virtuales por las siguientes razones:

- **Presencia geográfica y redundancia geográfica entre regiones**
	- Puede configurar su propia replicación geográfica o la sincronización con conectividad segura sin recurrir a los extremos con conexión a Internet.
	- Con el equilibrador de carga de Azure y Microsoft, o con una tecnología de agrupación en clústeres de otros fabricantes, puede configurar cargas de trabajo de alta disponibilidad y redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar Always On de SQL con grupos de disponibilidad en varias regiones de Azure.

- **Aplicaciones regionales de varios niveles con límite de aislamiento sólido**
	- En la misma región se pueden configurar aplicaciones de varios niveles con múltiples redes virtuales conectadas entre sí, con un aislamiento sólido y una comunicación entre niveles segura.



### Elementos a tener en cuenta

En este artículo, se le guiará en la conexión de dos redes virtuales: VNet1 y VNet2. Tendrá que familiarizarse con la conexión de redes para sustituir los intervalos de direcciones IP compatibles con sus requisitos de diseño de red.


![Conexión VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)



- Las redes virtuales pueden estar en la misma región de Azure o en regiones distintas (ubicaciones).

- Un servicio en la nube o un extremo de equilibrio de carga NO PUEDE abarcar varias redes virtuales aunque estas estén conectadas entre sí.

- La conexión simultánea de varias redes virtuales de Azure no requiere puertas de enlace de VPN locales, a menos que sea necesaria la conectividad entre entornos.

- VNet a VNet admite la conexión de redes virtuales. No admite la conexión de máquinas virtuales o servicios en la nube que NO estén en una red virtual.

- VNet a VNet requiere puertas de enlace de VPN de Azure con tipos de VPN RouteBased (anteriormente denominados dinámicos).

- La conectividad de red virtual se puede usar simultáneamente con VPN de varios sitios, con un máximo de 10 túneles de VPN por puerta de enlace de VPN de red virtual conectada, ya sea a otras redes virtuales o bien a sitios locales.

- Los espacios de direcciones de las redes virtuales y de los sitios de red local no se pueden solapar. Los espacios de direcciones solapados provocarán un error al crear redes virtuales.

- No se admiten los túneles redundantes entre un par de redes virtuales.

- Todos los túneles de VPN de la red virtual comparten el ancho de banda disponible en la puerta de enlace de VPN de Azure y el mismo SLA de tiempo de actividad de puerta de enlace de VPN en Azure.

- El tráfico VNet a VNet viaja a través de la red troncal de Azure.


## Antes de comenzar


Antes de empezar, compruebe que tiene lo siguiente:


- La versión más reciente de los cmdlets de Azure PowerShell Puede descargar e instalar la versión más reciente en la sección Windows PowerShell de la [Página de descargas](http://azure.microsoft.com/downloads/). 
- Una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).
- Si ya ha creado las redes virtuales, consulte [Conectar redes virtuales existentes](#connecting-existing-vnets).
	


Hay que seguir varios pasos para crear y configurar una conexión de red virtual a red virtual. Configure cada sección en el orden siguiente.


1. [Planear los intervalos de direcciones IP](#plan-your-ip-address-ranges)
2. [su suscripción](#connect-to-your-subscription)
3. [Crear una red virtual](#create-a-virtual-network)
4. [Solicitar una dirección IP pública para la puerta de enlace](#request-a-public-ip-address)
5. [Establecer la configuración de la puerta de enlace](#create-the-gateway-configuration)
6. [Creación de la puerta de enlace](#create-the-gateway)
7. [Repetir este procedimiento para configurar VNet2](#create-vnet2)
8. [Conectar las puertas de enlace de VPN](#connect-the-gateways)


## Planear los intervalos de direcciones IP

### Paso 1

Es importante decidir los intervalos que usará para establecer la configuración de red. Desde la perspectiva de VNet1, VNet2 solo es otra conexión VPN definida en la plataforma de Azure. Y desde la perspectiva de VNet2, VNet1 solo es otra conexión VPN. Tenga en cuenta que hay que asegurarse de que ninguno de los intervalos de VNet o intervalos de red local se solapen.


En los pasos siguientes, se crearán dos redes virtuales junto con sus subredes de puerta de enlace y configuraciones correspondientes. Después se creará una conexión de puerta de enlace de VPN entre las redes dos virtuales.

En este ejercicio, utilice los siguientes valores para las redes virtuales:

Valores para VNet1:

- Nombre de la red virtual = VNet1
- Grupo de recursos = testrg1
- Espacio de direcciones: 10.1.0.0/16 
- Región = Oeste de EE. UU.
- GatewaySubnet = 10.1.0.0/28
- Subnet1 = 10.1.1.0/28

Valores para VNet2:

- Nombre de la red virtual = VNet2
- Grupo de recursos = testrg2
- Espacio de direcciones: 10.2.0.0/16
- Región = Este de Japón
- GatewaySubnet = 10.2.0.0/28
- Subnet1 = 10.2.1.0/28

## su suscripción 

### Paso 2


Abra la consola de PowerShell y cambie al modo Administrador de recursos de Azure. Use el siguiente ejemplo para ayudarle a conectarse:

		Add-AzureAccount

Ejecute Select-AzureSubscription para conectarse a la suscripción que desea usar.

		Select-AzureSubscription "yoursubscription"

A continuación, cambie al modo ARM. Esto cambiará el modo para que pueda usar los cmdlets de ARM.

		Switch-AzureMode -Name AzureResourceManager

## Crear una red virtual

### Paso 3


Use el ejemplo siguiente para crear una red virtual y una subred de puerta de enlace. Sustituya los valores por los suyos. En este ejemplo, se va a crear VNet1. Podrá repetir los pasos para crear VNet2 más adelante.

En primer lugar, cree un grupo de recursos.

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

A continuación, cree su red virtual En el siguiente ejemplo se crea una red virtual denominada *VNet1* y dos subredes, una denominada *GatewaySubnet* y la otra denominada *Subnet1*. Es importante crear una subred denominada específicamente *GatewaySubnet*. Si la asigna otro nombre, se producirá un error en la configuración de la conexión. En el ejemplo siguiente, la subred de puerta de enlace está usando un /28. Puede elegir usar una subred de puerta de enlace tan pequeña como /29. Tenga en cuenta que algunas características (como una conexión en la que coexistan ExpressRoute y sitio a sitio) requieren una subred de puerta de enlace mayor de /27, así que quizá desee crear la subred de puerta de enlace para dar cabida a características adicionales que puede utilizar en el futuro.

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzurevirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## Solicitar una dirección IP pública

### Paso 4

A continuación, solicitará que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. No puede especificar la dirección IP que desea usar; se asigna una dinámicamente a la puerta de enlace. Utilizará esta dirección IP en la siguiente sección de configuración.

Utilice el siguiente ejemplo. El método de asignación para esta dirección debe ser dinámico.


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic


## Establecer la configuración de la puerta de enlace

### Paso 5

La configuración de puerta de enlace define la subred y la dirección IP pública. Use el ejemplo siguiente para crear la configuración de la puerta de enlace.


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Creación de la puerta de enlace

### Paso 6

En este paso, creará la puerta de enlace de red virtual para la VNet. Las configuraciones VNet a VNet requieren un VpnType RouteBased. La creación de una puerta de enlace puede tardar un rato, así que tenga paciencia.

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Crear VNet2

### Paso 7

Tras haber configurado VNet1, repita los pasos anteriores para configurar VNet2 junto con su configuración de puerta de enlace. Una vez completada la configuración tanto para las redes virtuales como de sus respectivas puertas de enlace, continúe con [Conectar las puertas de enlace](#connect-the-gateways).


## Conectar las puertas de enlace

### Paso 8

En este paso, creará las conexiones de puerta de enlace de VPN entre las dos puertas de enlace de red virtual. Verá una clave compartida a la que se hace referencia en los ejemplos. Puede utilizar sus propios valores para la clave compartida. Lo importante es que la clave compartida coincida en ambas configuraciones.

Al crear las conexiones, tenga en cuenta que tardan un tiempo en completarse.

**De VNet1 a VNet2**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**De VNet2 a VNet1**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    


Después de unos minutos, se debe establecer la conexión. Tenga en cuenta que en este momento, las puertas de enlace y las conexiones creadas con el Administrador de recursos de Azure no son visibles en el Portal de vista previa.

## Conectar las redes virtuales existentes

Si ya ha creado redes virtuales en modo de Administrador de recursos de Azure y desea conectarlas, compruebe lo siguiente:

- Tiene una subred de puerta de enlace de al menos /29 o mayor para cada red virtual.
- Los intervalos de direcciones para las redes virtuales no se superponen.

Si necesita agregar subredes de puerta de enlace a sus redes virtuales, utilice el ejemplo siguiente, reemplazando los valores dados por los suyos propios. Asegúrese de asignar el nombre 'GatewaySubnet' a la subred de puerta de enlace. Si la asigna otro nombre, la configuración de VPN no funcionará según lo esperado.

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

Después de comprobar que las subredes de la puerta de enlace están configuradas correctamente, continúe con [Solicitar una dirección IP pública](#request-a-public-ip-address) y siga los pasos.

## Pasos siguientes

Puede agregar máquinas virtuales a las redes virtuales. [Crear una máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->