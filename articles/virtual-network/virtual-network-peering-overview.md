<properties
   pageTitle="Emparejamiento de redes virtuales de Azure | Microsoft Azure"
   description="Más información sobre el emparejamiento de VNet en Azure."
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

# Emparejamiento de VNet

El emparejamiento de VNET es un mecanismo para conectar dos redes virtuales de la misma región mediante la red troncal de Azure. Una vez emparejadas, las dos redes virtuales aparecerán como una sola a efectos de conectividad. Todavía se pueden administrar como recursos independientes, pero las máquinas virtuales en estas redes virtuales pueden comunicarse entre sí directamente mediante la dirección IP privada. El tráfico entre máquinas virtuales en las redes virtuales emparejadas se enrutará a través de la infraestructura de Azure, como el tráfico entre máquinas virtuales de la misma red virtual. Estas son algunas de las ventajas del emparejamiento de VNet:

- Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
- Capacidad para utilizar los recursos como dispositivos de red virtual y puertas de enlace de VPN de redes virtuales emparejadas (tránsito).
- Conexión de la red virtual de Resource Manager a una red virtual clásica y habilitación de toda la conectividad entre los recursos de estas redes virtuales

Requisitos y aspectos clave del emparejamiento de VNet:

- Las dos redes virtuales emparejadas deben estar en la misma región de Azure
- Las redes virtuales emparejadas deben tener espacio de dirección IP no superpuesto
- El emparejamiento de VNet se realiza entre dos redes virtuales y hay ninguna relación transitiva derivada, es decir, que VNet A se empareje con VNet B y VNet B se empareje con VNet C no significa que VNet A esté emparejada con VNet C
- El emparejamiento puede establecerse entre redes virtuales de dos suscripciones distintas, siempre y cuando un usuario con privilegios de las suscripciones correspondientes lo autorice
- Se puede emparejar una red virtual de Resource Manager con otra VNet de Resource Manager o una clásica, pero las redes virtuales clásicas no pueden emparejarse entre sí
- Aunque la comunicación entre las máquinas virtuales de redes virtuales emparejadas no tiene ninguna restricción de ancho de banda adicional, se seguirá aplicando el límite de ancho de banda según el tamaño de máquina virtual.


![Emparejamiento de VNet básico](./media/virtual-networks-peering-overview/figure01.png)

## Conectividad 
Una vez emparejadas dos redes virtuales, una máquina virtual (rol de trabajo o web) de la red virtual puede conectarse directamente con otras de la red virtual emparejada. Tendrán conectividad de nivel IP completo. La latencia de red de ida y vuelta entre dos máquinas virtuales de redes virtuales emparejadas será la misma que en la red virtual local. El rendimiento de la red se basará en el ancho de banda permitido para la máquina virtual proporcional a su tamaño, no habrá ninguna restricción adicional en el ancho de banda permitido. El tráfico entre las máquinas virtuales en redes virtuales emparejadas se enruta directamente con la infraestructura de back-end de Azure y no a través de una puerta de enlace.

Las máquinas virtuales de una red virtual podrán tener acceso a los puntos de conexión con equilibrio de carga interno (ILB) de la red virtual emparejada. Los grupos de seguridad de red pueden aplicarse en cualquier red virtual para bloquear, si se quiere, el acceso a otra red o subred. Cuando el usuario configura el emparejamiento tiene la opción de abrir o cerrar las reglas del grupo de seguridad de red entre las redes virtuales. Si el usuario elige abrir toda la conectividad entre redes virtuales emparejadas (opción predeterminada), podrán usar NSG en subredes específicas o máquinas virtuales para bloquear o denegar accesos específicos.

La resolución de nombres DNS internos que proporciona Azure para las máquinas virtuales no funcionará en las redes virtuales emparejadas. Las máquinas virtuales tendrán nombres DNS internos que se puedan resolver dentro de la red virtual local. Sin embargo, los usuarios pueden configurar máquinas virtuales que se ejecuten en redes virtuales emparejadas como servidores DNS para una red virtual.

## Encadenamiento de servicio
Los usuarios pueden configurar tablas de rutas definidas por el usuario que apunten a máquinas virtuales en VNets emparejadas como próximo salto (como se muestra en el diagrama siguiente). Esto permite a los usuarios a lograr encadenamiento de servicio por el que se puede dirigir el tráfico de una red virtual a un dispositivo virtual que se ejecute en una red virtual emparejada a través de tablas de rutas definidas por el usuario. Los usuarios pueden crear también eficazmente entornos de tipo radio y concentrador donde este último hospede el componente de infraestructura, como un dispositivo de red virtual y todas las redes virtuales del radio se puedan emparejar a él y dirigir un subconjunto de tráfico a los dispositivos que se ejecuten en la red virtual del concentrador. En resumen, el emparejamiento de VNet permite que la dirección IP de próximo salto de la "tabla de ruta definida por el usuario" sea la de una máquina virtual de la red virtual emparejada.

## Puertas de enlace y conectividad local
Todas las redes virtuales, con independencia de si están emparejadas con otra, pueden conservar su propia puerta de enlace y usarla para conectarse localmente. Los usuarios también pueden configurar la conexión de red virtual a red virtual (proporcione el vínculo) con puertas de enlace, aunque las redes virtuales estén emparejadas. Cuando están configuradas ambas opciones de interconectividad de red virtual, el tráfico entre las redes virtuales se propagará a través de la configuración de emparejamiento (es decir, a través de la red troncal de Azure).

Cuando las redes virtuales están emparejadas, los usuarios también pueden configurar para utilizar la puerta de enlace de la red virtual emparejada como punto de tránsito a local. En este caso, la red virtual que usa una puerta de enlace remota no puede tener una puerta de enlace propia, en resumen, una red virtual puede tener solo una puerta de enlace, ya sea local o remota (en la red virtual emparejada), como se muestra en la siguiente imagen. El tránsito de puerta de enlace no es compatible entre la red virtual de Resource Manager y la clásica, en la relación de emparejamiento ambas redes virtuales deben ser de Resource Manager para que el tránsito de puerta de enlace funcione. Cuando las redes virtuales que comparten un único circuito ER están emparejadas, el tráfico pasa entre ellas a través de la relación de emparejamiento (es decir, a través de la red troncal de Azure). Los usuarios pueden seguir utilizando puertas de enlace locales en las redes virtuales para conectarse al circuito local o usar una puerta de enlace compartida y configurar el tránsito para la conectividad local.

![Tránsito del emparejamiento de VNet](./media/virtual-networks-peering-overview/figure02.png)

## Aprovisionamiento
El emparejamiento de VNet es una operación con privilegios. Es una función independiente en el espacio de nombres de red virtual. Los usuarios pueden tener derechos específicos para autorizar el emparejamiento. Un usuario que tenga acceso de lectura y escritura en la red virtual los heredará automáticamente. Los usuarios que ya sean administradores o tengan privilegios para emparejar pueden iniciar operaciones de emparejamiento a otra red virtual. Si hay una solicitud coincidente para el emparejamiento en el otro lado y se cumplen otros requisitos, se establece el emparejamiento.

Consulte los artículos de procedimientos para más información acerca de cómo realizar el emparejamiento de VNet entre dos redes virtuales.

## Límites
El número de emparejamientos permitidos para una red virtual está limitado; consulte [Límites de redes de Azure](../azure-subscription-service-limits.md#networking-limits) para más información.

## Precios
El emparejamiento de VNet no se cargará para durante el período de prueba. Una vez que se lance para la disponibilidad general habrá un cargo nominal en el tráfico de entrada y salida que utiliza el emparejamiento. Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-network).


## Pasos siguientes
- [Configuración del emparejamiento entre redes virtuales](virtual-networks-create-vnetpeering-arm-portal.md).
- Más información sobre [NSG](virtual-networks-nsg.md).
- Obtención de más información sobre [rutas definidas por el usuario y reenvío IP](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0803_2016-->