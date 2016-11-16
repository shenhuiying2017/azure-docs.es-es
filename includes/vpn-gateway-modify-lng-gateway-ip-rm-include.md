Para modificar la dirección IP de puerta de enlace, use el cmdlet `New-AzureRmVirtualNetworkGatewayConnection` . Siempre que mantenga el nombre de la puerta de enlace de red local exactamente igual que el nombre existente, se sobrescribirá la configuración. De momento, el cmdlet "Set" no admite la modificación de la dirección IP de puerta de enlace.

### <a name="a-namegwipnoconnectionahow-to-modify-the-gateway-ip-address-no-gateway-connection"></a><a name="gwipnoconnection"></a>Cómo modificar la dirección de puerta de enlace IP - sin conexión de puerta de enlace
Para actualizar la dirección IP de puerta de enlace para la puerta de enlace de red local que todavía no tiene una conexión, utilice el siguiente ejemplo. También puede actualizar los prefijos de dirección al mismo tiempo. La configuración que especifique sobrescribirá la configuración existente. Asegúrese de usar el nombre existente de la puerta de enlace de red local. Si no lo hace, creará una nueva puerta de enlace de red local, no sobrescribirá la existente.

Use el ejemplo siguiente y reemplace los valores por los suyos propios.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="a-namegwipwithconnectionahow-to-modify-the-gateway-ip-address-existing-gateway-connection"></a><a name="gwipwithconnection"></a>Cómo modificar la dirección de puerta de enlace IP - conexión de puerta de enlace existente
Si ya existe una conexión de puerta de enlace, primero deberá quitar esa conexión. Después, modifique la dirección IP de puerta de enlace y vuelva a crear una nueva conexión. Esto dará como resultado un tiempo de inactividad para la conexión VPN.

> [!IMPORTANT]
> No elimine la puerta de enlace de VPN. Si lo hace, tendrá que realizar otra vez los pasos para volver a crearla, así como volver a configurar el enrutador local con la dirección IP que se asignará a la puerta de enlace recientemente creada.
> 
> 

1. Cierre la conexión. Puede encontrar el nombre de la conexión mediante el cmdlet `Get-AzureRmVirtualNetworkGatewayConnection` .
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. Modifique el valor de GatewayIpAddress. También puede modificar los prefijos de dirección en este momento, si es necesario. Tenga en cuenta que esto sobrescribirá la configuración de puerta de enlace de red local existente. Utilice el nombre existente de la puerta de enlace de red local al modificarla para sobrescribir la configuración. Si no lo hace, se creará una nueva puerta de enlace de red local, y no se sobrescribirá la existente.
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. Cree la conexión. En este ejemplo, vamos a configurar un tipo de conexión IPsec. Cuando se vuelva a crear la conexión, use el tipo de conexión que se especifica para la configuración. Para otros tipos de conexión, consulte la página de [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obtener el nombre de VirtualNetworkGateway, puede ejecutar el cmdlet `Get-AzureRmVirtualNetworkGateway` .
   
    Establezca las variables:
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    Cree la conexión:
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Nov16_HO2-->


