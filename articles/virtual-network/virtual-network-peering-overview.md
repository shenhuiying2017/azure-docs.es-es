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
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: 15afcad97941fc595478e36e826a73831f40475e
ms.openlocfilehash: eb05b504c5cf13cd852a5e01cc3bec79fd20d547


---
# <a name="virtual-network-peering"></a>Emparejamiento de redes virtuales de Azure
El emparejamiento de redes virtuales (VNET) permite conectar dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecen como una sola a efectos de conectividad. No obstante, las dos redes virtuales se administran como recursos independientes, pero las máquinas virtuales de estas redes virtuales emparejadas pueden comunicarse entre sí directamente mediante sus direcciones IP privadas.

El tráfico entre máquinas virtuales en las redes virtuales emparejadas se enrutará a través de la infraestructura de Azure, similar el tráfico que se enruta entre máquinas virtuales de la misma red virtual. Estas son algunas de las ventajas que reporta el uso del emparejamiento de VNET:

* Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
* Capacidad para utilizar recursos como aparatos de red y puertas de enlace de VPN como puntos de tránsito en una red virtual emparejada.
* Capacidad para emparejar dos redes virtuales creadas mediante el modelo de implementación de Azure Resource Manager o para emparejar una red virtual creada mediante Resource Manager con una red virtual creada mediante el modelo de implementación clásica. Consulte el artículo donde se [describen los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md) para tener más información sobre las diferencias entre los dos modelos de implementación.

Requisitos y aspectos clave del emparejamiento de VNET:

* Las redes virtuales emparejadas deben existir en la misma región de Azure.
* Las redes virtuales emparejadas deben tener espacios de direcciones IP no superpuestos.
* El emparejamiento de redes virtuales se realiza entre dos redes virtuales, pero no hay ninguna relación transitiva derivada entre los emparejamientos. Por ejemplo, si VNetA está emparejada con VNetB y VNetB está emparejada con VNetC, VNetA *no* está emparejada con VNetC.
* Puede emparejar redes virtuales que existen en dos suscripciones distintas, siempre y cuando un usuario con privilegios de ambas suscripciones lo autorice y las suscripciones estén asociadas al mismo inquilino de Active Directory.
* Las redes virtuales se pueden emparejar si ambas se crean mediante el modelo de implementación de Resource Manager o si se crea una mediante el modelo de implementación de Resource Manager y la otra se crea mediante el modelo de implementación clásica. Sin embargo, dos redes virtuales creadas mediante el modelo de implementación clásica no se pueden emparejar entre sí. Cuando se crean redes virtuales emparejadas mediante diferentes modelos de implementación, las redes virtuales deben existir en la *misma* suscripción. La capacidad de emparejar redes virtuales creadas mediante diferentes modelos de implementación que existen en las *diferentes* suscripciones está **versión preliminar**. Consulte el artículo sobre la [creación de un emparejamiento de red virtual con Powershell](virtual-networks-create-vnetpeering-arm-ps.md) para más información.
* Aunque la comunicación entre las máquinas virtuales en redes virtuales emparejadas no tiene restricciones de ancho de banda adicionales, hay un ancho de banda de red máximo en función del tamaño de máquina virtual que se aplica. Para más información sobre el ancho de banda de red máximo para los diferentes tamaños de máquina virtual, consulte los artículos sobre los tamaños de máquina virtual [Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [Linux](../virtual-machines/virtual-machines-linux-sizes.md).

![Emparejamiento de VNET básico](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Conectividad
Después de emparejar dos redes virtuales, los roles de máquinas virtuales o servicios en la nube en la red virtual se pueden conectar directamente con otros recursos conectados a la red virtual emparejada. Las dos redes virtuales tienen conectividad de nivel IP completa.

La latencia de red en un recorrido de ida y vuelta entre dos máquinas virtuales de redes virtuales emparejadas es la misma que en una red virtual única. El rendimiento de la red se basa en el ancho de banda que se permite para la máquina virtual, en proporción con su tamaño. No hay restricciones adicionales con respecto al ancho de banda en el emparejamiento.

El tráfico entre las máquinas virtuales de redes virtuales emparejadas se enruta directamente con la infraestructura de back-end de Azure, no a través de una puerta de enlace.

Las máquinas virtuales conectadas a una red virtual pueden acceder a los puntos de conexión con equilibrio de carga (ILB) internos en la red virtual emparejada. Los grupos de seguridad de red (NSG) pueden aplicarse en cualquier red virtual para bloquear el acceso, si se quiere, a otra red o subred virtual.

Al configurar el emparejamiento, puede abrir o cerrar las reglas del grupo de seguridad de red entre las redes virtuales. Si abre la conectividad completa entre redes virtuales emparejadas (que es la opción predeterminada), puede aplicar los grupos de seguridad de red a subredes específicas o máquinas virtuales para bloquear o denegar un acceso específico. Consulte el artículo sobre [grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre estos grupos.

La resolución de nombres DNS internos que proporciona Azure para las máquinas virtuales no funcionará en las redes virtuales emparejadas. Las máquinas virtuales tienen nombres DNS internos que solo se pueden resolver en la red virtual local. No obstante, puede configurar máquinas virtuales conectadas a redes virtuales emparejadas como servidores DNS para una red virtual. Consulte el artículo [Resolución de nombres mediante su propio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) para más detalles.

## <a name="service-chaining"></a>Encadenamiento de servicios
Puede configurar rutas definidas por el usuario que señalan a las máquinas virtuales de redes virtuales emparejadas como dirección IP de "próximo salto", como se muestra en el diagrama de este mismo artículo. Esto permite lograr el encadenamiento de servicio, que permite dirigir el tráfico de una red virtual a un dispositivo virtual que se ejecuta en una red virtual emparejada a través de rutas definidas por el usuario.

También puede crear eficazmente entornos del tipo de concentrador y radio, en los que el concentrador puede hospedar componentes de la infraestructura, como una aplicación virtual de red. Luego, todas las redes virtuales de radio se pueden emparejar con ellos, así como un subconjunto del tráfico a las aplicaciones que se ejecutan en la red virtual del concentrador. En resumen, el emparejamiento de máquinas virtuales permite que la dirección IP de próximo salto de la ruta definida por el usuario sea la dirección IP de una máquina virtual de la red virtual emparejada. Consulte el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md) para obtener información adicional sobre dichas rutas.

## <a name="gateways-and-on-premises-connectivity"></a>Puertas de enlace y conectividad local
Todas las redes virtuales, con independencia de que están emparejadas con otra, pueden conservar su propia puerta de enlace y usarla para conectarse localmente. Los usuarios también pueden configurar [conexiones entre redes virtuales](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) mediante puertas de enlace, aunque las redes virtuales estén emparejadas.

Cuando están configuradas ambas opciones de interconectividad de red virtual, el tráfico entre las redes virtuales se propaga a través de la configuración de emparejamiento (es decir, a través de la red troncal de Azure).

Cuando las redes virtuales están emparejadas, los usuarios también pueden ajustar la configuración para utilizar la puerta de enlace de la red virtual emparejada como punto de tránsito a local. En este caso, la red virtual que usa una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual solo puede tener una puerta de enlace. La puerta de enlace puede ser local o remota (en la red virtual emparejada), como se muestra en la siguiente imagen:

![Tránsito de emparejamiento de VNET](./media/virtual-networks-peering-overview/figure02.png)

No se admite el tránsito de puerta de enlace en la relación de emparejamiento entre las redes virtuales creadas mediante modelos de implementación diferentes. Se deben haber creado ambas redes virtuales en la relación de emparejamiento mediante Resource Manager para que funcione el tránsito de puerta de enlace.

Cuando las redes virtuales que comparten una única conexión de Azure ExpressRoute están emparejadas, el tráfico entre ellas atraviesa la relación de emparejamiento (es decir, la red troncal de Azure). Los usuarios pueden seguir usando las puertas de enlace locales de cada red virtual para conectarse al circuito local. Como alternativa, puede utilizar una puerta de enlace compartida y configurar el tránsito para la conectividad local.

## <a name="provisioning"></a>Aprovisionamiento
El emparejamiento de VNET es una operación con privilegios. Es una función independiente en el espacio de nombres VirtualNetworks. Los usuarios pueden tener derechos específicos para autorizar el emparejamiento. Los usuarios que tengan acceso de lectura y escritura a la red virtual heredan automáticamente estos derechos.

Los usuarios que ya sean administradores o tengan privilegios para emparejar pueden iniciar operaciones de emparejamiento en otra red virtual. Si hay una solicitud coincidente de emparejamiento en el otro lado y se cumplen los restantes requisitos, se establecerá el emparejamiento.

Para más información acerca de cómo establecer un emparejamiento entre dos redes virtuales, consulte los artículos de la sección [Pasos siguientes](#next-steps).

## <a name="limits"></a>límites
Hay límites en el número de emparejamientos de que se permiten en una red virtual individual. Para más información, consulte [Límites de red de Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Precios
Hay un cargo nominal para el tráfico de entrada y salida que utiliza un emparejamiento de red virtual. Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="a-namenext-stepsanext-steps"></a><a name="next-steps"></a>Pasos siguientes
Aprenda a crear un emparejamiento de red virtual con:

* [Portal de Azure](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Plantilla de Azure Resource Manager](virtual-networks-create-vnetpeering-arm-template-click.md)



<!--HONumber=Feb17_HO1-->


