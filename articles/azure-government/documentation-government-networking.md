---
title: Redes de Azure Governmenmt | Microsoft Docs
description: "Se proporciona una comparación de características e instrucciones sobre la conectividad privada a e-Government."
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8


---
# <a name="azure-government-networking"></a>Redes de Azure Government
## <a name="expressroute-private-connectivity"></a>ExpressRoute (conectividad privada)
ExpressRoute está disponible con carácter general en Azure Government. Para más información (lo que incluye asociados y ubicaciones de emparejamiento), consulte la [ documentación pública de ExpressRoute ](../expressroute/index.md).

### <a name="variations"></a>Variaciones
ExpressRoute está disponible con carácter general (GA) en Azure Government. 

* Los clientes de Government se conectan a una capacidad aislada físicamente a través de una conexión ExpressRoute (ER) dedicada de Azure Government (Gov).
* Azure Gov proporciona una mayor disponibilidad y durabilidad al aprovechar varios pares de regiones ubicados a menos de 800 km de distancia. 
* De forma predeterminada, toda la conectividad de ER de Azure Gov está configurada con redundancia activo/activo y es compatible con seguridad y entregas hasta una capacidad de circuito de 10 G (el más pequeño 50 MB).
* Las ubicaciones de ER de Azure Gov proporcionan caminos optimizados (saltos más cortos, baja latencia, alto rendimiento, etc.) para clientes y regiones con redundancia geográfica de Azure Gov.
* La conexión privada de ER de Azure Gov no usa, recorre ni depende de Internet.
* La infraestructura física y lógica de Azure Gov está dedicada y separada físicamente, y el acceso está restringido a personas de EE. UU.
* Microsoft posee y opera toda la infraestructura de fibra entre regiones de Azure Gov y ubicaciones Meet-Me de ER de Azure Gov.
* ER de Azure Gov ofrece conectividad a Microsoft Azure, O365 y servicios en la nube de CRM.

### <a name="considerations"></a>Consideraciones
Hay dos servicios básicos que proporcionan conectividad de red privada en Azure Government: VPN (de sitio a sitio en una organización típica ) y ExpressRoute.

ExpressRoute de Azure se usa para crear conexiones privadas entre centros de datos de Azure Government y la infraestructura local, o en un entorno de coubicación. Las conexiones ExpressRoute no utilizan la Internet pública y ofrecen más confiabilidad, velocidad, seguridad y una menor latencia que las conexiones a Internet habituales. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre sistemas locales y Azure puede aportar beneficios económicos importantes.   

Con ExpressRoute, puede establecer conexiones con Azure en una ubicación de ExpressRoute (por ejemplo, las instalaciones de un proveedor de Exchange) o puede conectarse directamente a Azure desde su red WAN existente (por ejemplo, una red VPN de conmutación por etiquetas multiprotocolo, MPLS) proporcionada por un proveedor de servicios de red.

![texto alternativo](./media/azure-government-capability-private-connectivity-options.PNG)  ![texto alternativo](./media/government-capability-expressroute.PNG)  

Para que los servicios de red admitan aplicaciones y soluciones de cliente de Azure Government, se recomienda firmemente implementar ExpressRoute (conectividad privada) para conectar con Azure Government. Si se usan conexiones VPN, debe tener en cuenta lo siguiente:

* Los clientes deben ponerse en contacto con el organismo o funcionario autorizado para determinar si se necesita conectividad privada u otro mecanismo de conexión seguro para identificar las restricciones adicionales que se deben tener en cuenta.
* Los clientes deben decidir si quieren ordenar que la VPN de sitio a sitio se enrute mediante una zona de conectividad privada.
* Los clientes deben obtener un circuito MPLS o VPN con un proveedor de acceso de conectividad privada con licencia.

Todos los clientes que usen una arquitectura de conectividad privada deben validar que se haya establecido y mantenido una implementación adecuada para la conexión del cliente al punto de demarcación del enrutador de borde de red de puerta de enlace/Internet (GN/I) para Azure Government. De igual forma, la organización debe establecer conectividad de red entre el entorno local y el punto de demarcación del enrutador de borde de red de puerta de enlace/cliente (GN/C) para Azure Government.

## <a name="support-for-bgp-communities"></a>Soporte técnico para las comunidades de BGP
En esta sección se proporciona información general de cómo se usarán las comunidades de BGP con ExpressRoute en AzureGov. Microsoft anunciará rutas en las rutas de acceso de configuración de pares privados y de Microsoft con rutas etiquetadas con valores de la comunidad adecuada. La razón para hacerlo y los detalles de los valores de la comunidad se describen a continuación. Sin embargo, Microsoft no acepta los valores de comunidad etiquetados en las rutas anunciadas a Microsoft.

Si se va a conectar a Microsoft a través de ExpressRoute en cualquier ubicación de configuración de pares dentro de la región de AzureGov, tendrá acceso a todos los servicios en la nube de Microsoft en todas las regiones que se encuentren dentro de los límites del gobierno. 

Por ejemplo, si conectó a Microsoft en Washington D.C. a través de ExpressRoute, tendrá acceso a todos los servicios en la nube de Microsoft hospedados en AzureGov.

Consulte la pestaña "Introducción" de la [documentación pública de ExpressRoute](../expressroute/index.md) para más información sobre ubicaciones y asociados, y una lista detallada de ExpressRoute para las ubicaciones de emparejamiento de AzureGov.

Puede comprar más de un circuito de ExpressRoute. Tener varias conexiones ofrece importantes ventajas para la alta disponibilidad debido a la redundancia geográfica. En los casos en que tenga varios circuitos de ExpressRoute, recibirá el mismo conjunto de prefijos anunciados de Microsoft en las rutas de acceso de la configuración de pares públicos y de la configuración de pares de Microsoft. Esto significa que tendrá varias rutas de acceso desde su red a Microsoft. Potencialmente, esto puede provocar que se tomen decisiones de enrutamiento en la red que no sean óptimas. Como consecuencia, puede sufrir una conectividad con los diferentes servicios que no sea óptima. 

Microsoft etiquetará los prefijos anunciados a través de la configuración de pares públicos y de la configuración de pares de Microsoft con los valores de comunidad de BGP adecuados que indican la región en que se hospedan. Puede confiar en los valores de la comunidad para tomar decisiones de enrutamiento adecuadas para ofrecer un enrutamiento óptimo a los clientes.  Para más información, consulte la pestaña "Introducción" de la [documentación pública de ExpressRoute](../expressroute/index.md) y haga clic en "Optimización del enrutamiento".

| **Nubes nacionales Región de Azure**| **Valor de comunidad de BGP** |
| --- | --- |
| **Gobierno de Estados Unidos** |  |
| Gobierno de EE. UU. - Iowa | 12076:51109 |
| Gobierno de EE. UU. - Virginia | 12076:51105 |

Todas las rutas anunciadas de Microsoft se etiquetarán con el valor de la comunidad adecuado. 

Además, Microsoft también etiquetará los prefijos en función del servicio al que pertenecen. Esto se aplica solo a la configuración de pares de Microsoft. La tabla siguiente proporciona una asignación de servicio al valor de la comunidad de BGP.

| **Servicios en nubes nacionales** | **Valor de comunidad de BGP** |
| --- | --- |
| **Gobierno de Estados Unidos** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype Empresarial Online |12076:5130 |
| CRM en línea |12076:5140 |
| Otros servicios en línea de Office 365 |12076:5200 |

> [!NOTE]
> Microsoft no admite los valores de las comunidades de BGP que defina en las rutas anunciadas a Microsoft.

## <a name="support-for-load-balancer"></a>Compatibilidad con Load Balancer
Load Balancer está disponible en Azure Government con carácter general. Para más información, consulte la [documentación pública de Load Balancer](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manger"></a>Compatibilidad con Traffic Manager
Traffic Manager está disponible en Azure Government con carácter general. Para más información, consulte la [documentación pública de Traffic Manager](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-vnet-peering"></a>Compatibilidad con el emparejamiento de VNET 
El emparejamiento de VNET está disponible en Azure Government con carácter general. Para más información, consulte la [documentación pública del emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>Compatibilidad con VPN Gateway 
VPN Gateway está disponible en Azure Government con carácter general. Para más información, consulte la [documentación pública de VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>




<!--HONumber=Jan17_HO5-->


