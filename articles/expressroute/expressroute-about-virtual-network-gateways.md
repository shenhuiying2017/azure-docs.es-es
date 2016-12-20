---
title: Acerca de las puertas de enlace de red virtual de ExpressRoute | Microsoft Docs
description: Conozca sobre las puertas de enlace de red virtual para ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a575929f2c42a00b6e6cacd86318253d20b6b51e


---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Acerca de las puertas de enlace de red virtual para ExpressRoute
Una puerta de enlace de red virtual se usa para enviar tráfico de red entre redes virtuales y ubicaciones locales de Azure. Cuando configure una conexión ExpressRoute, debe crear y configurar una puerta de enlace de red virtual y una conexión de puerta de enlace de red virtual.

Al crear una puerta de enlace de red virtual, se especifican varios valores de configuración. Uno de los valores de configuración necesarios especifica si la puerta de enlace se usará para el tráfico de Express Route o de VPN de sitio a sitio. En el modelo de implementación de Resource Manager, el valor es '-GatewayType'.

Cuando se envíe el tráfico de red en una conexión privada dedicada, use el tipo de puerta de enlace "ExpressRoute". Esto también se conoce como puerta de enlace de ExpressRoute. Cuando envíe el tráfico de red cifrado a través de una conexión a Internet pública, use el tipo de puerta de enlace "VPN". Esto se conoce como puerta de enlace de VPN. Las conexiones de sitio a sitio, de punto a sitio y de red virtual a red virtual utilizan una puerta de enlace de VPN. 

Cada red virtual tiene una única puerta de enlace de red virtual por cada tipo de puerta de enlace. Por ejemplo, puede tener una puerta de enlace de una red virtual que use -GatewayType Vpn y otra que use -GatewayType ExpressRoute. Este artículo se centra en la puerta de enlace de red virtual de ExpressRoute.

## <a name="a-namegwskuagateway-skus"></a><a name="gwsku"></a>SKU de puerta de enlace
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si desea actualizar la puerta de enlace a una SKU de puerta de enlace más eficaz, en la mayoría de los casos puede usar el cmdlet de PowerShell Resize-AzureRmVirtualNetworkGateway. Esto funcionará para las actualizaciones de SKU Standard y HighPerformance. Sin embargo, para actualizar a la SKU UltraPerformance, debe volver a crear la puerta de enlace.

### <a name="a-nameaggthroughputaestimated-aggregate-throughput-by-gateway-sku"></a><a name="aggthroughput"></a>Rendimiento agregado estimado por SKU de puerta de enlace
En la tabla siguiente se muestran los tipos de puerta de enlace y el rendimiento agregado estimado. Esta tabla se aplica a los modelos de implementación del Administrador de recursos y clásico.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

## <a name="a-nameresourcesarest-apis-and-powershell-cmdlets"></a><a name="resources"></a>API de REST y cmdlets de PowerShell
Para más información sobre recursos técnicos y requisitos de sintaxis específicos al usar API de REST y cmdlets de PowerShell para configuraciones de puerta de enlace de red virtual, consulte las páginas siguientes:

| **Clásico** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API DE REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API DE REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Pasos siguientes
Consulte [Información técnica de ExpressRoute](expressroute-introduction.md) para más información sobre configuraciones de conexión disponibles. 




<!--HONumber=Nov16_HO3-->


