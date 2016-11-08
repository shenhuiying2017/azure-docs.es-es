---
title: Documentación de Azure Governmenmt | Microsoft Docs
description: Se proporciona una comparación de características e instrucciones sobre la conectividad privada a e-Government.
services: Azure-Government
cloud: gov
documentationcenter: ''
author: ryansoc
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 08/25/2016
ms.author: ryansoc

---
# Redes de Azure Government
## ExpressRoute (conectividad privada)
Hay dos servicios básicos que proporcionan conectividad de red privada en Azure Government: VPN (de sitio a sitio en una organización típica ) y ExpressRoute.

ExpressRoute de Azure se usa para crear conexiones privadas entre centros de datos de Azure Government y la infraestructura local, o en un entorno de coubicación. Las conexiones ExpressRoute no utilizan la Internet pública y ofrecen más confiabilidad, velocidad, seguridad y una menor latencia que las conexiones a Internet habituales. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre sistemas locales y Azure puede aportar beneficios económicos importantes.

Con ExpressRoute, puede establecer conexiones con Azure en una ubicación de ExpressRoute (por ejemplo, las instalaciones de un proveedor de Exchange) o puede conectarse directamente a Azure desde su red WAN existente (por ejemplo, una red VPN de conmutación por etiquetas multiprotocolo, MPLS) proporcionada por un proveedor de servicios de red.

![texto alternativo](./media/azure-government-capability-private-connectivity-options.PNG) ![texto alternativo](./media/government-capability-expressroute.PNG)

Para que los servicios de red admitan aplicaciones y soluciones de cliente de Azure Government, se recomienda firmemente implementar ExpressRoute (conectividad privada) para conectar con Azure Government. Si se usan conexiones VPN, debe tener en cuenta lo siguiente:

• Los clientes deben ponerse en contacto con el organismo o funcionario autorizado para determinar si se necesita conectividad privada u otro mecanismo de conexión seguro para identificar las restricciones adicionales que se deben tener en cuenta.

• Los clientes deben decidir si quieren ordenar que la VPN de sitio a sitio se enrute mediante una zona de conectividad privada.

• Los clientes deben obtener un circuito MPLS o VPN con un proveedor de acceso de conectividad privada con licencia.

Todos los clientes que usen una arquitectura de conectividad privada deben validar que se haya establecido y mantenido una implementación adecuada para la conexión del cliente al punto de demarcación del enrutador de borde de red de puerta de enlace/Internet (GN/I) para Azure Government. De igual forma, la organización debe establecer conectividad de red entre el entorno local y el punto de demarcación del enrutador de borde de red de puerta de enlace/cliente (GN/C) para Azure Government.

ExpressRoute está disponible con carácter general en Azure Government. Para más información (lo que incluye asociados y ubicaciones de emparejamiento), consulte la <a href="https://azure.microsoft.com/documentation/services/expressroute/"> documentación pública de ExpressRoute </a>.

Para información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government. </a>.

<!---HONumber=AcomDC_0831_2016-->