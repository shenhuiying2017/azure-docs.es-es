---
title: "Adición de una puerta de enlace de red virtual para ExpressRoute con Resource Manager y PowerShell | Microsoft Docs"
description: "En este artículo se indican los pasos para agregar una puerta de enlace de red virtual a una red virtual de Resource Manager ya creada para ExpressRoute."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 63e0bd60-abad-4963-8e27-3aa973e0d968
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 2a0db85dc5699b362aa6a920cc26c40bdfdfc28d
ms.openlocfilehash: 80ffdd66a02f84b844b660e6c88586d4e99ff9e7
ms.lasthandoff: 12/14/2016


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configure a virtual network gateway for ExpressRoute using Resource Manager and PowerShell (Configuración de una puerta de enlace de red virtual en ExpressRoute con Resource Manager y PowerShell)
> [!div class="op_single_selector"]
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clásico: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo: Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artículo le guiará por los pasos para agregar, cambiar el tamaño y quitar una puerta de enlace de red virtual (VNet) para una red virtual existente. Los pasos de esta configuración son específicos para las redes virtuales que se crearon con el **modelo de implementación de Resource Manager** y que se utilizarán en una configuración ExpressRoute. 

**Información acerca de los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Antes de comenzar
Compruebe que ha instalado los cmdlets de Azure PowerShell necesarios para esta configuración (1.0.2 o posterior). Si no, deberá hacerlo antes de comenzar con los pasos de configuración. Para más información sobre cómo instalar Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Después de crear la puerta de enlace de red virtual, puede vincular la red virtual a un circuito ExpressRoute. Consulte el artículo [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).


