---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
Para las SKU actuales (VpnGw1, VpnGw2 y VPNGW3), si quiere cambiar el tamaño de la SKU de puerta de enlace para actualizar a una más eficaz, puede usar el cmdlet de PowerShell `Resize-AzureRmVirtualNetworkGateway`. También puede cambiar a una versión anterior del tamaño de la SKU de puerta de enlace con este cmdlet. Si usa la SKU de puerta de enlace Basic, [siga estas instrucciones](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) para cambiar el tamaño de la puerta de enlace.

En el siguiente ejemplo de PowerShell se muestra una SKU de puerta de enlace cuyo tamaño se ha cambiado a VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

También se puede cambiar el tamaño de una puerta de enlace en Azure Portal desde la página **Configuración** para la puerta de enlace de red virtual. Para hacerlo, seleccione otra SKU en la lista desplegable.