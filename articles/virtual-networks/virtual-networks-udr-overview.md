<properties 
   pageTitle="Información general de las rutas definidas por el usuario y reenvío IP"
   description="Información general de las rutas definidas por el usuario (UDR) y reenvío IP"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Rutas definidas por el usuario y reenvío IP
Azure utiliza una tabla de enrutamiento para decidir cómo reenviar el tráfico IP basándose en el destino de cada paquete. Aunque Azure proporciona una tabla de enrutamiento predeterminada en función de la configuración de red virtual, es posible que tenga que agregar rutas personalizadas a esa tabla. La necesidad más común de una entrada personalizada en la tabla de enrutamiento es el uso de un dispositivo virtual en el entorno de Azure. Tenga en cuenta el escenario mostrado en la ilustración siguiente. Suponga que desea asegurarse de que todo el tráfico dirigido a la capa intermedia y a las subredes con copia de seguridad que se inicia desde la subred front-end a través de un dispositivo virtual de firewall. Si únicamente se agrega el dispositivo a la red virtual y se conecta a las diferentes subredes no proporcionará esta funcionalidad. También debe cambiar la tabla de enrutamiento que se aplica a la subred para asegurarse de que los paquetes se reenvían al dispositivo virtual del firewall.

Se aplica lo mismo si implementa un dispositivo NAT virtual para controlar el tráfico entre Internet y de la red virtual de Azure. Para asegurarse de que se usa el dispositivo virtual, debe crear una ruta que especifique que todo el tráfico destinado a Internet debe reenviarse al dispositivo virtual.

## Enrutamiento
Los paquetes se enrutan sobre una red TCP/IP basada en una tabla de enrutamiento definida en cada nodo de la red física. Una tabla de enrutamiento es una colección de rutas individuales que se utiliza para decidir dónde reenviar los paquetes según la dirección IP de destino. Una ruta consta de lo siguiente:

- **Prefijo de dirección** El CIDR de destino al que se aplica la ruta, por ejemplo, 10.1.0.0/16.
- **Tipo de próximo salto**. El tipo de salto de Azure al que debe enviarse el paquete. Los valores posibles son:
	- **Local**. Representa la red virtual local. Por ejemplo, si tiene dos subredes, 10.1.0.0/16 y 10.2.0.0/16 en la misma red virtual, la ruta de cada subred de la tabla de enrutamiento tendrá un valor de próximo salto de *Local*.
	- **Puerta de enlace de VPN**. Representa una puerta de enlace de VPN S2S de Azure. 
	- **Internet**. Representa la puerta de enlace de Internet predeterminada proporcionada por la infraestructura de Azure. 
	- **Dispositivo virtual**. Representa un dispositivo virtual agregado a la red virtual de Azure.
	- **NULL**. Representa un agujero negro. Los paquetes enviados a un agujero negro no se reenviarán de ninguna manera.
- **Valor del próximo salto**. El valor del próximo salto contiene los paquetes de la dirección IP a la que se deben reenviar. Solo se permiten valores de próximo salto en las rutas donde el tipo de próximo salto es *Dispositivo virtual*.

## Rutas predeterminadas
Cada subred creada en una red virtual se asocia automáticamente a una tabla de enrutamiento que contiene las siguientes reglas de enrutamiento predeterminadas: 
- **Regla de red virtual local**: esta regla se crea automáticamente para cada subred de una red virtual. Especifica que hay un vínculo directo entre las máquinas virtuales de la red virtual y que no hay ningún próximo salto intermedio. 
- **Regla local**: esta regla se aplica a todo el tráfico destinado al intervalo de direcciones local y utiliza la puerta de enlace de VPN como el próximo salto de destino. 
- **Regla de Internet**: esta regla controla todo el tráfico destinado a la red Internet pública y utiliza la puerta de enlace de Internet de infraestructura como el próximo salto para todo el tráfico destinado a Internet.

## Rutas BGP
Si tiene una conexión de ExpressRoute entre la red local y Azure, puede habilitar BGP propagar las rutas de la red local a Azure. Estas rutas BGP se usan en la misma forma que las rutas predeterminadas y las rutas definidas por el usuario en cada subred de Azure. Para obtener más información, consulte [Introducción a ExpressRoute](../expressroute-introduction).

[AZURE.IMPORTANT] Puede configurar el entorno de Azure para forzar la tunelización a través de la red local mediante la creación de una ruta definida por el usuario para la subred 0.0.0.0/0 que utiliza la puerta de enlace de VPN como el próximo salto. Sin embargo, esto solo funciona si se utiliza una puerta de enlace de VPN, no ExpressRoute. Para ExpressRoute, la tunelización forzada se configura a través de BGP.

## Rutas definidas por el usuario
No puede ver las rutas predeterminadas especificadas anteriormente en el entorno de Azure y, en la mayoría de los entornos, son las únicas rutas que va a necesitar. Sin embargo, puede que necesite crear una tabla de enrutamiento y agregar una o varias rutas en casos concretos, como:

- Tunelización forzada a Internet a través de la red local.
- Uso de dispositivos virtuales en el entorno de Azure.
- 
En los escenarios anteriores, tendrá que crear una tabla de enrutamiento y agregarle las rutas definidas por el usuario. Puede tener varias tablas de enrutamiento y la misma tabla de enrutamiento puede asociarse a una o varias subredes. Y cada subred solo puede estar asociada a una tabla de enrutamiento única. Todas las máquinas virtuales y servicios de nube de una subred utilizan la tabla de enrutamiento asociada a esa subred.

Las subredes dependen de rutas predeterminadas hasta que una tabla de enrutamiento está asociada a la subred. Una vez creada una asociación, el enrutamiento se realiza basándose en más larga del prefijo coincidencia (LPM) entre las rutas definidas por el usuario y las rutas predeterminadas. Si hay más de una ruta con la misma coincidencia LPM, se selecciona una ruta en función de su origen en el orden siguiente:

1. Ruta definida por el usuario
1. Ruta BGP (cuando se utiliza ExpressRoute)
1. Ruta predeterminada

[AZURE.IMPORTANT] Las rutas definidas por el usuario solo se aplican a las máquinas virtuales de Azure y servicios de nube. Por ejemplo, si desea agregar un dispositivo virtual de firewall entre la red local y Azure, debe crear una ruta definida por el usuario para las tablas de enrutamiento de Azure que reenvían todo el tráfico del espacio de direcciones local al dispositivo virtual. Sin embargo, el tráfico entrante desde el espacio de direcciones local se propagará a través de la puerta de enlace de VPN o circuito ExpressRoute directamente en el entorno de Azure, omitiendo el dispositivo virtual.

## Reenvío IP
Como se describió anteriormente, una de las razones principales para crear una ruta definida por el usuario es reenviar el tráfico a un dispositivo virtual. Un dispositivo virtual no es más que una máquina virtual que ejecuta una aplicación utilizada para controlar el tráfico de red de alguna manera, como un firewall o un dispositivo NAT.

La máquina virtual de este dispositivo virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP en la máquina virtual.

## Otras referencias

[Creación de rutas y habilitación del reenvío de IP en Azure](../virtual-networks-udr-how-to)

[Dirección IP pública de nivel de instancia (ILIP)](../virtual-networks-instance-level-public-ip)

[Información general sobre redes virtuales](https://msdn.microsoft.com/library/azure/jj156007.aspx)

<!--HONumber=52-->
 