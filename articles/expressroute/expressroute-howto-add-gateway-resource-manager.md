<properties
   pageTitle="Adición de una puerta de enlace de VPN a una red virtual para ExpressRoute usando Resource Manager y PowerShell | Microsoft Azure"
   description="En este artículo se indican los pasos para agregar una puerta de enlace de VPN a una red virtual de Resource Manager ya creada para ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# Adición de una puerta de enlace de VPN a una red virtual de Resource Manager para una configuración de ExpressRoute 

Este artículo le guiará por los pasos necesarios para agregar una subred de puerta de enlace y crear una puerta de enlace de VPN para una red virtual existente. Los pasos de esta configuración son específicos para las redes virtuales que se crearon con el **modelo de implementación de Resource Manager** y que se utilizarán en una configuración ExpressRoute. Si necesita información sobre cómo crear puertas de enlace para las redes virtuales con el modelo de implementación clásico, consulte [Configurar una red virtual en ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## Antes de comenzar

Compruebe que ha instalado los cmdlets de Azure PowerShell necesarios para esta configuración (1.0.2 o posterior). Si no, deberá hacerlo antes de comenzar con los pasos de configuración. Para obtener más información sobre cómo instalar Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## Comprobación de la creación de la puerta de enlace

Utilice el siguiente comando para comprobar si se ha creado la puerta de enlace.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## Pasos siguientes

Después de crear la puerta de enlace de VPN, puede vincular la red virtual a un circuito ExpressRoute. Consulte el artículo [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---HONumber=AcomDC_0309_2016-->