Antes de efectuar las siguientes tareas, debe crear una red virtual y una subred de puerta de enlace. Consulte el artículo [Configuración de una red virtual para ExpressRoute en el Portal clásico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) para obtener más información.

## Adición de una puerta de enlace

Utilice el siguiente comando para crear una puerta de enlace. Asegúrese de sustituir los valores por los suyos propios.

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## Comprobación de la creación de la puerta de enlace

Utilice el siguiente comando para comprobar si se ha creado la puerta de enlace. Este comando también recupera el identificador de la puerta de enlace, que necesita para realizar otras operaciones.

	Get-AzureVirtualNetworkGateway

## Cambio del tamaño de una puerta de enlace

Hay tres [SKU de puerta de enlace](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md). Puede utilizar el siguiente comando para cambiar en cualquier momento la SKU de puerta de enlace.

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## Eliminación de una puerta de enlace

Utilice el siguiente comando para quitar una puerta de enlace.

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->