---
title: Azure Virtual Network | Microsoft Docs
description: Más información sobre las características y conceptos de Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/23/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 851c8c1eb13497355038ef4a8d5f1f9326c8c3bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781187"
---
# <a name="what-is-azure-virtual-network"></a>¿Qué es Azure Virtual Network?

Azure Virtual Network permite muchos tipos de recursos de Azure, como máquinas virtuales (VM) de Azure, para comunicarse de forma segura entre ellos, con Internet y con las redes locales. Azure Virtual Network proporciona las siguientes funcionalidades importantes:

## <a name="isolation-and-segmentation"></a>Aislamiento y segmentación

Puede implementar varias redes virtuales dentro de cada [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [región](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) de Azure. Cada red virtual está aislada de las otras redes virtuales. Para cada red virtual puede:
- Especificar un espacio de direcciones IP privado personalizado mediante direcciones públicas y privadas (RFC 1918). Azure asigna a los recursos de una red virtual una dirección IP privada desde el espacio de direcciones que asigne.
- Segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de la red virtual para cada subred.
- Usar la resolución de nombres que proporciona Azure o especificar su propio servidor DNS para que lo usen los recursos conectados a una red virtual.

## <a name="communicate-with-the-internet"></a>Comunicación con internet

De manera predeterminada, todos los recursos de una red virtual se pueden comunicar hacia Internet. Para comunicarse con un recurso mediante, asígnele una dirección IP pública. Para más información, consulte [Direcciones IP públicas](virtual-network-public-ip-address.md).

## <a name="communicate-between-azure-resources"></a>Comunicación entre recursos de Azure

Los recursos de Azure se comunican de manera segura entre sí de una de las maneras siguientes:

- **Mediante una red virtual**: tanto las máquinas virtuales como otros tipos de recursos de Azure se pueden implementar en una red virtual, como Azure App Service Environment, Azure Kubernetes Service (AKS) y Azure Virtual Machine Scale Sets. Para ver una lista completa de los recursos de Azure que puede implementar en una red virtual, consulte [Integración de red virtual para los servicios de Azure](virtual-network-for-azure-services.md). 
- **Mediante un punto de conexión de servicio de red virtual**: extienda el espacio de direcciones privadas de la red virtual y la identidad de la red virtual a los recursos del servicio de Azure, como cuentas de Azure Storage e instancias de Azure SQL Database, mediante una conexión directa. Los puntos de conexión de los servicios permiten proteger los recursos críticos de los servicios de Azure únicamente en una red virtual. Para más información, consulte [Puntos de conexión del servicio de redes virtuales](virtual-network-service-endpoints-overview.md).
 
## <a name="communicate-with-on-premises-resources"></a>Comunicación con recursos locales

Puede conectar sus equipos y redes local a una red virtual mediante cualquier combinación de las siguientes opciones:

- **Red privada virtual (VPN) de punto a sitio:** se establece entre una red virtual y un equipo individual de la red. Cada equipo que desea establecer conectividad con una red virtual debe configurar su conexión. Este tipo de conexión es muy útil cuando se está comenzando con Azure, o para los desarrolladores, porque requiere poco o ningún cambio en la red existente. La comunicación entre el equipo y una red virtual se envía mediante un túnel cifrado a través de internet. Para más información, consulte [VPN de punto a sitio](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **VPN de sitio a sitio:** se establece entre un dispositivo VPN local y una instancia de Azure VPN Gateway que está implementada en una red virtual. Este tipo de conexión permite que cualquier recurso local que autorice acceda a una red virtual. La comunicación entre un dispositivo VPN local y una puerta de enlace de VPN de Azure se envía mediante un túnel cifrado a través de internet. Para más información, consulte [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** establecida entre la red y Azure, a través de un asociado de ExpressRoute. Esta conexión es privada. El tráfico no pasa por Internet. Para más información, consulte [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrado del tráfico de red
Puede filtrar el tráfico de red entre subredes mediante una o ambas de las siguientes opciones:
- **Grupos de seguridad de red:** un grupo de seguridad de red puede contener varias reglas de seguridad de entrada y salida que le permiten filtrar el tráfico que llega y sale de los recursos por dirección IP, puerto y protocolo de origen y destino. Para más información, consulte [Grupos de seguridad de red](security-overview.md#network-security-groups).
- **Aplicaciones virtuales de red:** una aplicación de red virtual es una máquina virtual que ejecuta una función de red, como un firewall, la optimización de WAN u otra función de red. Para ver una lista de las aplicaciones virtuales de red que se pueden implementar en una red virtual, consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Enrutado del tráfico de red

De forma predeterminada Azure enruta el tráfico entre subredes, redes virtuales conectadas, redes locales e Internet. Puede implementar una o ambas de las siguientes opciones para reemplazar las rutas predeterminadas que crea Azure:
- **Tablas de ruta:** puede crear tablas de ruta personalizadas con las rutas que controlan a dónde se enruta el tráfico para cada subred. Más información sobre las [tablas de rutas](virtual-networks-udr-overview.md#user-defined).
- **Rutas del protocolo de puerta de enlace de borde (BGP):** si conecta una red virtual a una red local mediante una conexión de Azure VPN Gateway o ExpressRoute, puede propagar las rutas BGP locales a sus redes virtuales. Más información sobre el uso de BGP con [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="connect-virtual-networks"></a>Conexión de redes virtuales

Las redes virtuales se pueden conectar entre sí, lo que permite que los recursos de cualquiera de ellas se comuniquen entre sí mediante el emparejamiento de red virtual. Las redes virtuales que conecte pueden estar en la misma región de Azure o en regiones distintas. Para más información, consulte [Emparejamiento de redes virtuales de Azure](virtual-network-peering-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Ya tiene una visión general de Azure Virtual Network. Para empezar a usar una red virtual, créela, implemente en ella varias máquinas virtuales y establezca comunicación entre las máquinas virtuales. Para aprender a hacerlo, consulte la guía de inicio rápido [Creación de una red virtual](quick-create-portal.md).