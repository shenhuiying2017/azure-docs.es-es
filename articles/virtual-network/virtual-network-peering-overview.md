
<properties
   pageTitle="Emparejamiento de redes virtuales de Azure | Microsoft Azure"
   description="Más información sobre el emparejamiento de VNET en Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="narayanannamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# Emparejamiento de VNET

El emparejamiento de VNET es un mecanismo que conecta dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecen como una sola a efectos de conectividad. No obstante, se administran como recursos independientes, pero las máquinas virtuales de estas redes virtuales pueden comunicarse entre sí directamente mediante sus direcciones IP privadas.

El tráfico entre máquinas virtuales en las redes virtuales emparejadas se enruta a través de la infraestructura de Azure, de forma muy parecida a como se enruta el tráfico entre máquinas virtuales de la misma red virtual. Estas son algunas de las ventajas que reporta el uso del emparejamiento de VNET:

- Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
- Capacidad para utilizar recursos como aparatos de red y puertas de enlace de VPN como puntos de tránsito en una red virtual emparejada.
- Capacidad para conectar una red virtual que utiliza el modelo de Azure Resource Manager a una red virtual que utiliza el modelo de implementación clásico y para habilitar la conectividad completa entre los recursos de estas redes virtuales.

Requisitos y aspectos clave del emparejamiento de VNET:

- Las dos redes virtuales emparejadas deben estar en la misma región de Azure.
- Los espacios de las direcciones IP de las redes virtuales emparejadas no deben superponerse.
- El emparejamiento de VNET se realiza entre dos redes virtuales y no hay ninguna relación transitiva derivada. Por ejemplo, si la red virtual A está emparejada con la red virtual B y la red virtual B esta emparejada con la red virtual de C, no implica necesariamente que la red virtual esté emparejada con la red virtual C.
- El emparejamiento se puede establecer entre redes virtuales de dos suscripciones distintas, siempre y cuando un usuario con privilegios de ambas suscripciones lo autorice.
- Una red virtual que use el modelo de implementación de Resource Manager se puede emparejar con otra red virtual que use ese mismo modelo, o bien con una red virtual que utilice el modelo de implementación clásico. Sin embargo, las redes virtuales que usen el modelo de implementación clásico no se pueden emparejar entre ellas.
- Aunque la comunicación entre máquinas virtuales de redes virtuales emparejadas no tiene ninguna restricción de ancho de banda adicional, se sigue aplicando el límite de ancho de banda según el tamaño de la máquina virtual.


![Emparejamiento de VNET básico](./media/virtual-networks-peering-overview/figure01.png)

## Conectividad
Después de que dos redes virtuales se hayan emparejado, una máquina virtual (rol de trabajo o web) de la red virtual se puede conectar directamente con otras máquinas virtuales de la red virtual emparejada. Estas dos redes conectividad total a nivel de IP.

La latencia de red en un recorrido de ida y vuelta entre dos máquinas virtuales de redes virtuales emparejadas es la misma que la de un recorrido en una red virtual local. El rendimiento de la red se basa en el ancho de banda que se permite para la máquina virtual, en proporción con su tamaño. No hay restricciones adicionales con respecto al ancho de banda.

El tráfico entre las máquinas virtuales de redes virtuales emparejadas se enruta directamente con la infraestructura de back-end de Azure, no a través de una puerta de enlace.

Las máquinas virtuales de una red virtual pueden acceder a los puntos de conexión con equilibrio de carga interna (ILB) de la red virtual emparejada. Si se desea, se pueden aplicar grupos de seguridad de red (NSG) en cualquier red virtual para bloquear el acceso a otras redes o subredes virtuales.

Cuando los usuarios configuran el emparejamiento, pueden abrir o cerrar las reglas de NSG entre las redes virtuales. Si el usuario elige abrir toda la conectividad entre redes virtuales emparejadas (que es la opción predeterminada), podrá usar los NSG en subredes o máquinas virtuales concretas para bloquear o denegar accesos específicos.

La resolución de nombres DNS internos que proporciona Azure para las máquinas virtuales no funciona en las redes virtuales emparejadas. Las máquinas virtuales tienen nombres DNS internos que solo se puedan resolver en la red virtual local. Sin embargo, los usuarios pueden configurar las máquinas virtuales que se ejecuten en redes virtuales emparejadas como servidores DNS para una red virtual.

## Encadenamiento de servicios
Los usuarios pueden configurar tablas de rutas definidas por el usuario que señalan a las máquinas virtuales de redes virtuales emparejadas como dirección IP del "próximo salto", como se mostrará en el diagrama de este mismo artículo. Esto permite a los usuarios lograr un encadenamiento de servicios, a través del que pueden dirigir el tráfico de una red virtual a una aplicación virtual que se ejecuta en una red virtual emparejada a través de tablas de rutas definidas por el usuario.

Los usuarios también pueden crear eficazmente entornos del tipo de concentrador y radio en los que el concentrador puede hospedar componentes de la infraestructura, como una aplicación virtual de red. Luego, todas las redes virtuales de radio se pueden emparejar con ellos, así como un subconjunto del tráfico a las aplicaciones que se ejecutan en la red virtual del concentrador. En resumen, el emparejamiento de VNET permite que la dirección IP de próximo salto de la "tabla de rutas definida por el usuario" sea la dirección IP de una máquina virtual de la red virtual emparejada.

## Puertas de enlace y conectividad local
Todas las redes virtuales, con independencia de que están emparejadas con otra, pueden conservar su propia puerta de enlace y usarla para conectarse localmente. Los usuarios también pueden configurar [conexiones entre redes virtuales](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) mediante puertas de enlace, aunque las redes virtuales estén emparejadas.

Cuando están configuradas las dos opciones de interconectividad de redes virtual, el tráfico entre las redes virtuales se propaga a través de la configuración del emparejamiento (es decir, a través de la red troncal de Azure).

Cuando las redes virtuales están emparejadas, los usuarios también pueden configurar la puerta de enlace de la red virtual emparejada como punto de tránsito a local. En este caso, la red virtual que usa una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual no puede tener más de una puerta de enlace. Puede ser una puerta de enlace local o una puerta de enlace remota (en la red virtual emparejada), como se muestra en la siguiente imagen.

El tránsito de puertas de enlace no se admite en la relación de emparejamiento entre redes virtuales que usan el modelo de Resource Manager y las usuarios utilizan el modelo de implementación clásico. Para que un tránsito de puertas de enlace funcione, es preciso que las dos redes virtuales de la relación de emparejamiento usen el modelo de implementación de Resource Manager.

Cuando las redes virtuales que comparten una única conexión de Azure ExpressRoute están emparejadas, el tráfico entre ellas atraviesa la relación de emparejamiento (es decir, la red troncal de Azure). Los usuarios pueden seguir usando las puertas de enlace locales de cada red virtual para conectarse al circuito local. Como alternativa, puede utilizar una puerta de enlace compartida y configurar el tránsito para la conectividad local.

![Tránsito de emparejamiento de VNET](./media/virtual-networks-peering-overview/figure02.png)

## Aprovisionamiento
El emparejamiento de VNET es una operación con privilegios. Es una función independiente en el espacio de nombres VirtualNetworks. Los usuarios pueden tener derechos específicos para autorizar el emparejamiento. Los usuarios que tengan acceso de lectura y escritura a la red virtual heredan automáticamente estos derechos.

Los usuarios que ya sean administradores o tengan privilegios para emparejar pueden iniciar operaciones de emparejamiento en otra red virtual. Si hay una solicitud coincidente de emparejamiento en el otro lado y se cumplen los restantes requisitos, se establecerá el emparejamiento.

Para más información acerca de cómo establecer un emparejamiento de VNET entre dos redes virtuales, consulte los artículos de la sección "Pasos siguientes".

## Límites
Hay límites en el número de emparejamientos de que se permiten en una red virtual individual. Para más información, consulte [Límites de red de Azure](../azure-subscription-service-limits.md#networking-limits).

## Precios
El emparejamiento de VNET será gratuito durante el período de evaluación. Una vez que se lance, habrá un cargo nominal por el tráfico de entrada y salida que utilice el emparejamiento. Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network).


## Pasos siguientes
- [Configuración del emparejamiento entre redes virtuales](virtual-networks-create-vnetpeering-arm-portal.md).
- Más información sobre [NSG](virtual-networks-nsg.md).
- Más información sobre [rutas definidas por el usuario y reenvío IP](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0810_2016-->