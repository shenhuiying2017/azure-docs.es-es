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
ms.openlocfilehash: 8803aada61ae58f1e221767aeb382f7d74c63eb4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
ms.locfileid: "30326532"
---
### <a name="noconnection"></a>Para modificar los prefijos de dirección IP de la puerta de enlace de red local (sin conexión de puerta de enlace)

Para agregar prefijos de dirección adicionales:

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
```

Para quitar prefijos de dirección:<br>
Omita los prefijos que ya no necesite. En este ejemplo, ya no necesitamos el prefijo 10.101.2.0/24 (del ejemplo anterior), por lo que se actualiza la puerta de enlace de la red local, sin incluir ese prefijo.

```azurepowershell-interactive
$local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
```

### <a name="withconnection"></a>Para modificar los prefijos de dirección IP de la puerta de enlace de red local (conexión de puerta de enlace existente)

Si tiene una conexión de puerta de enlace y desea agregar o quitar los prefijos de dirección IP contenidos en la puerta de enlace de red local, tendrá que realizar los pasos siguientes en orden. Esto tendrá como resultado un tiempo de inactividad para la conexión VPN. Al modificar los prefijos de dirección IP, no es necesario eliminar la puerta de enlace VPN. Basta con quitar la conexión.


1. Cierre la conexión.

  ```azurepowershell-interactive
  Remove-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
  ```
2. Modifique los prefijos de dirección de su puerta de enlace de red local.
   
  Establezca la variable para LocalNetworkGateway.

  ```azurepowershell-interactive
  $local = Get-AzureRmLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
  ```
   
  Modifique los prefijos.
   
  ```azurepowershell-interactive
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```
3. Cree la conexión. En este ejemplo, vamos a configurar un tipo de conexión de IPsec. Cuando se vuelva a crear la conexión, use el tipo de conexión que se especifica para la configuración. Para otros tipos de conexión, consulte la página de [cmdlets de PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).
   
  Establezca la variable para VirtualNetworkGateway.

  ```azurepowershell-interactive
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
  ```
   
  Cree la conexión. Este ejemplo utiliza la variable $local que se estableció en el paso 2.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 `
  -ResourceGroupName TestRG1 -Location 'East US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```