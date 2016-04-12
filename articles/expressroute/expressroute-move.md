<properties
   pageTitle="Transición de circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager | Microsoft Azure"
   description="Esta página proporciona una visión general de lo que necesita saber sobre cómo realizar la transición del modelo de implementación clásica al modelo de implementación de Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# Transición de los circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager

En este artículo se ofrece una visión general de lo que significa mover un circuito ExpressRoute desde el modelo de implementación clásica al modelo de Resource Manager.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

Se puede utilizar un solo circuito ExpressRoute para conectarse a redes virtuales (VNets) implementadas tanto en el modelo de implementación clásica como en el modelo de Resource Manager. Ahora, se puede vincular un circuito ExpressRoute, con independencia de cómo se cree, a redes virtuales en ambos modelos de implementación.

![](./media/expressroute-move/expressroute-move-1.png)

## Circuitos ExpressRoute creados en el modelo de implementación clásica

Los circuitos ExpressRoute creados en el modelo de implementación clásica tendrán que moverse primero al modelo de implementación de Resource Manager para permitir la conectividad con los modelos de implementación clásica y de Resource Manager. Mientras se mueve una conexión, no habrá pérdida de conectividad ni interrupciones. Se conservarán todos los vínculos entre el circuito y la red virtual en el modelo de implementación clásica (dentro de la misma suscripción y entre suscripciones). Finalizada la transición, el circuito ExpressRoute funciona exactamente igual que un circuito ExpressRoute creado en el modelo de implementación de Resource Manager. Ahora, podrá crear conexiones a redes virtuales en el modelo de implementación de Resource Manager.

Cuando el circuito ExpressRoute se haya pasado al modelo de implementación de Resource Manager, solo podrá administrar el ciclo de vida del circuito ExpressRoute con el modelo de implementación de Resource Manager. Esto significa que operaciones tales como agregar, actualizar y eliminar emparejamientos, actualizar las propiedades del circuito, como el ancho de banda, el SKU y el tipo de facturación, y eliminar circuitos, solo se pueden realizar en el modelo de implementación de Resource Manager. Consulte la sección siguiente sobre los circuitos creados en el modelo de implementación de Resource Manager para más información sobre cómo administrar el acceso a ambos modelos de implementación.

No será necesario que su proveedor de conectividad intervenga en la transición.

## Circuitos ExpressRoute creados en el modelo de implementación de Resource Manager

Puede habilitar los circuitos ExpressRoute creados en el modelo de implementación de Resource Manager para que sean accesibles desde ambos modelos de implementación. Cualquier circuito ExpressRoute de su suscripción se puede habilitar para el acceso desde ambos modelos de implementación.

- Los circuitos ExpressRoute que se crearon en el modelo de implementación de Resource Manager no tendrán acceso al modelo de implementación clásica de forma predeterminada.
- Los circuitos ExpressRoute que se han movido desde el modelo de implementación clásica al modelo de Resource Manager serán accesibles desde ambos modelos de implementación de forma predeterminada.
- Un circuito ExpressRoute siempre tendrá acceso al modelo de implementación de Resource Manager con independencia de si se creó en el modelo de implementación de Resource Manager o en el modelo de implementación clásica. Esto significa que puede crear conexiones a redes virtuales creadas en el modelo de implementación de Resource Manager siguiendo las instrucciones que se indican en [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md). 
- El acceso al modelo de implementación clásica se controla mediante el parámetro "allowClassicOperations" en el circuito ExpressRoute. 

>[AZURE.IMPORTANT] Se aplican todas las cuotas documentadas en la página de [límites de servicio](../azure-subscription-service-limits.md). Por ejemplo, un circuito estándar puede tener como máximo 10 vínculos de red virtual/conexiones entre los modelos de implementación clásica y de Resource Manager.


### Control del acceso al modelo de implementación clásica

Puede permitir que un solo circuito ExpressRoute se vincule a redes virtuales en ambos modelos de implementación; para ello, configure el parámetro "allowClassicOperations" del circuito ExpressRoute.

Al establecer "allowClassicOperations" en TRUE podrá vincular redes virtuales de ambos modelos de implementación al circuito ExpressRoute. Para vincular redes virtuales en el modelo de implementación clásica, siga las instrucciones que se describen en [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md). Para vincular redes virtuales en el modelo de implementación de Resource Manager, siga las instrucciones que se describen en [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).

Al establecer "allowClassicOperations" en FALSE se bloquea el acceso al circuito en el modelo de implementación clásica. Sin embargo, todos los vínculos a redes virtuales en el modelo de implementación clásica se conservan. En este caso, el circuito ExpressRoute no estará visible en el modelo de implementación clásica.

### Operaciones admitidas en el modelo de implementación clásica

Las siguientes operaciones clásicas se admiten en un circuito ExpressRoute cuando el parámetro "allowClassicOperations" está establecido en TRUE.

 - Obtener información del circuito ExpressRoute
 - Crear, actualizar, obtener y eliminar vínculos de red virtual a redes virtuales clásicas
 - Crear, actualizar, obtener y eliminar autorizaciones de vínculo de red virtual para la conectividad entre suscripciones

No podrá realizar las siguientes operaciones clásicas cuando "allowClassicOperations" está establecido en TRUE.

 - Crear, actualizar, obtener y eliminar emparejamientos BGP para emparejamientos públicos y privados de Azure y emparejamientos de Microsoft
 - Eliminar circuitos de ExpressRoute

## Comunicación entre los modelos de implementación clásica y de Resource Manager

El circuito ExpressRoute actuará como puente entre los modelos de implementación clásica y de Resource Manager. El tráfico entre máquinas virtuales implementadas en redes virtuales en el modelo de implementación clásica y las implementadas en redes virtuales en el modelo de implementación de Resource Manager fluirá a través de ExpressRoute si ambas redes virtuales están vinculadas al mismo circuito ExpressRoute. El rendimiento agregado se verá limitado por la capacidad de proceso de la puerta de enlace de red virtual. En tales casos, el tráfico no pasará al proveedor de conectividad o a las redes. El flujo del tráfico entre las redes virtuales estará completamente contenido en la red de Microsoft.

## Acceso a recursos de emparejamiento público de Azure y de Microsoft

Puede seguir teniendo acceso a los recursos a los que normalmente se accede mediante el emparejamiento público de Azure y el emparejamiento de Microsoft sin interrupciones.

## Lo que se admite

En esta sección se describe lo que se admite a través de esta funcionalidad.

 - Un solo circuito ExpressRoute puede utilizarse para tener acceso a las redes virtuales implementadas en los modelos de implementación clásica y de Resource Manager.
 - Puede mover un circuito ExpressRotue del modelo de implementación clásica al modelo de implementación de Resource Manager. Una vez realizada la transición, el circuito ExpressRoute funcionará exactamente igual que cualquier otro circuito ExpressRoute creado en el modelo de implementación de Resource Manager.
 - Solo se puede mover el circuito ExpressRoute. Los vínculos del circuito, las redes virtuales y las puertas de enlace de VPN no se mueven mediante esta operación.
 - Cuando el circuito ExpressRoute se haya pasado al modelo de implementación de Resource Manager, solo podrá administrar el ciclo de vida del circuito ExpressRoute con el modelo de implementación de Resource Manager. Esto significa que operaciones tales como agregar, actualizar y eliminar emparejamientos, actualizar las propiedades del circuito, como el ancho de banda, el SKU y el tipo de facturación, y eliminar circuitos, solo se pueden realizar en el modelo de implementación de Resource Manager.
 - El circuito ExpressRoute actuará como puente entre los modelos de implementación clásica y de Resource Manager. El tráfico entre las máquinas virtuales implementadas en las redes virtuales del modelo de implementación clásica y las implementadas en las redes virtuales del modelo de implementación de Resource Manager fluirá a través de ExpressRoute si ambas redes virtuales están vinculadas al mismo circuito ExpressRoute. 
 - Conectividad entre suscripciones en los modelos de implementación clásica y de Resource Manager.

## Lo que NO se admite

En esta sección se describe lo que no se admite con esta funcionalidad.

 - Mover vínculos, puertas de enlace y redes virtuales del modelo de implementación clásica al modelo de implementación de Resource Manager.
 - Administrar el ciclo de vida del circuito ExpressRoute desde el modelo de implementación clásica.
 - Compatibilidad con RBAC en el modelo de implementación clásica. No podrá realizar controles RBAC para el circuito en el modelo de implementación clásica. Cualquier administrador o coadministrador de la suscripción podrá vincular redes virtuales al circuito o desvincularlas de este.

## Configuración

Siga las instrucciones que se describen en [Transición de circuitos ExpressRoute desde el modelo de implementación clásica al modelo de implementación de Resource Manager](expressroute-howto-move-arm.md).

## Pasos siguientes

- Para obtener información sobre los flujos de trabajo, consulte [Flujos de trabajo de aprovisionamiento de circuitos y estados de circuito de ExpressRoute](expressroute-workflows.md).
- Configure su conexión ExpressRoute.

	- [Creación de un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
	- [Configuración del enrutamiento](expressroute-howto-routing-arm.md)
	- [Vinculación de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->