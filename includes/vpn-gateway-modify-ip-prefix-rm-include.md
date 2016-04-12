### Agregar o quitar prefijos si todavía no ha creado una conexión de puerta de enlace VPN

- **Para agregar** prefijos de dirección adicionales a una puerta de enlace de red local que haya creado, pero que todavía no dispone de una conexión de puerta de enlace de VPN, use el ejemplo siguiente.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para quitar** un prefijo de dirección de una puerta de enlace de red local que no dispone de una conexión VPN, use el ejemplo siguiente. Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualizará la puerta de enlace de red local y se excluirá ese prefijo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Agregar o quitar prefijos si ya ha creado una conexión de puerta de enlace VPN

Si ha creado la conexión VPN y quiere agregar o quitar los prefijos de dirección IP contenidos en la puerta de enlace de red local, tendrá que realizar los pasos siguientes en orden. Esto provocará un tiempo de inactividad en la conexión de VPN, ya que tendrás que eliminar y volver a crear la puerta de enlace. Sin embargo, como solicitaste una dirección IP para la conexión, no tendrás que volver a configurar el enrutador VPN local a menos que decidas cambiar los valores que usaste anteriormente.
 
1. Quitar la conexión de puerta de enlace. 
2. Modificar los prefijos de la puerta de enlace de red local. 
3. Crear una nueva conexión de puerta de enlace. 

Puedes usar el ejemplo siguiente como guía.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0323_2016-->