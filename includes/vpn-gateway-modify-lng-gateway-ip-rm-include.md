---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/28/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ee182202cf1ecbbb0845541269f7241de26c170
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
ms.locfileid: "30326548"
---
### <a name="gwipnoconnection"></a>Para modificar la puerta de enlace de red local "GatewayIpAddress": no hay ninguna conexión de puerta de enlace

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. Use el ejemplo para modificar una puerta de enlace de red local que no tenga una conexión de puerta de enlace.

Al modificar este valor, también puede modificar al mismo tiempo los prefijos de dirección. Asegúrese de usar el nombre existente de la puerta de enlace de la red local para sobrescribir la configuración actual. Si usa otro nombre, creará una nueva puerta de enlace de red local, en lugar de sobrescribir la existente.

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a>Para modificar la puerta de enlace de red local "GatewayIpAddress": conexión de puerta de enlace existente

Si el dispositivo VPN al que desea conectarse ha cambiado su dirección IP pública, debe modificar la puerta de enlace de red local para reflejar ese cambio. Si ya existe una conexión de puerta de enlace, primero deberá quitar esa conexión. Después de quitar la conexión, puede modificar la dirección IP de la puerta de enlace y volver a crear una nueva conexión. También puede modificar los prefijos de dirección al mismo tiempo. Esto tendrá como resultado un tiempo de inactividad para la conexión VPN. Al modificar la dirección IP de puerta de enlace, no es necesario eliminar la puerta de enlace VPN. Basta con quitar la conexión.
 

1. Cierre la conexión. Puede encontrar el nombre de la conexión mediante el cmdlet 'Get-AzureRmVirtualNetworkGatewayConnection'.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1
  ```
2. Modifique el valor de 'GatewayIpAddress'. También puede modificar los prefijos de dirección al mismo tiempo. Asegúrese de utilizar el nombre de la puerta de enlace de la red local existente para sobrescribir la configuración actual. Si no lo hace, creará una nueva puerta de enlace de la red local, en lugar de sobrescribir la existente.

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name Site1 `
  -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
  ```
3. Cree la conexión. En este ejemplo, vamos a configurar un tipo de conexión de IPsec. Cuando se vuelva a crear la conexión, use el tipo de conexión que se especifica para la configuración. Para otros tipos de conexión, consulte la página de [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obtener el nombre de VirtualNetworkGateway, puede ejecutar el cmdlet 'Get-AzureRmVirtualNetworkGateway'.
   
    Establezca las variables.

  ```azurepowershell-interactive
  $local = Get-AzureRMLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  ```
   
    Cree la conexión.

  ```azurepowershell-interactive 
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
  -Location "East US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```