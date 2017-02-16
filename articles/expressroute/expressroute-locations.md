---
title: Ubicaciones de ExpressRoute | Microsoft Docs
description: "Este artículo ofrece una introducción detallada de ubicaciones en la que se ofrecen los servicios e información sobre cómo conectarse a regiones de Azure."
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 25dbeb2c011d4f2b833140d86690d546bfb6825e
ms.openlocfilehash: 45288c24630a4931da9f89196236ab910de735ac


---
# <a name="expressroute-partners-and-peering-locations"></a>Asociados de ExpressRoute y ubicaciones de emparejamiento
Las tablas de este artículo proporcionan información acerca de los proveedores de conectividad de ExpressRoute, la cobertura geográfica de ExpressRoute, los Servicios en la nube de Microsoft compatibles a través de ExpressRoute y los integradores de sistemas de ExpressRoute (SI).

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>Proveedores de conectividad ExpressRoute
ExpressRoute es compatible con todas las ubicaciones y regiones de Azure. El siguiente mapa proporciona una lista de regiones de Azure y ubicaciones de ExpressRoute. Las ubicaciones de ExpressRoute hacen referencia a los emparejamientos de Microsoft con varios proveedores de servicio.

![Mapa de ubicación][0]

Tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolítica si se conectó al menos a una ubicación de ExpressRoute dentro de la región geopolítica.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiones de Azure para ubicaciones de ExpressRoute dentro de una región geopolítica.
La siguiente tabla proporciona un mapa de las regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.

| **Región geopolítica** | **Regiones de Azure** | **Ubicaciones de ExpressRoute** |
| --- | --- | --- |
| **Norteamérica** |Este de EE. UU., oeste de EE. UU., este de EE. UU. 2, oeste de EE.UU. 2, centro de EE. UU., centro-sur de EE. UU., centro-norte de EE. UU., centro-oeste de EE. UU., centro de Canadá y este de Canadá |Atlanta, Chicago, Dallas, Las Vegas, Los Ángeles, Nueva York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto |
| **Sudamérica** |Sur de Brasil |São Paulo |
| **Europa** |Europa del Norte, Europa Occidental, Oeste de Reino Unido, Sur de Reino Unido |Ámsterdam, Dublín, Londres, Newport(Wales)+, París |
| **Asia** |Este de Asia y Sudeste de Asia |Hong Kong y Singapur |
| **Japón** |Oeste de Japón y Este de Japón |Osaka, Tokyo |
| **Australia** |Este de Australia y Sudeste de Australia |Melbourne, Sidney |
| **India** |India occidental, India central, India del Sur |Chennai, Mumbai |
| **Corea del Sur** | Corea Central, Corea del Sur | Busan, Seúl |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiones y límites geopolíticos para nubes nacionales
En la tabla siguiente se proporciona información sobre las regiones y los límites geopolíticos para nubes nacionales.

| **Región geopolítica** | **Regiones de Azure** | **Ubicaciones de ExpressRoute** |
| --- | --- | --- | --- |
| **Nube del gobierno de Estados Unidos** |Iowa Gob. EE. UU., Virginia Gob. EE. UU., centro de US DoD+, este de US DoD+  |Chicago, Dallas, Nueva York, Silicon Valley+, Washington DC |
| **China** |Este de China, Norte de China |Beijing, Shanghai |
| **Alemania** |Centro de Alemania, Alemania oriental |Berlín+, Fráncfort |

No se admite la conectividad entre las regiones geopolíticas en el SKU de ExpressRoute estándar. Debe habilitar el complemento premium de ExpressRoute para admitir conectividad global. No se admite la conectividad con entornos de nube nacionales. Puede trabajar con su proveedor de conectividad si surge tal necesidad.

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>Ubicaciones del proveedor de conectividad
> [!div class="op_single_selector"]
> * [Ubicaciones por proveedor](expressroute-locations.md#locations)
> * [Proveedores por ubicación](expressroute-locations-providers.md#locations)
>
>

### <a name="production-azure"></a>Azure para producción
| **Proveedor de servicios** | **Microsoft Azure** | **Office 365 y CRM Online** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Compatible |Compatible |Melbourne, Sidney |
| **[Aryaka Networks](http://www.aryaka.com/)** |Compatible |Compatible |Ámsterdam, Dallas, Silicon Valley, Singapur, Tokio, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Londres, Silicon Valley, Singapur, Sídney, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Compatible |Compatible |Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Compatible |Compatible |Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Próximamente |Próximamente |Silicon Valley |
| **China Telecom Global** |Compatible |No compatible |Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Compatible |Compatible |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Compatible |Compatible |Ámsterdam, Dublín, Londres, Tokio |
| **Comcast** |Compatible |Compatible |Chicago, Silicon Valley, Washington DC |
| **Console**| Compatible | Compatible |Silicon Valley |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Compatible |Compatible |Los Ángeles, Nueva York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Compatible |Compatible |Ámsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Ángeles, Melbourne, Nueva York, Osaka, París, Sao Paulo, Seattle, Silicon Valley, Singapur, Sidney, Tokio, Toronto, Washington DC |
| **euNetworks** |Compatible |Compatible |Ámsterdam |
| **GÉANT** |Compatible |Compatible |Ámsterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Compatible |Compatible |Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** |Compatible |Compatible |Ámsterdam, Londres, Singapur, Washington DC |
| **Internet Solutions - Cloud Connect** |Compatible |Compatible |Ámsterdam y Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |Compatible |Compatible |Ámsterdam, Londres, París |
| **Jisc** |Compatible |Compatible |Londres |
| **[KPN](http://www.kpn.com/cloudconnect)** | Compatible | Compatible | Ámsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Las Vegas+, Londres, Seattle, Silicon Valley, Singapur, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible |Compatible |Dallas, Hong Kong, Las Vegas, Los Ángeles, Melbourne, Nueva York, Seattle, Singapur, Sidney, Toronto, Washington DC |
| **MTN** |Compatible |Compatible |Londres |
| **[Datos de última generación](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Compatible |Compatible |Newport (Gales) |
| **NEXTDC** |Compatible |Compatible |Melbourne, Sidney |
| **NTT Communications** |Compatible |Compatible |Londres, Los Ángeles, Osaka, Singapur, Tokio, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Compatible |Compatible |Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney y Washington DC |
| **PCCW Global Limited** |Compatible |Compatible |Hong Kong |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Compatible |Compatible |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Compatible |Compatible |Singapur |
| **Softbank** |Compatible |Compatible |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Compatible |Compatible |Ámsterdam, Chennai, Hong Kong, Londres, Mumbai, Silicon Valley, Singapur, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Compatible |Compatible |Ámsterdam, Dublín, Londres |
| **[Telefónica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Compatible |Compatible |São Paulo |
| **Telenor** |Compatible |Compatible |Ámsterdam y Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Compatible |Compatible |Melbourne, Sidney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Compatible |Compatible |Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Compatible |No compatible |Londres |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Compatible |Compatible |Chicago, Los Ángeles, Nueva York, Silicon Valley, Toronto, Washington DC |

 **+** indica próximamente

### <a name="national-cloud-environment"></a>Entorno de nube nacional

### <a name="us-government-cloud"></a>Nube del gobierno de Estados Unidos
| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Compatible |Compatible |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Compatible |Compatible |Chicago, Dallas, Nueva York, Silicon Valley+, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Compatible |Compatible |Chicago, Nueva York +, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible | Compatible | Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Compatible |Compatible |Chicago, Dallas, Nueva York, Washington DC |

### <a name="china"></a>China
| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **China Telecom** |Compatible |No compatible |Beijing, Shanghai |

Para obtener más información, consulte [ExpressRoute en China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemania
| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Compatible |No compatible |Berlín+, Fráncfort |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Compatible |No compatible |Fráncfort |
| **e-shelter** |Compatible |No compatible |Berlín |
| **Interxion** |Compatible |No compatible |Fráncfort |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible  | No compatible | Berlín+ |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>Conectividad a través de proveedores de servicios no enumerados
Si su proveedor de conectividad no aparece en la lista de las secciones anteriores, puede crear una conexión.

* Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los intercambios de la tabla anterior. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Haga que el proveedor de conectividad amplíe su red a la ubicación de emparejamiento que elija.
  * Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
* Solicitar un circuito ExpressRoute con el intercambio como proveedor de conectividad para conectarse a Microsoft.
  * Siga los pasos de la sección [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

| **Proveedor de conectividad** | **Exchange** | **Ubicaciones** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokio |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Ámsterdam |
| **[Exponencial E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |Nueva York, Washington DC |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sidney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Ámsterdam, Fráncfort |  
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Ángeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Fráncfort |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Silicon Valley, Washington DC |
| **[XO Communications](http://www.xo.com/)** |Equinix |Silicon Valley |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Nivel 3 | Madrid |


## <a name="expressroute-system-integrators"></a>Integradores de sistemas de ExpressRoute
Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistemas que aparecen en la tabla siguiente para ayudarle con la incorporación a ExpressRoute.

| **Integrador de sistemas** | **Continente** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** |Asia, Europa, Estados Unidos |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** |Europa |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** |US |
| **[IT Consultancy](http://itconsult.com.au/microsoft-expressroute)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemania) |
| **[Nelite](http://nelite.com/)** |Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** |Asia |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** |US |
| **[Project Leadership](http://www.projectleadership.net/azure)** |US |


## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
* Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"



<!--HONumber=Jan17_HO2-->


