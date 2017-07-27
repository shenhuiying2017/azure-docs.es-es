---
title: "Introducción a Azure App Service Environment"
description: "Descripción general breve de Azure App Service Environment"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>Introducción a App Service Environment #
 
## <a name="overview"></a>Información general ##

App Service Environment es una característica de Azure App Service que proporciona un entorno completamente aislado y dedicado para ejecutar de forma segura las aplicaciones de Azure App Service a gran escala. Esta funcionalidad puede hospedar sus instancias de [Web Apps][webapps], [Mobile Apps][mobileapps], [API Apps][APIapps] y [Functions][Functions].

Las instancias de App Service Environment son ideales para cargas de trabajo de aplicaciones que requieren:

- Escala muy grande
- Aislamiento y acceso a redes seguro
- Alto uso de memoria

Los clientes pueden crear varias instancias de App Service Environment en una o varias regiones de Azure. Esto hace que sean perfectos para los niveles de aplicación sin estado de escalado horizontal en el respaldo de cargas de trabajo RPS elevadas.

Los entornos del Servicios de aplicaciones están aislados para ejecutar únicamente las aplicaciones de un solo cliente, y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de las aplicaciones y las aplicaciones pueden establecer conexiones seguras de alta velocidad a los recursos corporativos locales a través de VPN.

Todos los artículos y procedimientos acerca de App Service Environment están disponibles en el archivo [Léame de App Service Environment][ASEReadme].

Para obtener información general del modo en que App Service Environment permite un acceso de red a alta escala y seguro, consulte el [vídeo Deep Dive de AzureCon](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) acerca de App Service Environment.

Para profundizar en el escalado horizontal con varias instancias de App Service Environment, consulte el artículo sobre cómo configurar una [superficie de aplicaciones con distribución geográfica](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/).

Para ver cómo se ha configurado la arquitectura de seguridad mostrada en la inmersión en AzureCon, consulte el artículo sobre la implementación de una [arquitectura de seguridad en capas](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) con entornos del Servicio de aplicaciones.

Las aplicaciones que se ejecutan en entornos de aplicación de servicio pueden tener su acceso validado por dispositivos de subida como firewalls de aplicación web (WAF). En el artículo sobre la [configuración de un WAF para entornos de aplicación de servicio](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) se trata este escenario.

## <a name="dedicated-environment"></a>Entorno dedicado ##

Una instancia de App Service Environment está dedicada exclusivamente a una sola suscripción y puede hospedar 100 instancias. Podrían ser 100 instancias en un único plan de App Service, 100 planes de App Service de una sola instancia o cualquier opción intermedia.

Una instancia de App Service Environment se compone de front-end y trabajos. Los servidores front-end son responsables de la terminación HTTP/HTTPS y del equilibrio de carga automático de la solicitudes de aplicación dentro de una instancia de App Service Environment. Los servidores front-end se agregan automáticamente ya que los planes de App Service en la instancia de App Service Environment se escalan horizontalmente.

Los trabajos son roles que hospedan las aplicaciones del cliente. Los trabajos están disponibles en 3 tamaños fijos:
* 1 núcleo, 3,5 GB de RAM
* 2 núcleos 7 GB de RAM
* 4 núcleos, 14 GB de RAM

Los clientes no necesitan administrar los servidores front-end ni los trabajos. Toda la infraestructura se agrega automáticamente cuando los clientes escalan horizontalmente sus planes de App Service. Cuando se crean o reducen horizontalmente planes de App Service en una instancia de App Service Environment, la infraestructura necesaria se agrega o elimina según corresponda.

Hay una tarifa plana mensual para una instancia de App Service Environment que paga por la infraestructura y no cambia con el tamaño de la instancia de App Service Environment. Adicionalmente, existe un costo por núcleo de plan de App Service. Todas las aplicaciones hospedadas en una instancia de App Service Environment están en el SKU de precios Aislado. Para más información sobre los precios de una instancia de App Service Environment, consulte la página [Precios de App Service][Pricing] y revise las opciones disponibles para App Service Environment.

## <a name="virtual-network-support"></a>Compatibilidad con redes virtuales ##

Una instancia de App Service Environment únicamente puede crearse en una red virtual de Azure Resource Manager. Consulte más información sobre las redes virtuales de Azure aquí: [Preguntas más frecuentes sobre las redes virtuales de Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Puesto que una instancia de App Service Environment reside siempre en una red virtual y, más concretamente, en una subred de una red virtual, puede aprovechar las características de seguridad de las redes virtuales para controlar las comunicaciones de red entrantes y salientes de las aplicaciones.

Una instancia de App Service Environment puede tener conexión a Internet con una dirección IP pública o tener una conexión interna solo con la dirección de un equilibrador de carga interno (ILB) de Azure.

Puede usar [grupos de seguridad de red][NSGs] para restringir las comunicaciones de red entrantes a la subred donde reside el entorno de App Service. Esto le permite ejecutar aplicaciones tras dispositivos y servicios ascendentes, como firewalls de aplicaciones web y proveedores de SaaS de red.

Las aplicaciones, además, suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web. Si la instancia de App Service Environment se implementa en una red virtual de Azure que tiene una conexión VPN a la red local, las aplicaciones de la instancia de App Service Environment podrán tener acceso a los recursos locales. Esto es cierto independientemente de si la red privada virtual es una VPN de [sitio a sitio](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) o de [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Para más información sobre cómo funciona App Service Environment con redes virtuales y redes locales, consulte [Consideraciones de red de App Service Environment][ASENetwork].

## <a name="asev1"></a>ASEv1 ##

App Service Environment tiene dos versiones: ASEv1 y ASEv2. La información anterior se centra en ASEv2. En esta sección se muestran las diferencias entre ASEv1 y ASEv2. 

En ASEv1, debe administrar todos los recursos de forma manual. Esto incluye los front-end, los trabajos y las direcciones IP utilizadas para SSL basada en IP. Antes de escalar horizontalmente el plan de App Service, primero debe escalar horizontalmente el grupo de trabajo en el que desea hospedarlo.

ASEv1 utiliza un modelo de precios diferente al de ASEv2. En ASEv1, debe pagar por cada núcleo asignado. Esto incluye los núcleos usados por servidores front-end y trabajos que no hospedan ninguna carga de trabajo. En ASEv1, el tamaño de escala máxima predeterminada de una instancia de App Service Environment es de 55 hosts en total. Esto incluye servidores front-end y trabajos. Una ventaja de ASEv1 es que se puede implementar en una red virtual clásica, así como en una red virtual de Resource Manager. Puede obtener más información sobre ASEv1 aquí: [Introducción a la versión 1 de App Service Environment][ASEv1Intro].

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

