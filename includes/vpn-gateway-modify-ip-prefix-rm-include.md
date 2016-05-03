### Agregar o quitar prefijos si todavía no ha creado una conexión de puerta de enlace VPN

- **Para agregar** prefijos de dirección adicionales a una puerta de enlace de red local que haya creado, pero que todavía no dispone de una conexión de puerta de enlace de VPN, use el ejemplo siguiente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para quitar** un prefijo de dirección de una puerta de enlace de red local que no dispone de una conexión VPN, use el ejemplo siguiente. Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualizará la puerta de enlace de red local y se excluirá ese prefijo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Agregar o quitar prefijos si ya ha creado una conexión de puerta de enlace VPN

Si ha creado la conexión VPN y quiere agregar o quitar los prefijos de dirección IP contenidos en la puerta de enlace de red local, tendrá que realizar los pasos siguientes en orden. Esto dará como resultado un tiempo de inactividad para la conexión VPN.

>[AZURE.IMPORTANT] No elimine la puerta de enlace de VPN. Si lo hace, tendrá que realizar otra vez los pasos para crearla de nuevo, así como volver a configurar el enrutador local con la nueva configuración.
 
1. Quite la conexión de IPsec. 
2. Modificar los prefijos de la puerta de enlace de red local. 
3. Cree una nueva conexión de IPsec. 

Puedes usar el ejemplo siguiente como guía.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0427_2016-->