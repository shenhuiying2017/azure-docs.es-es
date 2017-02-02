---
title: App Service Environment | Microsoft Docs
description: "¿Qué es un Entorno del Servicio de aplicaciones de Azure? Introducción al Entorno del Servicio de aplicaciones"
keywords: entorno del servicio de aplicaciones de Azure, red virtual, redes seguras
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: df2fa1d0790f8942a0350f9734232cc4b25cadc1
ms.openlocfilehash: a54f559801e1a4f4b752bc2268ea8d6cb20b1267


---
# <a name="app-service-environment-documentation"></a>Documentación del Entorno del Servicio de aplicaciones
Un entorno de App Service es una opción de plan de servicio [Premium][PremiumTier] de Azure App Serviceque proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones de Azure App Service a gran escala, lo que incluye [Web Apps][WebApps], [Mobile Apps][MobileApps] y [API Apps][APIApps].  

Los entornos del Servicio de aplicaciones son ideales para cargas de trabajo de aplicaciones que requieren:

* Muy grande escala
* Aislamiento y acceso a redes seguro

Los clientes pueden crear varios entornos del Servicio de aplicaciones en una o varias regiones de Azure.  Esto hace que sean perfectos para los niveles de aplicación sin estado de escalado horizontal en el respaldo de cargas de trabajo RPS elevadas.

Los entornos del Servicios de aplicaciones están aislados para ejecutar únicamente las aplicaciones de un solo cliente, y siempre se implementan en una red virtual.  Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de la aplicación si usan [grupos de seguridad de red][NetworkSecurityGroups].  Las aplicaciones también pueden establecer conexiones seguras a alta velocidad por redes virtuales a los recursos corporativos locales.

Las aplicaciones suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web.  Las aplicaciones que se ejecutan en entornos de App Service pueden tener acceso a los recursos accesibles mediante conexiones VPN [de sitio a sitio][SiteToSite] y [Azure ExpressRoute][ExpressRoute].

* [¿Qué es un entorno del Servicio de aplicaciones?](../app-service-web/app-service-app-service-environment-intro.md)
* [Creación de un Entorno del Servicio de aplicaciones](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creación de aplicaciones en un Entorno del Servicio de aplicaciones](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Creación y uso de un equilibrador de carga interno con entornos del Servicio de aplicaciones](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configuración de un entorno del Servicio de aplicaciones](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Escalado de aplicaciones en un Entorno del Servicio de aplicaciones](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Arquitectura y seguridad de red](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Procedimientos
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>Vídeos
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/



<!--HONumber=Dec16_HO1-->


