### <a name="noconnection"></a>Modificar prefijos - no hay ninguna conexión de puerta de enlace

- Para agregar prefijos de dirección adicionales:

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```

- Para quitar un prefijo de dirección:<br>
  Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualiza la puerta de enlace de la red local, sin incluir ese prefijo.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
  ```

### <a name="withconnection"></a>Modificar prefijos - conexión de puerta de enlace existente
Si tiene una conexión de puerta de enlace y desea agregar o quitar los prefijos de dirección IP contenidos en la puerta de enlace de red local, tendrá que realizar los pasos siguientes en orden. Esto tendrá como resultado un tiempo de inactividad para la conexión VPN.

> [!IMPORTANT]
> No elimine la puerta de enlace de VPN. Si lo hace, tendrá que volver atrás los pasos necesarios para volver a crearla. Además, debe actualizar el dispositivo VPN local con la nueva dirección IP de la puerta de enlace VPN.
> 
> 

1. Cierre la conexión.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. Modifique los prefijos de dirección de su puerta de enlace de red local.
   
  Establezca la variable para LocalNetworkGateway.

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  Modifique los prefijos.
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. Cree la conexión. En este ejemplo, vamos a configurar un tipo de conexión de IPsec. Cuando se vuelva a crear la conexión, use el tipo de conexión que se especifica para la configuración. Para otros tipos de conexión, consulte la página de [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .
   
  Establezca la variable para VirtualNetworkGateway.

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  Cree la conexión. Este ejemplo utiliza la variable $local que se estableció en el paso 2.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```