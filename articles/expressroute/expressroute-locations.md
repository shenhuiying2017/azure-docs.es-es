---
title: 'Proveedores de conectividad y ubicaciones: Azure ExpressRoute | Microsoft Docs'
description: "Este artículo ofrece una introducción detallada de ubicaciones en la que se ofrecen los servicios e información sobre cómo conectarse a regiones de Azure. Ordenado por proveedor de conectividad."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2018
ms.author: pareshmu
ms.openlocfilehash: 9450f509b777c6142e84470bf8e3e69d87fcf145
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="expressroute-partners-and-peering-locations"></a>Asociados de ExpressRoute y ubicaciones de emparejamiento

> [!div class="op_single_selector"]
> * [Ubicaciones por proveedor](expressroute-locations.md)
> * [Proveedores por ubicación](expressroute-locations-providers.md)


Las tablas de este artículo proporcionan información acerca de los proveedores de conectividad de ExpressRoute, la cobertura geográfica de ExpressRoute, los Servicios en la nube de Microsoft compatibles a través de ExpressRoute y los integradores de sistemas de ExpressRoute (SI).

## <a name="partners"></a>Proveedores de conectividad ExpressRoute
ExpressRoute es compatible con todas las ubicaciones y regiones de Azure. El siguiente mapa proporciona una lista de regiones de Azure y ubicaciones de ExpressRoute. Las ubicaciones de ExpressRoute hacen referencia a los emparejamientos de Microsoft con varios proveedores de servicio.

![Mapa de ubicación][0]

Tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolítica si se conectó al menos a una ubicación de ExpressRoute dentro de la región geopolítica.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiones de Azure para ubicaciones de ExpressRoute dentro de una región geopolítica.
La siguiente tabla proporciona un mapa de las regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.

| **Región geopolítica** | **Regiones de Azure** | **Ubicaciones de ExpressRoute** |
| --- | --- | --- |
| **Norteamérica** |Este de EE. UU., oeste de EE. UU., este de EE. UU. 2, oeste de EE.UU. 2, centro de EE. UU., centro-sur de EE. UU., centro-norte de EE. UU., centro-oeste de EE. UU., centro de Canadá y este de Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Ángeles, Miami, Nueva York, San Antonio, Seattle, Silicon Valley, Washington DC, Montreal, Ciudad de Quebec, Toronto |
| **Sudamérica** |Sur de Brasil |São Paulo |
| **Europa** |Europa del Norte, Europa Occidental, Oeste de Reino Unido, Sur de Reino Unido |Ámsterdam, Dublín, Londres, Newport(Wales)+, París |
| **Asia** |Este de Asia y Sudeste de Asia |Hong Kong, Singapur, Singapur2 |
| **Japón** |Oeste de Japón y Este de Japón |Osaka, Tokyo |
| **Australia** |Este de Australia y Sudeste de Australia |Melbourne, Sidney |
| **India** |India occidental, India central, India del Sur |Chennai, Mumbai |
| **Corea del Sur** |Corea Central, Corea del Sur |Busan, Seúl |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiones y límites geopolíticos para nubes nacionales
En la tabla siguiente se proporciona información sobre las regiones y los límites geopolíticos para nubes nacionales.

| **Región geopolítica** | **Regiones de Azure** | **Ubicaciones de ExpressRoute** |
| --- | --- | --- |
| **Nube del gobierno de Estados Unidos** |Arizona Gob. EE. UU., Iowa Gob. EE. UU., Texas Gob. EE.UU., Virginia Gob. EE.UU., US DoD Central, US DoD Este  |Chicago, Dallas, Nueva York, Seattle, Silicon Valley, Washington D.C. |
| **China** |Este de China, Norte de China |Beijing, Shanghai |
| **Alemania** |Centro de Alemania, Alemania oriental |Berlín+, Fráncfort |

No se admite la conectividad entre las regiones geopolíticas en el SKU de ExpressRoute estándar. Debe habilitar el complemento premium de ExpressRoute para admitir conectividad global. No se admite la conectividad con entornos de nube nacionales. Puede trabajar con su proveedor de conectividad si surge tal necesidad.

## <a name="locations"></a>Ubicaciones del proveedor de conectividad

La siguiente tabla muestra las ubicaciones por proveedor de servicios. Si desea ver los proveedores disponibles según la ubicación, consulte [Proveedores de servicios por ubicación](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Azure para producción
| **Proveedor de servicios** | **Microsoft Azure** | **Office 365 y Dynamics 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Compatible |Compatible |Melbourne, Sidney |
| **[Airtel](http://www.airtel.in/business/connexion)** | Compatible | Compatible | Chennai, Mumbai |
| **[Aryaka Networks](http://www.aryaka.com/)** |Compatible |Compatible |Ámsterdam, Dallas, Hong Kong, Silicon Valley, Singapur, Tokio, Washington DC |
| **[Centros de datos de Ascenty](https://ascenty.com/solucoes/conectividade-e-interconexoes/Microsoft-express-route/)** |Próximamente |Próximamente |São Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Compatible |Compatible |Montreal, Toronto, Ciudad de Quebec |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Compatible |Compatible |Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[C3ntro](https://c3ntro.com/)** |Próximamente |Próximamente |Miami |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Próximamente |Próximamente |Silicon Valley |
| **China Telecom Global** |Compatible |No compatible |Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Compatible |Compatible |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Compatible |Compatible |Ámsterdam, Dublín, Londres, París, Tokio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Compatible |Compatible |Chicago, Silicon Valley, Washington DC |
| **[Console](https://www.consoleconnect.com/partners/cloudsaas/)**| Compatible | Compatible |Silicon Valley, Toronto |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Compatible |Compatible |Chicago, Denver, Los Ángeles, Nueva York, Silicon Valley, Washinton DC |
| **Epsilon Global Communications** |Compatible |Compatible |Singapur |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Compatible |Compatible |Ámsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Ángeles, Melbourne, Nueva York, Osaka, París, Sao Paulo, Seattle, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **euNetworks** |Compatible |Compatible |Ámsterdam y Londres |
| **GÉANT** |Compatible |Compatible |Ámsterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Compatible| Compatible | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Compatible |Compatible |Ámsterdam, Londres, París, Singapur, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Compatible |Compatible |Osaka, Tokyo |
| **Internet Solutions - Cloud Connect** |Compatible |Compatible |Ámsterdam y Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Compatible |Compatible |Ámsterdam, Dublín, Londres, París |
| **Jisc** |Compatible |Compatible |Londres |
| **KINX** |Compatible |Compatible |Seúl |
| **[KPN](http://www.kpn.com/cloudconnect)** | Compatible | Compatible | Ámsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Las Vegas, Londres, Newport, Nueva York, San Antonio, Sao Paulo, Seattle, Silicon Valley, Singapur, Tokio, Washington DC |
| **LG CNS** |Compatible |Compatible |Busan, Seúl |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Hong Kong, Las Vegas, Londres, Los Ángeles, Melbourne, Miami, Nueva York, Quebec City, San Antonio, Seattle, Silicon Valley, Singapur, Singapur2, Sidney, Toronto, Washington DC |
| **MTN** |Compatible |Compatible |Londres |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Compatible |Compatible |Miami, Sao Paulo |
| **[Datos de última generación](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Compatible |Compatible |Newport (Gales) |
| **NEXTDC** |Compatible |Compatible |Melbourne, Sidney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Compatible |Compatible |Londres, Los Ángeles, Osaka, Singapur, Tokio, Washington DC |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |Compatible |Compatible |Osaka |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Compatible |Compatible |Ámsterdam, Hong Kong, Londres, París, Silicon Valley, Singapur, Sidney, Washington DC |
| **PCCW Global Limited** |Compatible |Compatible |Hong Kong |
| **Sejong Telecom** |Compatible |Compatible |Seúl |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Compatible |Compatible |Chennai, Mumbai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Compatible |Compatible |Singapur, Singapur2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Compatible |Compatible |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Compatible |Compatible |Ámsterdam, Chennai, Hong Kong, Londres, Mumbai, Silicon Valley, Singapur, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Compatible |Compatible |Ámsterdam, Dublín, Londres |
| **[Telefónica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Compatible |Compatible |Ámsterdam, Sao Paulo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Compatible |Compatible |Londres |
| **Telenor** |Compatible |Compatible |Ámsterdam y Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Compatible |Compatible |Melbourne, Sidney |
| **[Telus](http://www.telus.com)** |Compatible |Compatible |Montreal, Toronto |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |Compatible |Compatible |São Paulo |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Compatible |No compatible |Londres |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas+, Londres+, Los Ángeles, Nueva York, Silicon Valley, Toronto, Washington DC |

 **+** indica próximamente

### <a name="national-cloud-environment"></a>Entorno de nube nacional

### <a name="us-government-cloud"></a>Nube del gobierno de Estados Unidos
| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Compatible |Compatible |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Compatible |Compatible |Chicago, Dallas, Nueva York, Seattle, Silicon Valley, Washington D.C. |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Compatible |Compatible |Chicago, Nueva York+, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible | Compatible | Chicago, Dallas |
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
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Compatible |No compatible |Berlín |
| **Interxion** |Compatible |No compatible |Fráncfort |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible  | No compatible | Berlín |
| **T-Systems** |Compatible |No compatible |Berlín |

## <a name="connectivity-through-exchange-providers"></a>Conectividad a través de proveedores de intercambio

Si su proveedor de conectividad no aparece en la lista de las secciones anteriores, puede crear una conexión.

* Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los intercambios de la tabla anterior. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [Console](https://www.consoleconnect.com/partners/cloudsaas/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Haga que el proveedor de conectividad amplíe su red a la ubicación de emparejamiento que elija.
  * Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
* Solicitar un circuito ExpressRoute con el intercambio como proveedor de conectividad para conectarse a Microsoft.
  * Siga los pasos de la sección [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

## <a name="connectivity-through-additional-service-providers"></a>Conectividad a través de proveedores de servicios adicionales

| **Proveedor de conectividad** | **Exchange** | **Ubicaciones** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nueva York, Washington DC |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokio |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Ámsterdam |
| **[Exponencial E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Ámsterdam |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | Washington DC |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **LGA Telecom** |Equinix |Singapur|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, Nueva York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sidney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Ámsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Ámsterdam, Fráncfort |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Fráncfort |  
| **Rogers** | Cologix y Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londres | 
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Ámsterdam |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Ángeles |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nueva York |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **Zain** |Equinix |Londres|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Nivel 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Conectividad a través de proveedores de centro de datos
| **Proveedor** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[Centros de datos de RagingWire](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | Consola |
| **[Centros de datos de T5](http://t5datacenters.com/network-cloud-connect/)** | Consola |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Conectividad a través de redes nacionales de investigación y educación (NREN)

| **Proveedor**|
| --- |
| **AARNET**| 
| **DeIC, a través de GÉANT**|
| **GARR, a través de GÉANT**|
| **GÉANT**|
| **HEAnet a través de GÉANT**|
| **JISC**|
| **RedIRIS a través de GÉANT**|
| **SINET**|
| **Surfnet a través de GÉANT**|

* Si su proveedor de conectividad no aparece aquí, compruebe si están conectados a alguno de los proveedores de intercambio de ExpressRoute enumerados anteriormente.

## <a name="expressroute-system-integrators"></a>Integradores de sistemas de ExpressRoute
Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistemas que aparecen en la tabla siguiente para ayudarle con la incorporación a ExpressRoute.

| **Integrador de sistemas** | **Continente** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Asia, Europa, Norteamérica y Sudamérica |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Ensyst](http://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Norteamérica |
| **[FlexManage](http://www.flexmanage.com/cloud)** | Norteamérica |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | Europa |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Australia |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemania) |
| **[Nelite](http://nelite.com/)** | Europa |
| **[Firma nueva](https://www.newsignature.co.uk/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Norteamérica |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | Norteamérica |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Europa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |


## <a name="next-steps"></a>pasos siguientes
* Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
* Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"
