---
title: Redes de Azure Governmenmt | Microsoft Docs
description: "Se proporciona una comparación de características e instrucciones sobre la conectividad privada a e-Government."
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


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

## <a name="next-steps"></a>Pasos siguientes
Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>




<!--HONumber=Nov16_HO3-->


