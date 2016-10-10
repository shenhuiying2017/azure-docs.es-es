<properties
    pageTitle="Información general de IPv6 para Azure Load Balancer | Microsoft Azure"
    description="Información sobre la compatibilidad de IPv6 con Azure Load Balancer y máquinas virtuales de carga equilibrada."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# Información general de IPv6 para Azure Load Balancer

Los equilibradores de carga con conexión a Internet pueden implementarse con una dirección IPv6. Además de la conectividad de IPv4, esto permite las siguientes capacidades:

* Conectividad IPv6 nativa de un extremo a otro entre los clientes de Internet públicos y Azure Virtual Machines a través del equilibrador de carga.
* Conectividad IPv6 nativa saliente de un extremo a otro entre máquinas virtuales y los clientes de Internet públicos con IPv6 habilitado.

En la siguiente imagen se muestra la funcionalidad de IPv6 para Azure Load Balancer.

![Azure Load Balancer con IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Una vez implementado, un cliente de Internet con IPv4 o IPv6 habilitado puede comunicarse con direcciones IPv4 o IPv6 públicas (o nombres de host) de Azure Load Balancer con conexión a Internet. El equilibrador de carga redirige los paquetes IPv6 a las direcciones IPv6 privadas de las máquinas virtuales mediante la traducción de direcciones de red (NAT). El cliente de Internet IPv6 no se puede comunicar directamente con la dirección IPv6 de las máquinas virtuales.

## Características

La compatibilidad nativa de IPv6 para máquinas virtuales implementadas a través de Azure Resource Manager ofrece:

1. Servicios IPv6 de carga equilibrada para clientes IPv6 en Internet
2. Puntos de conexión de IPv4 y IPv6 nativas en máquinas virtuales ("dual apilada")
3. Conexiones de IPv6 nativas iniciadas de entrada y salida
4. Protocolos compatibles como TCP, UDP y HTTP (S) admiten una amplia gama de arquitecturas de servicio

## Ventajas

Esta funcionalidad ofrece los siguientes beneficios clave:

* Cumplir las normas gubernamentales que exigen que las nuevas aplicaciones sean accesibles solo a los clientes IPv6
* Habilitar desarrolladores móviles y de Internet de las cosas para usar Azure Virtual Machines con doble pila (IPv4 + IPv6) para satisfacer el crecimiento de los mercados móviles y de IOT

## Detalles y limitaciones

Detalles

* El servicio DNS de Azure contiene registros de nombre IPv4 A y IPv6 AAAA y responde con ambos registros para el equilibrador de carga. El cliente elige con qué dirección (IPv4 o IPv6) comunicarse.
* Cuando una máquina virtual inicia una conexión a un dispositivo conectado a Internet a través de la dirección IPv6 pública, la dirección IPv6 de origen de la máquina virtual es la dirección de red traducida (NAT) a la dirección IPv6 pública del equilibrador de carga.
* Las máquinas virtuales que ejecutan el sistema operativo Linux deben configurarse para recibir una dirección IP IPv6 a través de DHCP. Muchas de las imágenes de Linux de la Galería de Azure ya están configuradas para admitir IPv6 sin modificación. Para más información, vea [Configuring DHCPv6 for Linux VMs](load-balancer-ipv6-for-linux.md) (Configuración de DHCPv6 para máquinas virtuales de Linux).
* Si decide utilizar un sondeo de estado con el equilibrador de carga, cree un sondeo de IPv4 y utilícelo con los puntos de conexión de IPv4 y IPv6. Si el servicio deja de funcionar en la máquina virtual, los puntos de conexión de IPv4 y IPv6 se establecen fuera de la rotación.

Limitaciones

* No se pueden agregar reglas de equilibrio de carga de IPv6 a Azure Portal. Las reglas solo pueden crearse a través de la plantilla, la CLI o PowerShell.
* No puede actualizar las máquinas virtuales existentes para utilizar direcciones IPv6. Debe implementar nuevas máquinas virtuales.
* Una única dirección IPv6 se puede asignar a una sola interfaz de red en cada máquina virtual.
* Las direcciones IPv6 públicas no pueden asignarse a una máquina virtual. Solo pueden asignarse a un equilibrador de carga.
* Las máquinas virtuales con las direcciones IPv6 no pueden ser miembros de un servicio en la nube de Azure. Se pueden conectar a una red Azure Virtual Network (VNet) y comunicarse entre sí a través de sus direcciones IPv4.
* Las direcciones IPv6 privadas se pueden implementar en máquinas virtuales individuales en un grupo de recursos, pero no se pueden implementar en un grupo de recursos a través de conjuntos de escalas.
* No se pueden conectar máquinas virtuales de Azure Virtual Machines a través de IPv6 a otras máquinas virtuales, otros servicios de Azure o los dispositivos locales. Solo pueden comunicarse con Azure Load Balancer a través de IPv6. Sin embargo, pueden comunicarse con estos otros recursos mediante IPv4.
* La protección del grupo de seguridad de red (NSG) para IPv4 es compatible con las implementaciones de pila doble (IPv4 + IPv6). Los NSG no se aplican a los puntos de conexión de IPv6.
* El punto de conexión de IPv6 en la máquina virtual no se conecta directamente a Internet. Lo hace a través de un equilibrador de carga. Solo los puertos especificados en las reglas de equilibrador de carga son accesibles a través de IPv6.
* El cambio del parámetro IdleTimeout a IPv6 **no se admite actualmente**. El valor predeterminado es de cuatro minutos.

## Pasos siguientes

Obtenga información sobre cómo implementar un equilibrador de carga con IPv6.

* [Implementación de un equilibrador de carga con IPv6 mediante una plantilla](load-balancer-ipv6-internet-template.md)
* [Implementación de un equilibrador de carga con IPv6 mediante Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Implementación de un equilibrador de carga con IPv6 mediante la CLI de Azure](load-balancer-ipv6-internet-cli.md)

<!---HONumber=AcomDC_0928_2016-->