<properties
   pageTitle="Ubicaciones de ExpressRoute"
   description="Esta página ofrece una introducción detallada de ubicaciones en la que se ofrecen los servicios e información sobre cómo conectarse a regiones de Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# Asociados de ExpressRoute y ubicaciones de emparejamiento
Esta tabla proporciona información acerca de:

1. Los proveedores de conectividad ExpressRoute (EXP y NSP)
2. La cobertura geográfica de ExpressRoute
3. Los servicios en la nube de Microsoft compatibles a través de ExpressRoute
4. Integradores de sistemas de ExpressRoute (SI)

## Proveedores de conectividad ExpressRoute
ExpressRoute es compatible con todas las ubicaciones y regiones de Azure. El mapa siguiente proporciona una lista de regiones de Azure y ubicaciones de ExpressRoute. Las ubicaciones de ExpressRoute hacen referencia a los emparejamientos de Microsoft con varios proveedores de servicio.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolítica si se conectó al menos a una ubicación de ExpressRoute dentro de la región geopolítica. La siguiente tabla proporciona un mapa de las regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.

|**Región geopolítica**|**Regiones de Azure**|**Ubicaciones de ExpressRoute**|
|---|---|---|
|**EE. UU.**|Todas las regiones de EE. UU.: Oeste de EE. UU., Este de EE. UU., Centro de EE. UU., Centro sur de EE. UU. y Centro norte de EE. UU.|Atlanta, Chicago, Dallas, Los Ángeles, Nueva York, Seattle, Silicon Valley, Washington DC|
|**Sudamérica**|Sur de Brasil|São Paulo|
|**Europa**|Norte de Europa y Oeste de Europa|Ámsterdam y Londres|
|**Asia**|Este de Asia y Sudeste de Asia|Hong Kong y Singapur|
|**Japón**|Oeste de Japón y Este de Japón|Tokio|
|**Australia**|Este de Australia y Sudeste de Australia|Sidney|

No se admite la conectividad entre las regiones geopolíticas. Puede trabajar con su proveedor de conectividad para ampliar la conectividad entre las regiones geopolíticas a través de su red.


### Ubicaciones del proveedor (EXP) de Exchange
- Consulte esta [tabla](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP) para obtener una lista de proveedores de Exchange y las ubicaciones en las que son compatibles.
-  Visite [Configuración de la conexión de EXP](expressroute-configuring-exps.md) para conocer los pasos para la configuración de la conexión.

### Ubicaciones del proveedor de servicios de red (NSP)
- Consulte esta [tabla](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP) para obtener una lista de proveedores de servicios de red y las ubicaciones en las que son compatibles.
- Visite [Configuración de la conexión de NSP](expressroute-configuring-nsps.md) para conocer los pasos para la configuración de la conexión.

### Conectividad a través de proveedores de servicios no enumerados anteriormente

Si su proveedor de conectividad no está en la lista de las secciones anteriores, puede crear una conexión.

- Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los proveedores de Exchange en las ubicaciones enumeradas de EXP. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por los proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet de EXP.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Haga que el proveedor de conectividad amplíe su red a la ubicación de Exchange que elija.
	- Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
	- Los proveedores de conectividad (específicamente los proveedores de Ethernet) pueden requerir que adquiera un par de circuitos de intercambios de Ethernet para garantizar una alta disponibilidad. 
- Solicite un circuito ExpressRoute a través del proveedor de Exchange para conectarse a Azure.
	- Siga los pasos [Configuración de la conexión de EXP](expressroute-configuring-exps.md) para establecer la conectividad.

|**Proveedor de conectividad**|**Proveedores de Exchange**|**Ubicaciones de emparejamiento**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## ExpressRoute y Servicios en la nube de Microsoft
Las tablas siguientes proporcionan detalles sobre los proveedores de conectividad y la lista de servicios en la nube de Microsoft con las que son compatibles. Póngase en contacto con su proveedor de servicios.

**Proveedores de Exchange (EXP)**

|**Proveedor de servicios**|**Servicios de Microsoft Azure**|**Servicios de Office 365**|
|---|---|---|
|**Aryaka**|Compatible||
|**Colt Ethernet**|Compatible||
|Equinix|Compatible|Próximamente|
|**InterCloud**|Compatible||
|**Level 3 EVPL Service**|Compatible||
|**TeleCity Group**|Compatible||
|**Zayo Group**|Compatible||

**Proveedores de servicios de red (NSP)**

|**Proveedor de servicios**|**Servicios de Microsoft Azure**|**Servicios de Office 365**|
|---|---|---|
|**AT&T**|Compatible|Próximamente|
|**British Telecom**|Compatible|Próximamente|
|**Colt IPVPN**|Compatible||
|**Internet Initiative Japan Inc. - IIJ**|Compatible||
|**Level3 IPVPN**|Compatible||
|**Orange**|Compatible|| 
|**SingTel**|Compatible||
|**Tata Communications**|Compatible||
|**Teltra Corporation**|Compatible||
|**Verizon**|Compatible|| 


## Integradores de sistemas de ExpressRoute
Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistema que aparecen en la tabla siguiente para ayudarle con la incorporación de ExpressRoute.


|**Integrador del sistema**|**Continente**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Pasos siguientes
- Compruebe que se cumplen los [requisitos previos de ExpressRoute](expressroute-prerequisites.md).
- Revise las  [P+G](expressroute-faqs.md) para obtener más información.
- Seleccione el proveedor y configure la conexión. Consulte [Configuración de la conexión de EXP](expressroute-configuring-exps.md) o [Configuración de la conexión NSP](expressroute-configuring-nsps.md) para obtener información de configuración.


<!---HONumber=54-->