---
title: "Requisitos previos para la adopción de Azure ExpressRoute | Microsoft Docs"
description: "Esta página proporciona una lista de requisitos que deben cumplirse para poder solicitar un circuito ExpressRoute de Azure."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2017
ms.author: cherylmc
ms.openlocfilehash: 8629235511e0dda149ceef6a9c834c3042f64f90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-prerequisites--checklist"></a>Requisitos previos y lista de comprobación de ExpressRoute
Para conectarse a Microsoft Cloud Services con ExpressRoute, debe comprobar que se han cumplido los requisitos enumerados en las secciones siguientes.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Cuenta de Azure
* Una cuenta de Microsoft Azure válida y activa Esta cuenta es necesaria para configurar el circuito ExpressRoute. Los circuitos ExpressRoute son recursos que hay dentro de las suscripciones de Azure. Una suscripción de Azure es un requisito, aunque la conectividad esté limitada a servicios en la nube de Microsoft que no sean Azure, como los servicios de Office 365 y Dynamics 365.
* Una suscripción activa de Office 365 (si usa los servicios de Office 365). Para más información, consulte la sección [Requisitos específicos de Office 365](#office-365-specific-requirements) de este artículo.

## <a name="connectivity-provider"></a>Proveedor de conectividad

* Para conectarse a la nube de Microsoft, puede trabajar con un [asociado de conectividad ExpressRoute](expressroute-locations.md#partners) . Se puede configurar una conexión entre la red local y Microsoft de [tres maneras](expressroute-introduction.md).
* Aunque el proveedor no sea un asociado de conectividad ExpressRoute, puede conectarse a la nube de Microsoft mediante un [proveedor de intercambio de nube](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisitos de red
* **Conectividad redundante**: no hay ningún requisito de redundancia en la conectividad física entre usted y su proveedor. Microsoft requiere que se configuren sesiones BGP redundantes entre enrutadores de Microsoft y enrutadores de emparejamiento, aunque solo tenga [una conexión física a un intercambio de nube](expressroute-faqs.md#onep2plink).
* **Enrutamiento**: según como se conecte a Microsoft Cloud, usted o su proveedor deben configurar y administrar las sesiones BGP para los [dominios de enrutamiento](expressroute-circuit-peerings.md). Algunos proveedores de conectividad Ethernet o proveedores de intercambio en la nube pueden ofrecer administración BGP como un servicio de valor añadido.
* **NAT**: Microsoft solo acepta direcciones IP públicas a través de emparejamiento de Microsoft. Si utiliza direcciones IP privadas en la red local, usted o su proveedor deben convertir las direcciones IP privadas a las direcciones IP públicas [con NAT](expressroute-nat.md).
* **QoS**: Skype Empresarial cuenta con varios servicios (por ejemplo, voz, vídeo o texto) que requieren un tratamiento diferenciado de QoS. Usted y su proveedor deben seguir los [requisitos de QoS](expressroute-qos.md).
* **Seguridad de red**: cuando se conecte a Microsoft Cloud mediante ExpressRoute, tenga en cuenta la [seguridad de red](../best-practices-network-security.md) .

## <a name="office-365"></a>Office 365
Si tiene pensado habilitar Office 365 en ExpressRoute, revise los siguientes documentos para tener más información sobre los requisitos de Office 365.

* [Información general de ExpressRoute para Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Enrutamiento con ExpressRoute para Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Direcciones URL e intervalos de direcciones IP de Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planes de red y ajuste del rendimiento para Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Herramientas y calculadoras de ancho de banda de red](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integración de Office 365 con entornos locales](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Vídeos sobre el aprendizaje avanzado de ExpressRoute en Office 365](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Si tiene pensado habilitar Dynamics 365 en ExpressRoute, revise los siguientes documentos para más información sobre Dynamics 365

* [Documento técnico de Dynamics 365 y ExpressRoute](http://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [Direcciones URL de Dynamics 365](https://support.microsoft.com/kb/2655102) e [Intervalos de direcciones IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
* Encuentre un proveedor de conectividad ExpressRoute. Consulte [Asociados de ExpressRoute de Azure y ubicaciones de emparejamiento](expressroute-locations.md).
* Consulte los requisitos de [enrutamiento](expressroute-routing.md), [NAT](expressroute-nat.md) y [QoS](expressroute-qos.md).
* Configure su conexión ExpressRoute.
  * [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configuración del enrutamiento](expressroute-howto-routing-classic.md)
  * [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md)
