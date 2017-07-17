---
title: Emparejamiento de redes virtuales de Azure | Microsoft Docs
description: "Más información sobre el emparejamiento de redes virtuales en Azure."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: narayan
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: bfba85160cd02baa337881653dbe6582d10ba073
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
<a id="virtual-network-peering" class="xliff"></a>

# Emparejamiento de redes virtuales de Azure
El emparejamiento de red virtual permite conectar dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecen como una sola a efectos de conectividad. No obstante, se administran como recursos independientes, pero las máquinas virtuales de estas redes virtuales emparejadas pueden comunicarse entre sí directamente mediante sus direcciones IP privadas.

El tráfico entre máquinas virtuales en las redes virtuales emparejadas se enruta a través de la infraestructura de Azure, de forma muy parecida a como se enruta el tráfico entre máquinas virtuales de la misma red virtual. Algunas de las ventajas del uso del emparejamiento de red virtual son:

* Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
* Posibilidad de usar recursos como aplicaciones de red y puertas de enlace de VPN como puntos de tránsito en una red virtual emparejada.
* Posibilidad de emparejar dos redes virtuales creadas mediante el modelo de implementación de Azure Resource Manager o de emparejar una red virtual creada mediante Resource Manager con una red virtual creada mediante el modelo de implementación clásica. Consulte el artículo donde se [describen los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para tener más información sobre las diferencias entre los dos modelos de implementación.

Requisitos y aspectos clave del emparejamiento de red virtual:

* Las redes virtuales emparejadas deben existir en la misma región de Azure. Puede conectar redes virtuales en diferentes regiones de Azure mediante una instancia de [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Deben tener además espacios de direcciones IP que no se solapen.
* No se pueden agregar espacios de direcciones a una red virtual ni eliminarse de esta una vez que una red virtual se empareja con otra red virtual.
* El emparejamiento de red virtual se realiza entre dos redes virtuales. No hay ninguna relación transitiva derivada entre emparejamientos. Por ejemplo, si la red virtual A está emparejada con la red virtual B y la red virtual B está emparejada con la red virtual C, la red virtual A *no* está emparejada con la red virtual C.
* Puede emparejar redes virtuales que existan en dos suscripciones distintas, siempre y cuando un usuario con privilegios de ambas suscripciones lo autorice y las suscripciones estén asociadas al mismo inquilino de Azure Active Directory. Puede usar una instancia de [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar redes virtuales en suscripciones asociadas a diferentes inquilinos de Active Directory.
* Se pueden emparejar redes virtuales si ambas se crean mediante el modelo de implementación de Resource Manager o si una se crea mediante el modelo de implementación de Resource Manager y la otra se crea mediante el modelo de implementación clásica. No obstante, dos redes virtuales creadas mediante el modelo de implementación clásica no se pueden emparejar entre sí. Cuando se emparejan redes virtuales creadas mediante diferentes modelos de implementación, estas deben existir en la *misma* suscripción. La posibilidad de emparejar redes virtuales creadas mediante diferentes modelos de implementación que existen en las *diferentes* suscripciones se encuentra en **versión preliminar**. Para más información, consulte el artículo sobre la [creación de un emparejamiento de red virtual](virtual-network-create-peering.md#different-subscriptions-different-deployment-models).
* Aunque la comunicación entre las máquinas virtuales en redes virtuales emparejadas no tiene restricciones de ancho de banda adicionales, hay un ancho de banda de red máximo en función del tamaño de máquina virtual que se aplica. Para más información sobre el ancho de banda de red máximo para los diferentes tamaños de máquina virtual, consulte los artículos sobre los tamaños de máquina virtual [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

![Emparejamiento básico de red virtual](./media/virtual-networks-peering-overview/figure01.png)

<a id="connectivity" class="xliff"></a>

## Conectividad
Después de que se emparejan dos redes virtuales, los roles de máquinas virtuales o de Cloud Services de la red virtual pueden conectar directamente con otros recursos conectados a la red virtual emparejada. Las dos redes virtuales tienen conectividad total a nivel de IP.

La latencia de red en un recorrido de ida y vuelta entre dos máquinas virtuales de redes virtuales emparejadas es la misma que la de un recorrido en una única red virtual. El rendimiento de la red se basa en el ancho de banda que se permite para la máquina virtual, en proporción a su tamaño. No hay restricciones adicionales con respecto al ancho de banda en el emparejamiento.

El tráfico entre las máquinas virtuales de redes virtuales emparejadas se enruta directamente a través de la infraestructura de back-end de Azure, y no a través de una puerta de enlace.

Las máquinas virtuales conectadas a una red virtual pueden acceder a los puntos de conexión con equilibrio de carga interna de la red virtual emparejada. Si se desea, se pueden aplicar grupos de seguridad de red en cualquier red virtual para bloquear el acceso a otras redes o subredes virtuales.

Al configurar el emparejamiento de red virtual, puede abrir o cerrar las reglas del grupo de seguridad de red entre las redes virtuales. Si abre la conectividad completa entre las redes virtuales emparejadas (que es la opción predeterminada), puede aplicar grupos de seguridad de red a subredes o máquinas virtuales concretas para bloquear o denegar acceso específico. Para más información sobre los grupos de seguridad de red, lea el artículo de [introducción a los grupos de seguridad de red](virtual-networks-nsg.md).

La resolución de nombres DNS internos que proporciona Azure para las máquinas virtuales no funciona en las redes virtuales emparejadas. Las máquinas virtuales tienen nombres DNS internos que solo se puedan resolver en la red virtual local. No obstante, puede configurar las máquinas virtuales conectadas a redes virtuales emparejadas como servidores DNS para una red virtual. Consulte el artículo [Resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) para más detalles.

<a id="service-chaining" class="xliff"></a>

## Encadenamiento de servicios
Puede configurar las rutas definidas por el usuario que apuntan a máquinas virtuales de redes virtuales emparejadas como la dirección IP del "próximo salto" para permitir el encadenamiento de servicios. El encadenamiento de servicios permite dirigir el tráfico desde una red virtual hasta una aplicación virtual de una red virtual emparejada mediante rutas definidas por el usuario.

También puede crear eficazmente entornos del tipo de concentrador y radio, en los que el concentrador puede hospedar componentes de la infraestructura, como una aplicación virtual de red. Todas las redes virtuales de radio se pueden emparejar con la red virtual de concentrador. El tráfico puede fluir por las aplicaciones virtuales de red que se ejecutan en la red virtual de concentrador. En resumen, el emparejamiento de red virtual permite que la dirección IP de próximo salto de la ruta definida por el usuario sea la dirección IP de una máquina virtual de la red virtual emparejada. Para aprender más sobre las rutas definidas por el usuario, lea el artículo de [introducción a las rutas definidas por el usuario](virtual-networks-udr-overview.md).

<a id="gateways-and-on-premises-connectivity" class="xliff"></a>

## Puertas de enlace y conectividad local
Todas las redes virtuales, con independencia de que estén emparejadas con otra, pueden tener su propia puerta de enlace y usarla para conectarse a una red local. También puede configurar [conexiones de red virtual a red virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mediante puertas de enlace, incluso aunque las redes virtuales estén emparejadas.

Cuando están configuradas las dos opciones de interconectividad de redes virtual, el tráfico entre las redes virtuales se propaga a través de la configuración del emparejamiento (es decir, a través de la red troncal de Azure).

Cuando las redes virtuales están emparejadas, también puede configurar la puerta de enlace de la red virtual emparejada como punto de tránsito a una red local. En este caso, la red virtual que usa una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual no puede tener más de una puerta de enlace. La puerta de enlace puede ser local o remota (en la red virtual emparejada), como se muestra en la siguiente imagen:

![Tránsito de emparejamiento de VNET](./media/virtual-networks-peering-overview/figure02.png)

No se admite el tránsito de puerta de enlace en la relación de emparejamiento entre las redes virtuales creadas mediante modelos de implementación diferentes. Para que funcione el tránsito de puerta de enlace, ambas redes virtuales de la relación de emparejamiento se deben haber creado mediante Resource Manager.

Cuando las redes virtuales que comparten una única conexión de Azure ExpressRoute están emparejadas, el tráfico entre ellas atraviesa la relación de emparejamiento (es decir, la red troncal de Azure). Puede seguir usando las puertas de enlace locales de cada red virtual para conectarse al circuito local. Como alternativa, puede utilizar una puerta de enlace compartida y configurar el tránsito para la conectividad local.

<a id="provisioning" class="xliff"></a>

## Aprovisionamiento
El emparejamiento de red virtual es una operación que requiere privilegios. Es una función independiente en el espacio de nombres VirtualNetworks. Los usuarios pueden tener derechos específicos para autorizar el emparejamiento. Los usuarios que tengan acceso de lectura y escritura a la red virtual heredan automáticamente estos derechos.

Los usuarios que ya sean administradores o tengan privilegios para emparejar pueden iniciar operaciones de emparejamiento en otra red virtual. Si hay una solicitud coincidente de emparejamiento en el otro lado y se cumplen el resto de requisitos, se establecerá el emparejamiento.

<a id="limits" class="xliff"></a>

## Límites
Hay límites en el número de emparejamientos de que se permiten en una red virtual individual. Para más información, revise los [límites de red de Azure](../azure-subscription-service-limits.md#networking-limits).

<a id="pricing" class="xliff"></a>

## Precios
Hay un cargo nominal para el tráfico de entrada y salida que utiliza un emparejamiento de red virtual. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network)para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

* Realice el [tutorial de emparejamiento de red virtual](virtual-network-create-peering.md)
* Conozca toda la [configuración de emparejamiento de red virtual y cómo cambiarla](virtual-network-manage-peering.md).

