### <a name="gwipnoconnection"></a>Para modificar la puerta de enlace de red local "GatewayIpAddress": no hay ninguna conexión de puerta de enlace

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. Use el ejemplo para modificar una puerta de enlace de red local que no tenga una conexión de puerta de enlace.

Al modificar este valor, también puede modificar al mismo tiempo los prefijos de dirección. Asegúrese de usar el nombre existente de la puerta de enlace de la red local para sobrescribir la configuración actual. Si usa otro nombre, creará una nueva puerta de enlace de red local, en lugar de sobrescribir la existente.

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>Para modificar la puerta de enlace de red local "GatewayIpAddress": conexión de puerta de enlace existente

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. Si ya existe una conexión de puerta de enlace, primero deberá quitar esa conexión. Después de quitar la conexión, puede modificar la dirección IP de la puerta de enlace y volver a crear una nueva conexión. También puede modificar los prefijos de dirección al mismo tiempo. Esto tendrá como resultado un tiempo de inactividad para la conexión VPN. Al modificar la dirección IP de puerta de enlace, no es necesario eliminar la puerta de enlace VPN. Basta con quitar la conexión.
 

1. Cierre la conexión. Puede encontrar el nombre de la conexión mediante el cmdlet 'Get-AzureRmVirtualNetworkGatewayConnection'.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. Modifique el valor de 'GatewayIpAddress'. También puede modificar los prefijos de dirección al mismo tiempo. Asegúrese de utilizar el nombre de la puerta de enlace de la red local existente para sobrescribir la configuración actual. Si no lo hace, creará una nueva puerta de enlace de la red local, en lugar de sobrescribir la existente.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. Cree la conexión. En este ejemplo, vamos a configurar un tipo de conexión de IPsec. Cuando se vuelva a crear la conexión, use el tipo de conexión que se especifica para la configuración. Para otros tipos de conexión, consulte la página de [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obtener el nombre de VirtualNetworkGateway, puede ejecutar el cmdlet 'Get-AzureRmVirtualNetworkGateway'.
   
    Establezca las variables.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    Cree la conexión.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```