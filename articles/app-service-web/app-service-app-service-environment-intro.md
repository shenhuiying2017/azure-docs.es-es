---
title: "Introducción al entorno del Servicio de aplicaciones"
description: "Obtenga información sobre la característica de entorno del Servicio de aplicaciones que proporciona unidades de escalado dedicadas y seguras unidas en redes virtuales para ejecutar todas sus aplicaciones."
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 91b1d6315a9414789b28442f3f19d14c2aed8f00
ms.contentlocale: es-es
ms.lasthandoff: 03/01/2017


---
# <a name="introduction-to-app-service-environment"></a>Introducción al entorno del Servicio de aplicaciones
## <a name="overview"></a>Información general
Un entorno de App Service es una opción de plan de servicio [Premium][PremiumTier] de Azure App Serviceque proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones de Azure App Service a gran escala, lo que incluye [Web Apps][WebApps], [Mobile Apps][MobileApps] y [API Apps][APIApps].  

Los entornos del Servicio de aplicaciones son ideales para cargas de trabajo de aplicaciones que requieren:

* Muy grande escala
* Aislamiento y acceso a redes seguro

Los clientes pueden crear varios entornos del Servicio de aplicaciones en una o varias regiones de Azure.  Esto hace que sean perfectos para los niveles de aplicación sin estado de escalado horizontal en el respaldo de cargas de trabajo RPS elevadas.

Los entornos del Servicios de aplicaciones están aislados para ejecutar únicamente las aplicaciones de un solo cliente, y siempre se implementan en una red virtual.  Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de las aplicaciones, y las aplicaciones pueden establecer conexiones seguras de alta velocidad a los recursos corporativos locales a través de redes virtuales.

Todos los artículos y procedimientos acerca de los entornos del Servicio de aplicaciones están disponibles en el archivo [Léame para entornos del Servicio de aplicaciones](../app-service/app-service-app-service-environments-readme.md).

Para obtener información general del modo en que los entornos de App Service permiten el acceso de red a alta escala y seguro, consulte el [vídeo Deep Dive de AzureCon][AzureConDeepDive] sobre los entornos de App Service.

Para profundizar en el escalado horizontal con varios entornos de App Service, consulte el artículo sobre cómo configurar una [superficie de aplicaciones con distribución geográfica][GeodistributedAppFootprint].

Para ver cómo se ha configurado la arquitectura de seguridad mostrada en la inmersión en AzureCon, consulte el artículo sobre la implementación de una [arquitectura de seguridad en capas](app-service-app-service-environment-layered-security.md) con entornos del Servicio de aplicaciones.

Las aplicaciones que se ejecutan en entornos de aplicación de servicio pueden tener su acceso validado por dispositivos de subida como firewalls de aplicación web (WAF).  En el artículo sobre la [configuración de un WAF para entornos de aplicación de servicio](app-service-app-service-environment-web-application-firewall.md) se trata este escenario. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Recursos de proceso dedicados
Todos los recursos de proceso de un entorno del Servicio de aplicaciones están dedicados exclusivamente a una sola suscripción, y se puede configurar un entorno del Servicio de aplicaciones con hasta cincuenta recursos de proceso (50) para uso exclusivo de una única aplicación.

Un entorno de este tipo se compone de un grupo de recursos de proceso front-end y de entre uno a tres grupos de recursos de proceso de trabajo. 

El grupo de servidores front-end contiene los recursos de proceso responsables de la terminación SSL y del equilibrio de carga automático de las solicitudes de aplicación dentro de un entorno del Servicio de aplicaciones. 

Cada grupo de trabajo contiene recursos de proceso asignados a [Planes de App Service][AppServicePlan] que, a su vez, contienen una o más aplicaciones de Azure App Service.  Como en un entorno del Servicio de aplicaciones puede haber hasta tres grupos de trabajo distintos, tiene la flexibilidad de elegir diferentes recursos de proceso para cada grupo de trabajo.  

Esto permite, por ejemplo, crear un grupo de trabajo con recursos de proceso de menor eficacia para los planes del Servicio de aplicaciones destinados a aplicaciones de prueba o desarrollo.  Un segundo (o incluso tercer) grupo de trabajo podría usar recursos de proceso más eficaces en los planes del Servicio de aplicaciones que ejecuten aplicaciones de producción.

Para obtener más detalles sobre la cantidad de recursos de proceso disponibles para los grupos de servidores front-end y los grupos de trabajo, consulte el artículo de [configuración de un entorno de App Service][HowToConfigureanAppServiceEnvironment].  

Para obtener más detalles sobre los tamaños de los recursos de proceso disponibles admitidos en un entorno de App Service, consulte la página de [precios de App Service][AppServicePricing] y revise las opciones disponibles para este tipo de entornos en el plan de tarifa Premium.

## <a name="virtual-network-support"></a>Compatibilidad con redes virtuales
Puede crearse un entorno de App Service en**** una red virtual de Azure Resource Manager **o** en una del modelo de implementación clásica ([obtenga más información sobre las redes virtuales][MoreInfoOnVirtualNetworks]).  Puesto que los entornos de este tipo residen siempre en una red virtual y, más concretamente, en una subred de una red virtual, puede aprovechar las características de seguridad de las redes virtuales para controlar las comunicaciones de red entrantes y salientes.  

Una instancia de App Service Environment puede tener conexión a Internet con una dirección IP pública, o tener una conexión interna solo con la dirección de un equilibrador de carga interno (ILB) de Azure.

Puede usar [grupos de seguridad de red][NetworkSecurityGroups] para restringir las comunicaciones de red entrantes a la subred donde reside el entorno de App Service.  Esto le permite ejecutar aplicaciones tras dispositivos y servicios ascendentes, como firewalls de aplicaciones web y proveedores de SaaS de red.

Las aplicaciones, además, suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web.  Un enfoque común consiste en poner estos extremos a disposición únicamente del flujo de tráfico de red interno de una red virtual de Azure.  Una vez que un entorno de App Service se une a la misma red virtual que los servicios internos, las aplicaciones que se ejecutan en el entorno pueden tener acceso a ellos, incluidos los puntos de conexión accesibles mediante conexiones [De sitio a sitio][SiteToSite] y [Azure ExpressRoute][ExpressRoute].

Para obtener más detalles sobre cómo funcionan los entornos de App Service con redes virtuales y locales, consulte los siguientes artículos sobre [información general sobre la arquitectura de red de los entornos de App Service][NetworkArchitectureOverview], de [cómo controlar el tráfico de entrada a un entorno de App Service][ControllingInboundTraffic] y sobre [conexión segura a los recursos de back-end desde un entorno de App Service][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introducción
Para empezar a trabajar con los entornos de App Service, consulte el artículo sobre [creación de un entorno de App Service][HowToCreateAnAppServiceEnvironment].

Todos los artículos y procedimientos para los entornos del Servicio de aplicaciones están disponibles en el archivo [Léame para entornos del Servicio de aplicaciones](../app-service/app-service-app-service-environments-readme.md).

Para obtener más información sobre la plataforma Azure App Service, consulte [Azure App Service][AzureAppService].

Para obtener información general sobre la arquitectura de red del entorno de App Service, consulte el artículo de [información general sobre la arquitectura de red de los entornos de App Service][NetworkArchitectureOverview].

Para obtener información detallada sobre el uso de un entorno de App Service con ExpressRoute, consulte el siguiente artículo sobre [detalles de configuración de red para entornos de App Service con ExpressRoute][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->



