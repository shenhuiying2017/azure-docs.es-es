<properties
   pageTitle="Ubicaciones de ExpressRoute | Microsoft Azure"
	description="Este artículo ofrece una introducción detallada de ubicaciones en la que se ofrecen los servicios e información sobre cómo conectarse a regiones de Azure."
	services="expressroute"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor="tysonn"/>
<tags
   ms.service="expressroute"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/06/2015"
	ms.author="cherylmc"/>

# Asociados de ExpressRoute de Azure y ubicaciones de emparejamiento
Las tablas de este artículo proporcionan información acerca de los proveedores de conectividad ExpressRoute (EXP y NSP), la cobertura geográfica de ExpressRoute, los servicios en la nube de Microsoft compatibles a través de ExpressRoute y los integradores de sistemas de ExpressRoute (SI).

## Proveedores de conectividad ExpressRoute
ExpressRoute es compatible con todas las ubicaciones y regiones de Azure. El siguiente mapa proporciona una lista de regiones de Azure y ubicaciones de ExpressRoute. Las ubicaciones de ExpressRoute hacen referencia a los emparejamientos de Microsoft con varios proveedores de servicio.

![](./media/expressroute-locations/expressroute-locations-map.png)

Tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolítica si se conectó al menos a una ubicación de ExpressRoute dentro de la región geopolítica. La siguiente tabla proporciona un mapa de las regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.

|**Región geopolítica**|**Regiones de Azure**|**Ubicaciones de ExpressRoute**|
|---|---|---|
|**EE. UU.**|Todas las regiones de EE. UU.: Este de EE. UU., Oeste de EE. UU., Este de EE. UU. - 2, Centro de EE. UU., Centro y Sur de EE. UU. y Centro y norte de EE. UU.|Atlanta, Chicago, Dallas, Los Ángeles, Nueva York, Seattle, Silicon Valley, Washington DC|
|**USGov**|Iowa, Virginia|Ashburn, Chicago|
|**Sudamérica**|Sur de Brasil|São Paulo|
|**Europa**|Norte de Europa y Oeste de Europa|Ámsterdam y Londres|
|**Asia**|Este de Asia y Sudeste de Asia|Hong Kong y Singapur|
|**Japón**|Oeste de Japón y Este de Japón|Tokio|
|**Australia**|Este de Australia y Sudeste de Australia|Melbourne, Sidney|
|**India**|India occidental, India central, India del Sur|Chennai, Mumbai|

No se admite la conectividad entre las regiones geopolíticas. Puede trabajar con su proveedor de conectividad para ampliar la conectividad entre las regiones geopolíticas a través de su red.


## Ubicaciones del proveedor de Exchange (EXP)

| **Proveedor de servicios** |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Compatible | No compatible | Silicon Valley, Singapur, Washington DC |
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Compatible | No compatible | Ámsterdam y Londres |
| **Comcast** | Compatible | No compatible | Silicon Valley, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Compatible | Próximamente | Ámsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Ángeles, Melbourne, Nueva York, Sao Paulo, Seattle, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[InterCloud](https://www.intercloud.com/)** | Compatible | No compatible | Ámsterdam, Londres, Singapur, Washington DC |
| **Internet Solutions - Cloud Connect** | Compatible | No compatible | Ámsterdam y Londres |
| **Interxion** | Compatible | No compatible | Ámsterdam |
| **[Level 3 Communications - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Compatible | No compatible | Chicago, Dallas, Londres, Seattle, Silicon Valley, Washington DC |
| **NEXTDC** | Compatible | No compatible | Melbourne, Sidney+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Compatible | Próximamente | Ámsterdam y Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Compatible | No compatible | Melbourne+, Sidney |
| **[Zayo Group](http://www.zayo.com/)** | Compatible | No compatible | Washington DC |

 **+** indica próximamente

Consulte [Configuración de la conexión de EXP](expressroute-configuring-exps.md) para conocer los pasos para la configuración de la conexión.

## Ubicaciones de los proveedores de servicios de red (NSP)


| **Proveedor de servicios** |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Compatible | Próximamente | Ámsterdam+, Londres+, Dallas, Silicon Valley, Washington DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Compatible | Próximamente | Ámsterdam, Londres, Silicon Valley+, Washington DC |
|**China Telecom Global** | Próximamente | No compatible | Hong Kong+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Compatible | No compatible | Ámsterdam y Londres |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | Compatible | No compatible | Tokio |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Compatible | No compatible | Chicago, Dallas, Londres, Seattle, Silicon Valley, Washington DC |
| **NTT Communications** | Próximamente | No compatible | Tokio+ |
| **[Orange](http://www.orange-business.com/)** | Compatible | No compatible | Ámsterdam, Londres, Silicon Valley, Washington DC |
| **PCCW Global Limited** | Compatible | No compatible | Hong Kong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Compatible | No compatible | Singapur |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Compatible | Próximamente | Ámsterdam, Chennai+, Hong Kong, Londres, Mumbai+, Singapur |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Compatible | No compatible | Melbourne+, Sidney |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Compatible | No compatible | Londres, Hong Kong, Dallas, Silicon Valley, Washington DC |

 **+** indica próximamente

Consulte [Configuración de la conexión de NSP](expressroute-configuring-nsps.md) para conocer los pasos para la configuración de la conexión.

## Conectividad a través de proveedores de servicios no enumerados

Si su proveedor de conectividad no aparece en la lista de las secciones anteriores, puede crear una conexión.

- Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los proveedores de Exchange en las ubicaciones enumeradas de EXP. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por Proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet de EXP.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Haga que el proveedor de conectividad amplíe su red a la ubicación de Exchange que elija.
	- Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
	- Los proveedores de conectividad (específicamente los proveedores de Ethernet) pueden requerir que adquiera un par de circuitos de intercambios de Ethernet para garantizar una alta disponibilidad.
- Solicite un circuito ExpressRoute a través del proveedor de Exchange para conectarse a Azure.
	- Siga los pasos [Configuración de la conexión de EXP](expressroute-configuring-exps.md) para establecer la conectividad.

|**Proveedor de conectividad**|**Proveedor de Exchange**|**Ubicaciones de emparejamiento**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## Integradores de sistemas de ExpressRoute
Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistemas que aparecen en la tabla siguiente para ayudarle con la incorporación de ExpressRoute.


|**Integrador de sistemas**|**Continente**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Pasos siguientes
- Compruebe que se cumplen los [requisitos previos de ExpressRoute](expressroute-prerequisites.md).
- Revise las [P+G](expressroute-faqs.md) para obtener más información.
- Si desea configurar una conexión de ExpressRoute, consulte [Configuración de la conexión de EXP](expressroute-configuring-exps.md) o [Configuración de la conexión NSP](expressroute-configuring-nsps.md).
- Si desea configurar una conexión VPN sitio a sitio y ExpressRoute para la misma red virtual, consulte [Configuración de conexiones de ExpressRoute y VPN sitio a sitio coexistentes](expressroute-coexist.md).

<!---HONumber=August15_HO9-->