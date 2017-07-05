---
title: 'Directrices de infraestructura de red de Azure: Windows | Microsoft Docs'
description: "Obtenga información sobre el diseño de claves y las directrices de implementación para implementar una red virtual en los servicios de infraestructura de Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e2d45973-5eba-4904-8ba0-1821f64feed7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 3338d3d259189c85c21758ae92b7f45611e400ac
ms.contentlocale: es-es
ms.lasthandoff: 03/31/2017


---
# <a name="azure-networking-infrastructure-guidelines-for-windows-vms"></a>Directrices de infraestructura de red de Azure para máquinas virtuales Windows 

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artículo se centra en describir los pasos de planeación necesarios para las redes virtuales dentro de Azure y la conectividad entre entornos locales existentes.

## <a name="implementation-guidelines-for-virtual-networks"></a>Directrices de implementación para las redes virtuales
Decisiones:

* ¿Qué tipo de red virtual necesita para hospedar su infraestructura o carga de trabajo de TI (solo en la nube o entre locales)?
* Para las redes virtuales entre locales, ¿cuánto espacio de direcciones necesita para hospedar ahora las subredes y las máquinas virtuales y durante un tiempo razonable en el futuro?
* ¿Va a crear redes virtuales centralizadas o crear redes virtuales individuales para cada grupo de recursos?

Tareas:

* Defina el espacio de direcciones para la red virtual que se va a crear.
* Defina el conjunto de subredes y el espacio de direcciones para cada uno.
* Para las redes virtuales entre locales, defina el conjunto de espacios de direcciones de redes locales para las ubicaciones locales que las máquinas virtuales de la red virtual deben alcanzar.
* Trabaje con el equipo de redes local para asegurarse de que está configurado el enrutamiento adecuado al crear redes virtuales entre entornos.
* Cree la red virtual usando su convención de nomenclatura.

## <a name="virtual-networks"></a>Redes virtuales
Las redes virtuales son necesarias para admitir las comunicaciones entre máquinas virtuales. Puede definir subredes, direcciones IP personalizadas, configuración de DNS, filtrado de seguridad y equilibrio de carga, al igual que con redes físicas. Mediante una [puerta de enlace de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) o un [circuito de Express Route](../../expressroute/expressroute-introduction.md), puede conectar redes virtuales de Azure a sus redes locales. Lea más sobre [redes virtuales y sus componentes](../../virtual-network/virtual-networks-overview.md).

Al utilizar grupos de recursos, tendrá flexibilidad para diseñar los componentes de red virtual. Las máquinas virtuales pueden conectarse a redes virtuales fuera de su propio grupo de recursos. Un enfoque de diseño común sería crear grupos de recursos centralizados que contienen la infraestructura de red principal que puede administrarse por un equipo común y, después, las máquinas virtuales y sus aplicaciones implementadas para grupos de recursos independientes. Gracias a este enfoque, los propietarios de aplicaciones podrán acceder al grupo de recursos que contiene sus máquinas virtuales sin tener que abrir el acceso a la configuración de los recursos de redes virtuales más amplios.

## <a name="site-connectivity"></a>Conectividad de sitio
### <a name="cloud-only-virtual-networks"></a>Redes virtual solo en la nube
Si los equipos y usuarios locales no requieren una conectividad continua a las máquinas virtuales de una red virtual de Azure, el diseño de la red virtual será bastante sencillo:

![Diagrama de red virtual básico solo en la nube](./media/infrastructure-networking-guidelines/vnet01.png)

Este enfoque está destinado a cargas de trabajo con conexión a Internet, como un servidor web basado en Internet. Puede administrar estas máquinas virtuales mediante RDP o conexiones VPN de punto a sitio.

Como no se conectan a la red local, las redes virtuales solo de Azure pueden usar cualquier parte del espacio de direcciones IP privado, incluso si el mismo espacio privado está en uso de forma local.

### <a name="cross-premises-virtual-networks"></a>Redes virtuales entre locales
Si los equipos y usuarios locales requieren una conectividad continua a máquinas virtuales de una red virtual de Azure, cree una red virtual entre locales.  Conéctela a la red local con una conexión VPN de sitio a sitio o ExpressRoute.

![Diagrama de red virtual local](./media/infrastructure-networking-guidelines/vnet02.png)

En esta configuración, la red virtual de Azure es esencialmente una extensión basada en la nube de su red local.

Dado que se conectan a la red local, las redes virtuales entre entornos deben utilizar una parte del espacio de direcciones usado por la organización que es única. De la misma manera que diferentes ubicaciones de la empresa se asignarán a una subred IP específica, Azure se convierte en otra ubicación cuando se amplíe la red.

Para permitir que los paquetes viajen de la red virtual entre locales a la red local, debe configurar el conjunto de prefijos pertinentes de direcciones locales como parte de la definición de la red local para la red virtual. En función del espacio de direcciones de la red virtual y del conjunto de ubicaciones locales pertinentes, puede haber varios prefijos de direcciones en la red local.

Puede convertir una red virtual solo en la nube a una entre locales, pero, probablemente, tendrá que volver a numerar el espacio de direcciones de la red virtual, las subredes y los recursos de Azure. Por lo tanto, considere detenidamente si una red virtual debe estar conectada a la red local al asignar una subred IP.

## <a name="subnets"></a>Subredes
Las subredes permiten organizar los recursos que están relacionados, ya sea de manera lógica (por ejemplo, una subred para VM asociadas a la misma aplicación) o física (por ejemplo, una subred por grupo de recursos), o bien emplear técnicas de aislamiento de subred para ofrecer mayor seguridad.

En lo que respecta a las redes virtuales entre locales, debe diseñar subredes con las mismas convenciones que para los recursos locales. **Azure siempre usa las tres primeras direcciones IP del espacio de direcciones en cada subred**. Para determinar el número de direcciones que requiere la subred, empiece contando el número de máquinas virtuales que necesita en este momento. Calcule cuántas cree que tendrá en el futuro y, después, utilice la siguiente tabla para determinar el tamaño de la subred.

| Número de máquinas virtuales necesario | Número de bits de host necesarios | Tamaño de la subred |
| --- | --- | --- |
| 1-3 |3 |/29 |
| 4-11 |4 |/28 |
| 12-27 |5 |/27 |
| 28-59 |6 |/26 |
| 60-123 |7 |/25 |

> [!NOTE]
> Para las subredes locales normales, el número máximo de direcciones de host para una subred con n bits de host es 2<sup>n</sup> – 2. Para una subred de Azure, el número máximo de direcciones de host para una subred con n bits de host es 2<sup>n</sup> – 5 (2 más 3 para las direcciones que Azure usa en cada subred).
> 
> 

Si elige un tamaño de subred demasiado pequeño, tendrá que volver a asignar la IP y a implementar las máquinas virtuales en la subred.

## <a name="network-security-groups"></a>Grupos de seguridad de red
Puede aplicar reglas de filtrado al tráfico que fluye por las redes virtuales mediante el uso de grupos de seguridad de red. Puede crear reglas de filtrado granulares para proteger el entorno de red virtual, con lo que se controla el tráfico entrante y saliente, los intervalos IP de origen y de destino, los puertos permitidos, etc. Los grupos de seguridad de red pueden aplicarse a subredes de una red virtual o bien directamente a una interfaz de red virtual determinada. Se recomienda tener cierto nivel de tráfico de filtrado del grupo de seguridad de red en las redes virtuales. Lea más sobre [Grupos de seguridad de red](../../virtual-network/virtual-networks-nsg.md).

## <a name="additional-network-components"></a>Componentes de red adicionales
Al igual que con una infraestructura de red física local, las redes virtuales de Azure pueden contener más elementos, aparte de las subredes y direcciones IP. Al diseñar la infraestructura de aplicaciones, puede incorporar algunos de estos componentes adicionales:

* [Puertas de enlace de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) : conecte las redes virtuales de Azure a otras del mismo tipo, o bien a redes locales a través de una conexión VPN de sitio a sitio. Para disfrutar de una conectividad específica y segur, implemente conexiones ExpressRoute. También puede proporcionar a los usuarios acceso directo con conexiones VPN de punto a sitio.
* [Equilibrador de carga](../../load-balancer/load-balancer-overview.md) : proporciona equilibrio de carga del tráfico para el tráfico interno y externo, como se desee.
* [Application Gateway](../../application-gateway/application-gateway-introduction.md) : se trata de un servicio de equilibrio de carga HTTP en el nivel de aplicación, lo que proporciona algunas otras ventajas a las aplicaciones web, en lugar de implementar solo Azure Load Balancer.
* [Administrador de tráfico](../../traffic-manager/traffic-manager-overview.md) : distribución del tráfico basado en DNS para dirigir a los usuarios finales al punto de conexión de aplicación disponible más cercano, lo que le permite hospedar la aplicación en los centros de datos de Azure en diferentes regiones.

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


