<properties 
	pageTitle="Introducción al entorno del Servicio de aplicaciones" 
	description="Obtenga información sobre la característica de entorno del Servicio de aplicaciones que proporciona unidades de escalado dedicadas y seguras unidas en redes virtuales para ejecutar todas sus aplicaciones." 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="stefsh"/>

# Introducción al entorno del Servicio de aplicaciones

## Información general ##
Un entorno del Servicio de aplicaciones es una opción de plan de servicio [Premium][PremiumTier] del Servicio de aplicaciones de Azure que proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura todas las aplicaciones. Esto incluye las [Aplicaciones web][WebApps], [Aplicaciones móviles][MobileApps], [Aplicaciones de API][APIApps] y [Aplicaciones lógicas][LogicApps] con opciones de escalado ampliadas.

Los recursos de proceso de un entorno del Servicio de aplicaciones se dedican exclusivamente a ejecutar sus aplicaciones. Un entorno de este tipo se crea siempre en una red virtual regional, lo que ofrece a las aplicaciones nuevas opciones para el aislamiento de red. Además, un entorno del Servicio de aplicaciones admite opciones de escalado adicionales, con hasta cincuenta (50) recursos de proceso disponibles para ejecutar las aplicaciones. Fuera de un entorno del Servicio de aplicaciones hay un límite de 20 recursos de proceso para hospedar las aplicaciones.

## Compatibilidad con redes virtuales ##
Un entorno del Servicio de aplicaciones puede crearse en una red virtual regional existente o bien en una nueva ([más información sobre redes virtuales][MoreInfoOnVirtualNetworks]). Puesto que los entornos de este tipo residen siempre en una red virtual regional y, más concretamente, en una subred de una red virtual regional, puede aprovechar las características de seguridad de las redes virtuales para controlar las comunicaciones de red entrantes y salientes.

Puede usar [grupos de seguridad de red][NetworkSecurityGroups] para restringir las comunicaciones de red entrantes a la subred donde reside el entorno del Servicio de aplicaciones. Esto le permite ejecutar aplicaciones tras dispositivos y servicios ascendentes, como firewalls de aplicaciones web y proveedores de SaaS de red.

Las aplicaciones, además, suelen requerir acceso a recursos corporativos, como bases de datos internas y servicios web. Un enfoque común consiste en poner estos extremos a disposición únicamente del flujo de tráfico de red interno de una red virtual de Azure. Una vez que un entorno del Servicio de aplicaciones se une a la misma red virtual que los servicios internos, las aplicaciones que se ejecutan en el entorno pueden tener acceso a ellos, incluidos los extremos accesibles mediante conexiones [De sitio a sitio][SiteToSite] y [Azure ExpressRoute][ExpressRoute].

## Recursos de proceso dedicados ##
Todos los recursos de proceso de un entorno del Servicio de aplicaciones están dedicados exclusivamente a una sola suscripción. Un entorno de este tipo se compone de un solo grupo de recursos de proceso front-end y de entre uno a tres grupos de recursos de proceso de trabajo.

El grupo de servidores front-end contiene los recursos de proceso responsables de la terminación SSL y del equilibrio de carga automático de las solicitudes de aplicación dentro de un entorno del Servicio de aplicaciones.

Cada grupo de trabajo contiene recursos de proceso asignados a [Planes del Servicio de aplicaciones][AppServicePlan] que, a su vez, contienen una o más aplicaciones del Servicio de aplicaciones de Azure. Como en un entorno del Servicio de aplicaciones puede haber hasta tres grupos de trabajo distintos, tiene la flexibilidad de elegir diferentes recursos de proceso para cada grupo de trabajo.

Esto le permite, por ejemplo, crear un grupo de trabajo con recursos de proceso de menor eficacia para los planes del Servicio de aplicaciones destinados a aplicaciones de prueba o desarrollo. Un segundo (o incluso tercer) grupo de trabajo podría usar recursos de proceso más eficaces en los planes del Servicio de aplicaciones que ejecuten aplicaciones de producción.

Los entornos del Servicio de aplicaciones pueden configurarse con un máximo de cincuenta (50) recursos de proceso en un mismo grupo de trabajo. Para obtener más detalles sobre la cantidad de recursos de proceso disponibles para los grupos de servidores front-end y los grupos de trabajo, consulte [Configuración de un entorno del Servicio de aplicaciones][HowToConfigureanAppServiceEnvironment].

Para obtener más detalles sobre los tamaños de los recursos de proceso disponibles admitidos en un entorno del Servicio de aplicaciones, consulte la página de [Precios de Servicio de aplicaciones][AppServicePricing] y revise las opciones disponibles para este tipo de entornos en el nivel de precios Premium.


## Introducción

Para empezar a trabajar con los entornos del Servicio de aplicaciones, vea [Creación de un entorno del Servicio de aplicaciones][HowToCreateAnAppServiceEnvironment].

Para obtener más información acerca de la plataforma de Servicio de aplicaciones de Azure, consulte [Servicio de aplicaciones de Azure][AzureAppService].

Para obtener información general sobre la arquitectura de red del Entorno del Servicio de aplicaciones, consulte el artículo [Información general sobre la arquitectura de red][NetworkArchitectureOverview].

Para obtener información detallada sobre el uso de un entorno del Servicio de aplicaciones con ExpressRoute, consulte el siguiente artículo sobre [ExpressRoute y los entornos del Servicio de aplicaciones][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=August15_HO6-->