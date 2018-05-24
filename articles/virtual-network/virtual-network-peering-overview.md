---
title: Emparejamiento de redes virtuales de Azure | Microsoft Docs
description: Más información sobre el emparejamiento de redes virtuales en Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: jdial
ms.openlocfilehash: f2dddb6e9a933c1dc1eb4ccbf7ace6757d546fc8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367113"
---
# <a name="virtual-network-peering"></a>Emparejamiento de redes virtuales de Azure

El emparejamiento de redes virtuales permite conectar sin problemas dos [redes virtuales](virtual-networks-overview.md) de Azure. Una vez emparejadas, a efectos de conectividad las redes virtuales aparecen como una sola. El tráfico entre las máquinas virtuales de las redes virtuales emparejadas se enruta a través de la infraestructura de la red troncal de Microsoft, de forma muy parecida a como se enruta el tráfico entre máquinas virtuales de la misma red virtual a través únicamente de direcciones IP *privadas*. Azure admite:
* Emparejamiento de redes virtuales: conexión de redes virtuales dentro de la misma región de Azure
* Emparejamiento de redes virtuales globales: conexión de redes virtuales por regiones de Azure

Las ventajas del uso del emparejamiento de redes virtuales, ya sean locales o globales, son las siguientes:

* El tráfico de red entre redes virtuales emparejadas es privado. El tráfico entre las redes virtuales se mantiene en la red troncal de Microsoft. No se requieren ninguna red pública de Internet, puertas de enlace o cifrado en la comunicación entre las redes virtuales.
* Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
* La capacidad para que los recursos de una red virtual se comunique con los recursos de otra red virtual diferente, una vez que las redes virtuales estén emparejadas.
* La capacidad para transferir datos a través de las suscripciones de Azure, los modelos de implementación y entre las distintas regiones de Azure.
* Posibilidad de emparejar redes virtuales creadas mediante Azure Resource Manager o de emparejar una red virtual creada mediante Resource Manager con una red virtual creada mediante el modelo de implementación clásica. Para más información sobre los modelos de implementación de Azure, consulte [Descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* No tienen tiempo de inactividad ni los recursos de la red virtual al crear el emparejamiento, o después.

## <a name="connectivity"></a>Conectividad

Después de que se hayan emparejado las redes virtuales, los recursos de cualquiera de las máquinas virtuales se pueden conectar directamente con los recursos de la otra red virtual emparejada.

La latencia de red entre las máquinas virtuales de redes virtuales emparejadas en la misma región es la misma que la de una única red virtual. El rendimiento de la red se basa en el ancho de banda que se permite para la máquina virtual, en proporción a su tamaño. No hay restricciones adicionales con respecto al ancho de banda en el emparejamiento.

El tráfico entre las máquinas virtuales en las redes virtuales emparejadas se enruta directamente a través de la infraestructura de red troncal de Microsoft, no de una puerta de enlace ni de una red Internet pública.

Si se desea, se pueden aplicar grupos de seguridad de red en cualquier red virtual para bloquear el acceso a otras redes o subredes virtuales.
Al configurar el emparejamiento de red virtual, puede abrir o cerrar las reglas del grupo de seguridad de red entre las redes virtuales. Si abre la conectividad completa entre las redes virtuales emparejadas (que es la opción predeterminada), puede aplicar grupos de seguridad de red a subredes o máquinas virtuales concretas para bloquear o denegar acceso específico. Para más información acerca de los grupos de seguridad de red, consulte [Filtrado del tráfico de red con grupos de seguridad de red](security-overview.md).

## <a name="service-chaining"></a>Encadenamiento de servicios

Puede configurar las rutas definidas por el usuario que apuntan a máquinas virtuales de redes virtuales emparejadas como la dirección IP del *próximo salto*, o a puertas de enlace de red virtual, para permitir el encadenamiento de servicios. El encadenamiento de servicios permite dirigir el tráfico desde una red virtual hasta una aplicación virtual, o una puerta de enlace de red virtual, en una red virtual emparejada mediante rutas definidas por el usuario.

Puede implementar redes del tipo de concentrador y radio, en los que el concentrador puede hospedar componentes de la infraestructura, como una aplicación virtual de red o puerta de enlace de VPN. Todas las redes virtuales de radio se pueden emparejar con la red virtual de concentrador. El tráfico puede fluir por las aplicaciones virtuales de red o puertas de enlace de VPN que se ejecutan en la red virtual de concentrador. 

El emparejamiento de red virtual permite que el próximo salto de una ruta definida por el usuario sea la dirección IP de una máquina virtual de la red virtual emparejada o en una puerta de enlace de VPN. Sin embargo, no puede enrutar entre redes virtuales con una ruta definida por el usuario que especifique una puerta de enlace ExpressRoute como el tipo de salto siguiente. Para obtener más información sobre las rutas definidas por el usuario, consulte [Introducción a las rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined). Para aprender a crear una topología de red en estrella tipo hub-and-spoke, consulte [Implement a hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering) (Implementación de una topología en estrella tipo hub-and-spoke en Azure).

## <a name="gateways-and-on-premises-connectivity"></a>Puertas de enlace y conectividad local

Todas las redes virtuales, con independencia de que estén emparejadas con otra, pueden tener su propia puerta de enlace y usarla para conectarse a una red local. También puede configurar [conexiones de red virtual a red virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mediante puertas de enlace, incluso aunque las redes virtuales estén emparejadas.

Cuando están configuradas las dos opciones de interconectividad de redes virtual, el tráfico entre las redes virtuales se propaga a través de la configuración del emparejamiento (es decir, a través de la red troncal de Azure).

Cuando las redes virtuales están emparejadas en la misma región, también puede configurar la puerta de enlace de la red virtual emparejada como punto de tránsito a una red local. En este caso, la red virtual que usa una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual no puede tener más de una puerta de enlace. La puerta de enlace puede ser local o remota (en la red virtual emparejada), como se muestra en la siguiente imagen:

![tránsito de emparejamiento de redes virtuales](./media/virtual-networks-peering-overview/figure04.png)

No se admite el tránsito de puerta de enlace en la relación de emparejamiento entre las redes virtuales creadas en regiones diferentes. Ambas redes virtuales de la relación de emparejamiento deben existir en la misma región para que funcione el tránsito de puerta de enlace. El tránsito de puerta de enlace entre las redes virtuales creadas mediante diferentes modelos de implementación (Resource Manager y clásico) solo se admite si la puerta de enlace está en la red virtual (Resource Manager). Para más información sobre el uso de una puerta de enlace de tránsito, consulte [Configure a VPN gateway for transit in a virtual network peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Configuración de una puerta de enlace de VPN de tránsito en un emparejamiento de red virtual).

Cuando las redes virtuales que comparten una única conexión de Azure ExpressRoute están emparejadas, el tráfico entre ellas atraviesa la relación de emparejamiento (es decir, la red troncal de Azure). Puede seguir usando las puertas de enlace locales de cada red virtual para conectarse al circuito local. Como alternativa, puede utilizar una puerta de enlace compartida y configurar el tránsito para la conectividad local.

## <a name="troubleshoot"></a>Solución de problemas

Para confirmar un emparejamiento de redes virtuales puede [comprobar rutas efectivas](virtual-network-routes-troubleshoot-portal.md) para una interfaz de red en cualquier subred de una red virtual. Si el emparejamiento de redes virtuales existe, todas las subredes de la red virtual tienen rutas con próximo salto del tipo *emparejamiento de VNet*, para cada espacio de direcciones en cada red virtual emparejada.

También puede solucionar problemas con la conectividad de una máquina virtual en una red virtual emparejada con la [comprobación de conectividad](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de Network Watcher. La comprobación de conectividad le permite ver cómo se enruta el tráfico desde la interfaz de red de una máquina virtual de origen a la interfaz de red de una máquina virtual de destino.

## <a name="requirements-and-constraints"></a>Requisitos y restricciones

Para más información acerca de los requisitos y restricciones, consulte [Requisitos y restricciones de emparejamiento de redes virtuales](virtual-network-manage-peering.md#requirements-and-constraints). Para más información acerca de los límites para el número de emparejamientos que puede crear para una red virtual, consulte [Límites de red de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Permisos

Para más información acerca de los permisos necesarios para crear un emparejamiento de red virtual, consulte [Permisos de emparejamiento de red virtual](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Precios

Hay un cargo nominal para el tráfico de entrada y salida que utiliza una conexión de emparejamiento de red virtual. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network)para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

* Un emparejamiento de redes virtuales se crea entre redes virtuales creadas mediante el mismo modelo de implementación o mediante modelos distintos que existen en la misma suscripción o en cualquier suscripción diferente. Realice el tutorial para uno de los siguientes escenarios:

    |Modelo de implementación de Azure             | La suscripción  |
    |---------                          |---------|
    |Ambas mediante Resource Manager              |[La misma](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Una mediante Resource Manager y la otra clásica  |[La misma](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Aprenda a crear una [topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Conozca toda la [configuración de emparejamiento de red virtual y cómo cambiarla](virtual-network-manage-peering.md).
