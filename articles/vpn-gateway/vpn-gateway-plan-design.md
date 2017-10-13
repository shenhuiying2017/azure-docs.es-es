---
title: "Planeamiento y diseño de conexiones entre sitios locales: Azure VPN Gateway | Microsoft Docs"
description: "Obtenga información acerca del planeamiento y diseño de VPN Gateway para conexiones entre sitios locales, híbridos y entre redes virtuales"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: d5aaab83-4e74-4484-8bf0-cc465811e757
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2017
ms.author: cherylmc
ms.openlocfilehash: 0ebc3ef4a64432e993dd6ed69766bb64544fe433
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="planning-and-design-for-vpn-gateway"></a>Planeamiento y diseño de VPN Gateway

Planear y diseñar configuraciones entre sitios locales y entre redes virtuales puede resultar simple o complejo, según las necesidades de la red. Este artículo le guía a través de las consideraciones de diseño y planeamiento básicas.

## <a name="planning"></a>Planeamiento

### <a name="compare"></a>Opciones de conectividad entre locales

Si quiere conectar sus sitios locales de manera segura a una red virtual, existen tres maneras distintas de hacerlo: de sitio a sitio, de punto a sitio y mediante ExpressRoute. Compare las distintas conexiones entre sitios locales que hay disponibles. La opción que elija dependerá de varios factores, por ejemplo:

* ¿Qué tipo de rendimiento requiere la solución?
* ¿Desea comunicarse a través de la red pública de Internet mediante una VPN segura o a través de una conexión privada?
* ¿Dispone de una dirección IP pública?
* ¿Planea usar un dispositivo VPN? Si es así, ¿es compatible?
* ¿Va a conectar solo algunos equipos o desea establecer una conexión persistente para su sitio?
* ¿Qué tipo de puerta de enlace de VPN se necesita para la solución que desea crear?
* ¿Qué SKU de puerta de enlace se debe usar?

### <a name="planningtable"></a>Tabla de planeamiento

La tabla siguiente puede ayudarle a decidir la mejor opción de conectividad para su solución.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="gwsku"></a>SKU de puerta de enlace

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="wf"></a>Flujo de trabajo

La lista siguiente describe el flujo de trabajo común para la conectividad en la nube:

1. Diseñe y planee la topología de conectividad y enumere los espacios de direcciones para todas las redes que desee conectar.
2. Cree una red virtual de Azure. 
3. Cree una puerta de enlace de VPN para la red virtual.
4. Cree y configure conexiones a redes locales o a otras redes virtuales (según sea necesario).
5. Cree y configure una conexión de punto a sitio para su puerta de enlace de Azure VPN Gateway (según sea necesario).

## <a name="design"></a>Diseño
### <a name="topologies"></a>Topologías de conexión

Para comenzar, consulte los diagramas del artículo [Acerca de VPN Gateway](vpn-gateway-about-vpngateways.md). El artículo contiene diagramas básicos, los modelos de implementación de cada topología y las herramientas de implementación disponibles que puede usar para implementar la configuración.

### <a name="designbasics"></a>Conceptos básicos de diseño

Las secciones siguientes analizan los aspectos básicos de VPN Gateway. 

#### <a name="servicelimits"></a>Límites de servicios de red

Desplácese por las tablas para ver los [límites de servicios de red](../azure-subscription-service-limits.md#networking-limits). Los límites mencionados pueden afectar a su diseño.

#### <a name="subnets"></a>Acerca de las subredes

Al crear conexiones, debe tener en cuenta los intervalos de subred. Los intervalos de direcciones de subred no se pueden superponer. Se habla de subred superpuesta cuando una red virtual o una ubicación local contiene el mismo espacio de direcciones que la otra ubicación. Esto significa que es necesario que los ingenieros de red definan un intervalo que pueda usar para las subredes o el espacio de direcciones IP de Azure. Necesita un espacio de direcciones que no se esté usando en la red local.

También es importante evitar las subredes superpuestas cuando se trabaja con conexiones entre redes virtuales. Si las subredes se superponen y una dirección IP existe en la red virtual de envío y en la red virtual de destino, se produce un error en las conexiones entre redes virtuales. Azure no puede enrutar los datos a la otra red virtual porque la dirección de destino forma parte de la red virtual de envío.

Las puertas de enlace de VPN requieren una subred específica llamada subred de puerta de enlace. Para que funcionen correctamente, todas las subredes de puerta de enlace se deben llamar GatewaySubnet. Asegúrese de no asignar un nombre distinto a la subred de puerta de enlace y no implemente máquinas virtuales ni ningún otro elemento en la subred de puerta de enlace. Consulte [Subredes de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="local"></a>Acerca de las puertas de enlace de red local

La puerta de enlace de red local suele hacer referencia a la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio de red local. Cuando configure una puerta de enlace de red local, asígnele un nombre, especifique la dirección IP pública en el dispositivo VPN local y especifique los prefijos de dirección que están en la ubicación local. Azure examina los prefijos de dirección de destino del tráfico de red, consulta la configuración que especificó para la puerta de enlace de red local y enruta los paquetes según corresponda. Puede modificar los prefijos de dirección según sea necesario. Para más información, consulte [Puertas de enlace de red](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="gwtype"></a>Acerca de los tipos de puerta de enlace

Seleccionar el tipo de puerta de enlace correcto para su topología es fundamental. Si selecciona el tipo incorrecto, la puerta de enlace no funcionará correctamente. El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y es un valor de configuración necesario para el modelo de implementación de Resource Manager.

Los tipos de puerta de enlace son los siguientes:

* VPN
* ExpressRoute

#### <a name="connectiontype"></a>Acerca de los tipos de conexión

Cada configuración requiere un tipo de conexión específico. Los tipos de conexión son los siguientes:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="vpntype"></a>Acerca de los tipos de VPN

Cada configuración requiere un tipo específico de VPN específico. Si combina dos configuraciones, como la creación de una conexión de sitio a sitio y una conexión de punto a sitio en la misma red virtual, debe usar un tipo de VPN que cumpla ambos requisitos de conexión.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Las tablas siguientes muestran el tipo de VPN y su correspondencia con la configuración de la conexión. Asegúrese de que el tipo de VPN correspondiente a su puerta de enlace coincida con la configuración que desea crear. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>Dispositivos VPN para conexiones de sitio a sitio

Para configurar una conexión de sitio a sitio, independientemente del modelo de implementación, necesita los siguientes elementos:

* Un dispositivo VPN compatible con Azure VPN Gateway.
* Una dirección IP IPv4 pública que no se encuentre detrás de un NAT.

Debe tener experiencia en la configuración de dispositivos VPN o pedir a alguien que le configure el dispositivo.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="forcedtunnel"></a>Considerar el enrutamiento de túnel forzado

La tunelización forzada se puede configurar en la mayoría de las configuraciones. La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio con fines de inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI. 

Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure siempre irá desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionar o auditar el tráfico. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad.

Ambos modelos de implementación permiten configurar una conexión de tunelización forzada mediante distintas herramientas. Para obtener más información, consulte [Configuración de la tunelización forzada](vpn-gateway-forced-tunneling-rm.md).

**Diagrama de tunelización forzada**

![Diagrama de tunelización forzada de Azure VPN Gateway](./media/vpn-gateway-plan-design/forced-tunneling-diagram.png)

## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos [Preguntas más frecuentes sobre VPN Gateway](vpn-gateway-vpn-faq.md) e [Información sobre VPN Gateway](vpn-gateway-about-vpngateways.md) para obtener más información que pueda ayudarle con el diseño.

Para obtener más información acerca de la configuración de puerta de enlace específica, consulte [Acerca de la configuración de VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).