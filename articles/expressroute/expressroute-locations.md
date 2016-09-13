<properties
   pageTitle="Ubicaciones de ExpressRoute | Microsoft Azure"
   description="Este artículo ofrece una introducción detallada de ubicaciones en la que se ofrecen los servicios e información sobre cómo conectarse a regiones de Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/07/2016"
   ms.author="cherylmc" />

# Asociados de ExpressRoute y ubicaciones de emparejamiento

Las tablas de este artículo proporcionan información acerca de los proveedores de conectividad de ExpressRoute, la cobertura geográfica de ExpressRoute, los Servicios en la nube de Microsoft compatibles a través de ExpressRoute y los integradores de sistemas de ExpressRoute (SI).

## <a name="partners"></a>Proveedores de conectividad ExpressRoute

ExpressRoute es compatible con todas las ubicaciones y regiones de Azure. El siguiente mapa proporciona una lista de regiones de Azure y ubicaciones de ExpressRoute. Las ubicaciones de ExpressRoute hacen referencia a los emparejamientos de Microsoft con varios proveedores de servicio.

![Mapa de ubicación][0]

Tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolítica si se conectó al menos a una ubicación de ExpressRoute dentro de la región geopolítica. La siguiente tabla proporciona un mapa de las regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.

|**Región geopolítica**|**Regiones de Azure**|**Ubicaciones de ExpressRoute**|
|---|---|---|
|**Norteamérica**|Este de EE. UU., Oeste de EE. UU., Este de EE. UU. 2, Centro de EE. UU., Centro y Sur de EE. UU., Centro y norte de EE. UU., Centro de Canadá y Este de Canadá.|Atlanta, Chicago, Dallas, Las Vegas+, Los Ángeles, Nueva York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto|
|**Sudamérica**|Sur de Brasil|São Paulo|
|**Europa**|Europa del Norte, Europa Occidental, Oeste de Reino Unido, Sur de Reino Unido|Ámsterdam, Dublín, Londres, Newport(Wales)+, París|
|**Asia**|Este de Asia y Sudeste de Asia|Hong Kong y Singapur|
|**Japón**|Oeste de Japón y Este de Japón|Osaka, Tokyo|
|**Australia**|Este de Australia y Sudeste de Australia|Melbourne, Sidney|
|**India**|India occidental, India central, India del Sur|Chennai, Mumbai|



En la tabla siguiente se proporciona información sobre las regiones y los límites geopolíticos para nubes nacionales.

|**Región geopolítica**|**Regiones de Azure**|**Ubicaciones de ExpressRoute**|
|---|---|---|---|
|**Nube del gobierno de Estados Unidos**|Gobierno de EE. UU. - Iowa, Gobierno de EE. UU. - Virginia|Chicago, Dallas+, Nueva York, Washington DC|
|**China**|Este de China, Norte de China|Beijing, Shanghai|
|**Alemania**|Centro de Alemania, Alemania oriental|Berlín+, Fráncfort|


No se admite la conectividad entre las regiones geopolíticas en el SKU de ExpressRoute estándar. Debe habilitar el complemento premium de ExpressRoute para admitir conectividad global. No se admite la conectividad con entornos de nube nacionales. Puede trabajar con su proveedor de conectividad si surge tal necesidad.


## Ubicaciones de proveedor de conectividad

> [AZURE.SELECTOR]
[Locations By Provider](expressroute-locations.md#connectivity-provider-locations)
[Providers By Location](expressroute-locations-providers.md#connectivity-provider-locations)

### Azure para producción

| **Proveedor de servicios** |**Microsoft Azure** | **Office 365 y CRM Online** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Compatible | Compatible | Ámsterdam, Silicon Valley, Singapur, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Compatible | Compatible | Ámsterdam, Chicago, Dallas, Londres, Silicon Valley, Singapur, Sídney, Washington DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Compatible | Compatible | Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
|**CenturyLink** | Próximamente | Próximamente| Silicon Valley |
|**China Telecom Global** | Compatible | No compatible | Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Compatible | Próximamente | Montreal+, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Compatible | Compatible | Ámsterdam, Dublín, Londres, Tokio |
| **Comcast** | Compatible | Compatible | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Compatible | Compatible | Los Ángeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Compatible | Compatible | Ámsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Ángeles, Melbourne, Nueva York, Osaka, Sao Paulo, Seattle, Silicon Valley, Singapur, Sidney, Tokio, Toronto, Washington DC |
| **euNetworks** | Compatible | Compatible | Ámsterdam |
| **GÉANT** | Próximamente | Próximamente | Ámsterdam+ |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** | Compatible | Compatible | Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** | Compatible | Compatible | Ámsterdam, Londres, Singapur, Washington DC |
| **Internet Solutions - Cloud Connect** | Compatible | Compatible | Ámsterdam y Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** | Compatible | Compatible | Ámsterdam, Londres, París |
| **Jisc** | Próximamente | Próximamente | Londres+ | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Compatible | Compatible | Ámsterdam, Chicago, Dallas, Las Vegas+, Londres, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Compatible | Compatible | Dallas, Hong Kong, Las Vegas, Los Ángeles, Melbourne, Nueva York, Seattle, Singapur, Sidney, Washington DC |
| **MTN** | Compatible | Compatible | Londres |
| **NEXTDC** | Compatible | Compatible | Melbourne, Sidney |
| **NTT Communications** | Compatible | Compatible | Londres, Los Ángeles, Osaka, Tokio |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** | Compatible | Compatible | Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney y Washington DC |
| **PCCW Global Limited** | Compatible | Compatible | Hong Kong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Compatible | Compatible | Singapur |
| **Softbank** | Compatible | Compatible | Osaka, Tokyo | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Compatible | Compatible | Ámsterdam, Chennai, Hong Kong, Londres, Mumbai, Silicon Valley, Singapur, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Compatible | Compatible | Ámsterdam y Londres |
| **Telefónica** | Compatible | Próximamente | São Paulo |
| **Telenor** | Compatible | Compatible | Ámsterdam y Londres |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Compatible | Próximamente | Melbourne, Sidney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Compatible | Compatible | Ámsterdam, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **Vodafone** | Compatible | No compatible | Londres | 
| **[Zayo Group](http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Compatible | Compatible | Chicago, Los Ángeles, Nueva York, Silicon Valley, Toronto, Washington DC |

 **+** indica próximamente

### Entornos de nube nacionales

#### Nube del gobierno de Estados Unidos

| **Proveedor de servicios** |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Compatible | Compatible | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Compatible | Compatible | Chicago, Dallas+, Nueva York, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Compatible | Compatible | Chicago, Nueva York +, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Compatible | Compatible | Chicago, Dallas+, Nueva York, Washington DC |

#### China

| **Proveedor de servicios** |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Compatible | No compatible | Beijing, Shanghai|
Para obtener más información, consulte [ExpressRoute en China](http://www.windowsazure.cn/home/features/expressroute/).

#### Alemania

| **Proveedor de servicios** |**Microsoft Azure** | **Office 365** | **Ubicaciones** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Compatible | No compatible | Berlín+, Fráncfort|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Próximamente | No compatible | Fráncfort+|
| **e-shelter** | Próximamente | No compatible | Berlín+|
| **Interxion** | Compatible | No compatible | Fráncfort|

## <a name="nonpartners"></a>Conectividad a través de proveedores de servicios no enumerados

Si su proveedor de conectividad no aparece en la lista de las secciones anteriores, puede crear una conexión.

- Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los intercambios de la tabla anterior. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet.

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- Haga que el proveedor de conectividad amplíe su red a la ubicación de emparejamiento que elija.
	- Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
- Solicitar un circuito ExpressRoute con el intercambio como proveedor de conectividad para conectarse a Microsoft.
	- Siga los pasos de la sección [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

|**Proveedor de conectividad**|**Exchange**|**Ubicaciones**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapur|
|**Alaska Communications**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)**|Equinix|Nueva York, Washington DC|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## Integradores de sistemas de ExpressRoute

Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistemas que aparecen en la tabla siguiente para ayudarle con la incorporación a ExpressRoute.

|**Integrador de sistemas**|**Continente**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asia, Europa, Estados Unidos |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|US|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | US |
|**[Project Leadership](http://www.projectleadership.net/azure)** | US |

## Pasos siguientes

- Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
- Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"

<!---HONumber=AcomDC_0907_2016-->