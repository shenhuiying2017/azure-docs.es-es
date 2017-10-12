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
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 082cd8a6cf50f76c89fe5995047396c734f83034
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-peering"></a>Emparejamiento de redes virtuales de Azure

[La red virtual de Azure (VNet)](virtual-networks-overview.md) es su propio espacio de red privada en Azure que permite conectar de forma segura los recursos de Azure entre sí.

El emparejamiento de redes virtuales le permite conectar sin problemas redes virtuales. Una vez emparejadas, las redes virtuales aparecen como una sola a efectos de conectividad. Las máquinas virtuales de las redes virtuales emparejadas pueden comunicarse directamente entre sí.
El tráfico entre máquinas virtuales en las redes virtuales emparejadas se enruta a través de la infraestructura de red troncal de Microsoft, de forma muy parecida a como se enruta el tráfico entre máquinas virtuales de la misma red virtual a través únicamente de direcciones IP *privadas*.

>[!IMPORTANT]
> Puede emparejar redes virtuales de diferentes regiones de Azure. Esta funcionalidad actualmente está en su versión preliminar. Puede [registrar la suscripción para la versión preliminar](virtual-network-create-peering.md). Está generalmente disponible el emparejamiento de redes virtuales en las mismas regiones.
>

Las ventajas del uso del emparejamiento de redes virtuales son las siguientes:

* El tráfico que se dirige por los emparejamientos de redes virtuales es completamente privado. Atraviesa la red troncal de Microsoft y no hay ni Internet pública ni puertas de enlace implicadas.
* Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
* Capacidad de usar recursos en una red virtual desde otra red virtual una vez emparejados.
* El emparejamiento de redes virtuales le ayuda a transferir datos a través de las suscripciones de Azure, modelos de implementación y a través de regiones de Azure (versión preliminar).
* Posibilidad de emparejar redes virtuales creadas mediante Azure Resource Manager o de emparejar una red virtual creada mediante Resource Manager con una red virtual creada mediante el modelo de implementación clásica. Consulte el artículo donde se [describen los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para tener más información sobre las diferencias entre los dos modelos de implementación.

## <a name="requirements-constraints"></a>Requisitos y restricciones

* El emparejamiento de redes virtuales en las mismas regiones tiene disponibilidad general. El emparejamiento de redes virtuales en diferentes regiones está actualmente en versión preliminar en Centro-oeste de EE. UU., Canadá Central y Oeste de EE. UU. 2. Puede [registrar la suscripción para la versión preliminar.](virtual-network-create-peering.md)
    > [!WARNING]
    > Los emparejamientos de redes virtuales que se crean en este escenario no pueden tener el mismo nivel de disponibilidad y confiabilidad que los escenarios de la versión de disponibilidad general. Es posible que los emparejamientos de redes virtuales tengan funcionalidades limitadas y no estén disponibles en todas las regiones de Azure. Para las notificaciones más al día sobre disponibilidad y estado de esta característica, consulte la página de [actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) .

* Deben tener además espacios de direcciones IP que no se solapen.
* No se pueden agregar espacios de direcciones a una red virtual ni eliminarse de esta una vez que una red virtual se empareja con otra red virtual.
* El emparejamiento de red virtual se realiza entre dos redes virtuales. No hay ninguna relación transitiva derivada entre emparejamientos. Por ejemplo, si la red virtual A está emparejada con la red virtual B y la red virtual B está emparejada con la red virtual C, la red virtual A *no* está emparejada con la red virtual C.
* Puede emparejar redes virtuales que existan en dos suscripciones distintas, siempre y cuando un usuario con privilegios (consulte los [permisos específicos](create-peering-different-deployment-models-subscriptions.md#permissions)) de ambas suscripciones lo autorice y las suscripciones estén asociadas al mismo inquilino de Azure Active Directory. Puede usar una instancia de [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar redes virtuales en suscripciones asociadas a diferentes inquilinos de Active Directory.
* Se pueden emparejar redes virtuales si ambas se crean mediante el modelo de implementación de Resource Manager o si una se crea mediante el modelo de implementación de Resource Manager y la otra se crea mediante el modelo de implementación clásica. No obstante, las redes virtuales creadas mediante el modelo de implementación clásica no se pueden emparejar entre sí. Puede usar [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) para conectar redes virtuales creadas mediante el modelo de implementación clásica.
* Aunque la comunicación entre las máquinas virtuales en redes virtuales emparejadas no tiene restricciones de ancho de banda adicionales, hay un ancho de banda de red máximo en función del tamaño de máquina virtual que se aplica. Para más información sobre el ancho de banda de red máximo para los diferentes tamaños de máquina virtual, consulte los artículos sobre los tamaños de máquina virtual [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Emparejamiento básico de red virtual](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Conectividad

Después de que se hayan emparejado las redes virtuales, los recursos de cualquiera de las máquinas virtuales se pueden conectar directamente con los recursos de la otra red virtual emparejada.

La latencia de red entre las máquinas virtuales de redes virtuales emparejadas en la misma región es la misma que en una única red virtual. El rendimiento de la red se basa en el ancho de banda que se permite para la máquina virtual, en proporción a su tamaño. No hay restricciones adicionales con respecto al ancho de banda en el emparejamiento.

El tráfico entre las máquinas virtuales de redes virtuales emparejadas se enruta directamente a través de la infraestructura de red troncal de Microsoft, y no a través de una puerta de enlace o de una red Internet pública.

Las máquinas virtuales de una red virtual pueden acceder al equilibrador de carga interna de la red virtual emparejada de la misma región. La compatibilidad con el equilibrador de carga interno no se extiende por las redes virtuales emparejadas globalmente en la versión preliminar. La versión de disponibilidad general del emparejamiento de las redes virtuales globales será compatible con el equilibrador de carga interno.

Si se desea, se pueden aplicar grupos de seguridad de red en cualquier red virtual para bloquear el acceso a otras redes o subredes virtuales.
Al configurar el emparejamiento de red virtual, puede abrir o cerrar las reglas del grupo de seguridad de red entre las redes virtuales. Si abre la conectividad completa entre las redes virtuales emparejadas (que es la opción predeterminada), puede aplicar grupos de seguridad de red a subredes o máquinas virtuales concretas para bloquear o denegar acceso específico. Para más información sobre los grupos de seguridad de red, lea el artículo de [introducción a los grupos de seguridad de red](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Encadenamiento de servicios

Puede configurar las rutas definidas por el usuario que apuntan a máquinas virtuales de redes virtuales emparejadas como la dirección IP del "próximo salto" para permitir el encadenamiento de servicios. El encadenamiento de servicios permite dirigir el tráfico desde una red virtual hasta una aplicación virtual de una red virtual emparejada mediante rutas definidas por el usuario.

También puede crear eficazmente entornos del tipo de concentrador y radio, en los que el concentrador puede hospedar componentes de la infraestructura, como una aplicación virtual de red. Todas las redes virtuales de radio se pueden emparejar con la red virtual de concentrador. El tráfico puede fluir por las aplicaciones virtuales de red que se ejecutan en la red virtual de concentrador. En resumen, el emparejamiento de red virtual permite que la dirección IP de próximo salto de la ruta definida por el usuario sea la dirección IP de una máquina virtual de la red virtual emparejada. Para aprender más sobre las rutas definidas por el usuario, lea el artículo de [introducción a las rutas definidas por el usuario](virtual-networks-udr-overview.md). Para aprender a crear una [topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)

## <a name="gateways-and-on-premises-connectivity"></a>Puertas de enlace y conectividad local

Todas las redes virtuales, con independencia de que estén emparejadas con otra, pueden tener su propia puerta de enlace y usarla para conectarse a una red local. También puede configurar [conexiones de red virtual a red virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) mediante puertas de enlace, incluso aunque las redes virtuales estén emparejadas.

Cuando están configuradas las dos opciones de interconectividad de redes virtual, el tráfico entre las redes virtuales se propaga a través de la configuración del emparejamiento (es decir, a través de la red troncal de Azure).

Cuando las redes virtuales están emparejadas en la misma región, también puede configurar la puerta de enlace de la red virtual emparejada como punto de tránsito a una red local. En este caso, la red virtual que usa una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual no puede tener más de una puerta de enlace. La puerta de enlace puede ser local o remota (en la red virtual emparejada), como se muestra en la siguiente imagen:

![tránsito de emparejamiento de redes virtuales](./media/virtual-networks-peering-overview/figure04.png)

No se admite el tránsito de puerta de enlace en la relación de emparejamiento entre las redes virtuales creadas mediante modelos de implementación diferentes o regiones distintas. Para que funcione el tránsito de puerta de enlace, ambas redes virtuales de la relación de emparejamiento se deben haber creado mediante Resource Manager y deben estar en la misma región. Las redes virtuales emparejadas globalmente no admiten el tránsito de puerta de enlace de momento.

Cuando las redes virtuales que comparten una única conexión de Azure ExpressRoute están emparejadas, el tráfico entre ellas atraviesa la relación de emparejamiento (es decir, la red troncal de Azure). Puede seguir usando las puertas de enlace locales de cada red virtual para conectarse al circuito local. Como alternativa, puede utilizar una puerta de enlace compartida y configurar el tránsito para la conectividad local.

## <a name="permissions"></a>Permisos

El emparejamiento de red virtual es una operación que requiere privilegios. Es una función independiente en el espacio de nombres VirtualNetworks. Los usuarios pueden tener derechos específicos para autorizar el emparejamiento. Los usuarios que tengan acceso de lectura y escritura a la red virtual heredan automáticamente estos derechos.

Los usuarios que ya sean administradores o tengan privilegios para emparejar pueden iniciar operaciones de emparejamiento en otra red virtual. El nivel mínimo de permiso necesario es el de colaborador de red. Si hay una solicitud coincidente de emparejamiento en el otro lado y se cumplen el resto de requisitos, se establecerá el emparejamiento.

Por ejemplo, si está emparejando redes virtuales denominadas myvirtual networkA y myvirtual networkB, su cuenta debe tener asignado el rol o los permisos mínimos siguientes para cada red virtual:

|Red virtual|Modelo de implementación|Rol|Permisos|
|---|---|---|---|
|myvirtual networkA|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|myvirtual networkB|Resource Manager|[Colaborador de la red](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clásico|[Colaborador de la red clásica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Supervisión

Cuando se emparejan dos redes virtuales creadas mediante el Administrador de recursos, debe configurarse un emparejamiento para cada red virtual en el emparejamiento.
Puede supervisar el estado de la conexión de emparejamiento. El estado de emparejamiento puede ser uno de los siguientes:

* **Iniciado**: cuando se crea el emparejamiento a la segunda red virtual desde la primera red virtual, el estado de emparejamiento es Iniciado.

* **Conectado**: cuando se crea el emparejamiento desde la segunda red virtual a la primera red virtual, el estado de emparejamiento es Conectado. Si consulta el estado de emparejamiento de la primera red virtual, verá que ha cambiado de Iniciado a Conectado. El emparejamiento no se habrá establecido correctamente mientras el estado de ambos emparejamientos de red virtual no sea Conectado.

* **Desconectado**: si uno de los vínculos de emparejamiento se elimina después de que se estableció una conexión, el estado de emparejamiento es Desconectado.

## <a name="troubleshoot"></a>Solución de problemas

Para solucionar problemas de tráfico a través de la conexión de emparejamiento, puede [comprobar sus rutas eficaces.](virtual-network-routes-troubleshoot-portal.md)

También puede solucionar problemas con la conectividad de una máquina virtual en una red virtual emparejada con la [comprobación de conectividad](../network-watcher/network-watcher-connectivity-portal.md) de Network Watcher. La comprobación de conectividad permite ver cómo se enruta directamente de la interfaz de red de la máquina virtual de origen a la interfaz de red de la máquina virtual de destino.

## <a name="limits"></a>límites

Hay límites en el número de emparejamientos de que se permiten en una red virtual individual. El número predeterminado de emparejamientos es 50. Puede aumentar el número de emparejamientos. Para más información, revise los [límites de red de Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Precios

Hay un cargo nominal para el tráfico de entrada y salida que utiliza una conexión de emparejamiento de red virtual. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network)para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

* Realice el tutorial de emparejamiento de redes virtuales. Un emparejamiento de redes virtuales se crea entre redes virtuales creadas mediante el mismo modelo de implementación o mediante modelos distintos que existen en la misma suscripción o en cualquier suscripción diferente. Realice el tutorial para uno de los siguientes escenarios:

    |Modelo de implementación de Azure  | La suscripción  |
    |---------|---------|
    |Ambas mediante Resource Manager |[La misma](virtual-network-create-peering.md)|
    | |[Diferente](create-peering-different-subscriptions.md)|
    |Una mediante Resource Manager y la otra clásica     |[La misma](create-peering-different-deployment-models.md)|
    | |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Aprenda a crear una [topología de red de concentrador y radio](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Información acerca de toda la [configuración de emparejamiento de red virtual y cómo cambiarla](virtual-network-manage-peering.md)
