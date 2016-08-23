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
   ms.date="07/28/2016"
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
|**Europa**|Norte de Europa y Oeste de Europa|Ámsterdam, Dublín, Londres, Newport(Wales)+, París|
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
| **Ubicación** | **Proveedores de servicios** |
|---------------|-----------------------|
| **Ámsterdam** | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT+, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor y Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Tata Communications |
| **Chicago** | AT&T NetBond, Comcast, Equinix, Level 3 Communications y Zayo Group |
| **Dallas** | AT&T NetBond, Equinix, Level 3 Communications y Megaport |
| **Dublín** | Colt |
| **Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications y Verizon |
| **Londres** | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon y Vodafone |
| **Las Vegas** | Level 3 Communications+ y Megaport
| **Los Ángeles** | CoreSite, Equinix, Megaport, NTT y Zayo Group |
| **Melbourne** | Equinix, Megaport, NEXTDC y Telstra Corporation |
| **Nueva York** | Equinix, Megaport y Zayo Group |
| **Montreal** | Cologix+ |
| **Mumbai (Bombay)** | Tata Communications |
| **Osaka** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications y Softbank |
| **París** | Interxion |
| **São Paulo** | Equinix y Telefonica+ |
| **Seattle** | Equinix, Level 3 Communications y Megaport |
| **Silicon Valley** | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon y Zayo Group |
| **Singapur** | Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications y Verizon |
| **Sidney** | AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Telstra Corporation y Verizon |
| **Tokio** | Aryaka Networks, British Telecom, Colt, Equinix, Internet iniciativa Japón Inc. - IIJ, NTT comunicaciones, Softbank y Verizon |
| **Toronto** | Cologix, Equinix y Zayo Group |
| **Washington DC** | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon y Zayo Group |

 **+** indica próximamente

### Entornos de nube nacionales

#### Nube del gobierno de Estados Unidos

| **Ubicación** |**Proveedores de servicios** |
|---------------|--------------------|
| **Chicago** | AT&T NetBond, Equinix, Level 3 Communications y Verizon |
| **Dallas** | Equinix+ y Verizon+ |
| **Nueva York** | Equinix, Level 3 Communications+ y Verizon |
| **Washington DC** | AT&T NetBond, Equinix, Level 3 Communications y Verizon |

#### China

| **Ubicación** | **Proveedores de servicios** |
|---------------|-----------------------|
| **Beijing** | China Telecom |
| **Shanghai** | China Telecom |
Para más información, consulte [ExpressRoute en China](http://www.windowsazure.cn/home/features/expressroute/).

#### Alemania

| **Ubicación** | **Proveedores de servicios** |
|---------------|-----------------------|
| **Berlín** | Colt+ y e-shelter+ |
| **Fráncfort** | Colt, Equinix+ e Interxion |

## <a name="nonpartners"></a>Conectividad a través de proveedores de servicios no enumerados

Si su proveedor de conectividad no aparece en la lista de las secciones anteriores, puede crear una conexión.

- Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los intercambios de la tabla anterior. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet.

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- Haga que el proveedor de conectividad amplíe su red a la ubicación de emparejamiento que elija.
	- Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
- Solicitar un circuito ExpressRoute con el intercambio como proveedor de conectividad para conectarse a Microsoft.
	- Siga los pasos de la sección [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

|**Ubicación**|**Exchange**|**Proveedores de conectividad**|
|-------------|------------|-------------------------|
| **Nueva York** | Equinix | Lightower |
| **Seattle** | Equinix | Alaska Communications |
| **Silicon Valley** | Equinix | XO Communications |
| **Singapur** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## Integradores de sistemas de ExpressRoute

Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistemas que aparecen en la tabla siguiente para ayudarle con la incorporación a ExpressRoute.

|**Continente**|**Integradores de sistemas**|
|-------------|---------------------|
| **Asia** | Avanade Inc. y OneAs1a|
| **Europa** | Avanade Inc. y Dotnet Solutions|
| **EE. UU.** | Avanade Inc., Equinix Professional Services, Perficient y Project Leadership|

## Pasos siguientes

- Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
- Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"

<!---HONumber=AcomDC_0817_2016-->