### <a name="noconnection"></a>Adición o eliminación de prefijos (sin conexión de la puerta de enlace)
### <a name="to-add-additional-prefixes"></a>Para agregar prefijos adicionales

Para agregar prefijos de dirección adicionales a una puerta de enlace de red local que se ha creado, pero que aún no tiene una conexión de puerta de enlace, use el ejemplo siguiente. Asegúrese de cambiar los valores por los suyos.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```
### <a name="to-remove-an-address-prefix"></a>Para quitar un prefijo de dirección

Para quitar un prefijo de dirección de una puerta de enlace de red local que no dispone de una conexión VPN, use el ejemplo siguiente. Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 20.0.0.0/24 (del ejemplo anterior), por lo que se actualizará la puerta de enlace de red local y se excluirá ese prefijo.

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>Adición o eliminación de prefijos (conexión de la puerta de enlace existente)
Si ha creado una conexión de puerta de enlace y desea agregar o quitar los prefijos de dirección IP contenidos en la puerta de enlace de red local, tendrá que realizar los pasos siguientes en orden. Esto dará como resultado un tiempo de inactividad para la conexión VPN. Al actualizar los prefijos, quite primero la conexión, modifique los prefijos y luego cree una nueva conexión. En los siguientes ejemplos, asegúrese de cambiar los valores por los suyos.

> [!IMPORTANT]
> No elimine la puerta de enlace de VPN. Si lo hace, tendrá que realizar otra vez los pasos para crearla de nuevo, así como volver a configurar el enrutador local con la nueva configuración.
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
3. Cree la conexión. En este ejemplo, vamos a configurar un tipo de conexión IPsec. Cuando se vuelva a crear la conexión, use el tipo de conexión que se especifica para la configuración. Para otros tipos de conexión, consulte la página de [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .
   
  Establezca la variable para VirtualNetworkGateway.

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  Cree la conexión. Tenga en cuenta que en este ejemplo se utiliza la variable $local que estableció en el paso anterior.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```
