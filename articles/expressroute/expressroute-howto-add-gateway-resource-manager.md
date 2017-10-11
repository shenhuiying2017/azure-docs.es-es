---
title: "Adición de una puerta de enlace de red virtual a una red virtual para ExpressRoute: PowerShell (Azure) | Microsoft Docs"
description: "En este artículo, se indican los pasos para agregar una puerta de enlace de red virtual a una red virtual de Resource Manager ya creada para ExpressRoute."
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: charwen
ms.openlocfilehash: 3aeddd03e0be548933775164ae790ba208fc13ae
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configuración de una puerta de enlace de red virtual para ExpressRoute con PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager: Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clásico: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo: Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artículo lo guía por los pasos para agregar, cambiar el tamaño y quitar una puerta de enlace de red virtual (VNet) para una red virtual existente. Los pasos de esta configuración son específicos para las redes virtuales que se crearon con el modelo de implementación de Resource Manager que se utilizarán en una configuración ExpressRoute. Para obtener más información acerca de las puertas de enlace de red virtual y la configuración de puerta de enlace para ExpressRoute, consulte [Acerca de las puertas de enlace de red virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Antes de comenzar
Compruebe que haya instalado los cmdlets más recientes de Azure PowerShell. Si no, deberá hacerlo antes de comenzar con los pasos de configuración. Para obtener más información, vea [Install and Configure Azure PowerShell](/powershell/azure/overview) (Instalación y configuración de Azure PowerShell).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Después de crear la puerta de enlace de red virtual, puede vincular la red virtual a un circuito ExpressRoute. Consulte el artículo [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

