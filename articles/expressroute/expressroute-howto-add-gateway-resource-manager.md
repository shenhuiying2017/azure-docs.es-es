<properties
   pageTitle="Adding a VNet gateway to a virtual network for ExpressRoute using Resource Manager and PowerShell (Incorporación de una puerta de enlace de red virtual en ExpressRoute con Resource Manager y PowerShell) | Microsoft Azure"
   description="En este artículo se indican los pasos para agregar una puerta de enlace de red virtual a una red virtual de Resource Manager ya creada para ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2016"
   ms.author="charwen"/>

# Configure a virtual network gateway for ExpressRoute using Resource Manager and PowerShell (Configuración de una puerta de enlace de red virtual en ExpressRoute con Resource Manager y PowerShell)


> [AZURE.SELECTOR]
- [PowerShell: administrador de recursos](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell: clásico](expressroute-howto-add-gateway-classic.md)


Este artículo le guiará por los pasos para agregar, cambiar el tamaño y quitar una puerta de enlace de red virtual (VNet) para una red virtual existente. Los pasos de esta configuración son específicos para las redes virtuales que se crearon con el **modelo de implementación de Resource Manager** y que se utilizarán en una configuración ExpressRoute.

**Información acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Antes de comenzar

Compruebe que ha instalado los cmdlets de Azure PowerShell necesarios para esta configuración (1.0.2 o posterior). Si no, deberá hacerlo antes de comenzar con los pasos de configuración. Para más información sobre cómo instalar Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

	
## Pasos siguientes

Después de crear la puerta de enlace de red virtual, puede vincular la red virtual a un circuito ExpressRoute. Consulte el artículo [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0928_2016-->