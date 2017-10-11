---
title: SKU de puerta de enlace de red virtual de Azure heredada | Microsoft Docs
description: SKU de puerta de enlace de red virtual antigua
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 3b2126b1ecd1613950bbf311ae08fafd4af0d51f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabajo con SKU de puerta de enlace de red virtual (SKU antiguas)

Este artículo contiene información sobre las SKU de puerta de enlace de red virtual heredadas (antiguas). Las SKU heredadas siguen funcionando en ambos modelos de implementación para las puertas de enlace de VPN ya creadas. Las puertas de enlace de VPN clásicas siguen usando las SKU heredadas para puertas de enlace existentes y para nuevas puertas de enlace. Al crear nuevas puertas de enlace de VPN de Resource Manager, use las nuevas SKU de puerta de enlace. Para más información sobre las nuevas SKU, vea [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU de puerta de enlace

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Rendimiento agregado estimado por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configuraciones admitidas por el tipo de VPN y SKU

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Cambio de tamaño de una puerta de enlace (cambio de SKU de puerta de enlace)

Puede cambiar el tamaño de una SKU de puerta de enlace dentro de la misma familia de la SKU. Por ejemplo, si tiene una SKU Estándar, puede cambiar a una SKU HighPerformance. No se puede cambiar el tamaño de las puertas de enlace de VPN entre las familias de SKU antiguas y las nuevas. Por ejemplo, no se puede pasar de una SKU Estándar a una SKU VpnGw2. 

Para cambiar el tamaño de una SKU de puerta de enlace del modelo de implementación clásica, use el siguiente comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Para cambiar el tamaño de una SKU de puerta de enlace del modelo de implementación de Resource Manager, use el siguiente comando:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>Migración a las nuevas SKU de puerta de enlace

Si está trabajando con el modelo de implementación de Resource Manager, puede migrar a las nuevas SKU de puerta de enlace. Si está trabajando con el modelo de implementación clásica, no puede migrar a las nuevas SKU, sino que debe seguir usando las SKU heredadas.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las nuevas SKU de puerta de enlace, consulte [SKU de puerta de enlace](vpn-gateway-about-vpngateways.md#gwsku).

Para más información sobre los valores de configuración, vea [Acerca de la configuración de la puerta de enlace de VPN](vpn-gateway-about-vpn-gateway-settings.md).