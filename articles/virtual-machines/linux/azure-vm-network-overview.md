---
title: "Información general sobre las redes de máquina virtual con Linux y Azure| Microsoft Docs"
description: "Información general sobre las redes de máquina virtual con Linux y Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1ff4a0482d6dc6ec0eceaa89ca4b87ba1e2f89a1
ms.lasthandoff: 04/03/2017


---
# <a name="azure-and-linux-vm-network-overview"></a>Información general sobre las redes de máquina virtual con Linux y Azure
## <a name="virtual-networks"></a>Redes virtuales
Una red virtual de Azure (VNet) es una representación de su propia red en la nube. Es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Puede controlar por completo los bloques de direcciones IP, la configuración DNS, las directivas de seguridad y las tablas de rutas dentro de esta red. También puede segmentar aún más la red virtual en subredes e iniciar máquinas virtuales de IaaS de Azure (VM) o servicios en la nube (instancias de rol de PaaS). Además, puede conectar la red virtual a su red local mediante una de las opciones de conectividad disponibles en Azure. En esencia, puede ampliar su red en Azure, con control total sobre bloques de direcciones IP con la ventaja de la escala empresarial que ofrece Azure.

* [Información general sobre redes virtuales](../../virtual-network/virtual-networks-overview.md)

Para crear una red virtual mediante la CLI de Azure, siga estos pasos.

* [Creación de una red virtual mediante la CLI de Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>Grupos de seguridad de red
El grupo de seguridad de red (NSG) contiene una lista de reglas de la lista de control de acceso (ACL) que permiten o deniegan el tráfico de red a sus instancias de VM en una red virtual. Los NSG se pueden asociar con las subredes o las instancias individuales de máquina virtual dentro de esa subred. Cuando un NSG está asociado a una subred, las reglas de ACL se aplican a todas las instancias de VM de esa subred. Además, se puede restringir más el tráfico a una máquina virtual individual asociando un NSG directamente a esa máquina virtual.

* [¿Qué es un grupo de seguridad de red?](../../virtual-network/virtual-networks-nsg.md)
* [Creación de grupos de seguridad de red en la CLI de Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>Rutas definidas por el usuario
Al agregar máquinas virtuales (VM) a una red virtual (VNet) en Azure, observará que las máquinas virtuales son capaces de comunicarse automáticamente con otras máquinas por toda la red. No es necesario que especifique una puerta de enlace, incluso si las máquinas virtuales están en subredes diferentes. Cuando existe una conexión híbrida de Azure a su centro de datos, puede aplicar esto a la comunicación de las máquinas virtuales en la red pública e incluso a su red local.

* [¿Qué son las rutas definidas por el usuario y el reenvío IP?](../../virtual-network/virtual-networks-udr-overview.md)
* [Creación de un UDR en la CLI de Azure](../../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>Asociación de un FQDN a la máquina virtual con Linux
Cuando crea una máquina virtual en el Azure Portal mediante el modelo de implementación de Resource Manager, se crea automáticamente un recurso de IP pública para la máquina virtual. Use esta dirección IP para acceder de forma remota a la máquina virtual. Aunque el portal no crea un nombre de dominio completo, o FQDN, de forma predeterminada, puede agregar uno cuando se crea la máquina virtual.

* [Creación de un nombre de dominio completo en Azure Portal](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="network-interfaces"></a>Interfaces de red
Una interfaz de red (NIC) es la interconexión entre una máquina virtual (VM) y la red de software subyacente. Este artículo explica qué es una interfaz de red y cómo se utiliza en el modelo de implementación de Azure Resource Manager.

* [Interfaces de red virtual](../../virtual-network/virtual-network-network-interface.md)

## <a name="virtual-nics-and-dns-labeling"></a>NIC virtuales y etiquetado de DNS
Si tiene un servidor que debe ser persistente, pero ese servidor se trata sin la atención debida y se cierra y se implementa con frecuencia, tendrá que usar el etiquetado DNS en la NIC para conservar la persistencia del nombre en la red virtual.  Con el siguiente tutorial configurará una NIC de nombre persistente con una IP estática.

* [Creación de un entorno de Linux completo mediante la CLI de Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>Puertas de enlace de red virtual
Se usa una puerta de enlace de red virtual para enviar tráfico de red entre redes virtuales de Azure y ubicaciones locales, así como entre redes virtuales dentro de Azure (de red virtual a red virtual). Cuando se configura una puerta de enlace de VPN, debe crear y configurar una puerta de enlace de red virtual y una conexión de puerta de enlace de red virtual.

* [Acerca de VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>Equilibrio de carga interno
Azure Load Balancer es un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye proporcionando una alta disponibilidad el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales de un conjunto de carga equilibrada. Azure Load Balancer también pueden presentar prestar servicios en varios puertos, varias direcciones IP o ambos.

* [Creación de un equilibrador de carga interno mediante la CLI de Azure](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)


