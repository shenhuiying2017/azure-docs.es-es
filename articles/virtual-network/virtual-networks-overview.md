---
title: Azure Virtual Network | Microsoft Docs
description: "Más información sobre las características y conceptos de Azure Virtual Network."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-virtual-network"></a>Red virtual

El servicio de Azure Virtual Network le permite conectar de forma segura los recursos de Azure con redes virtuales. Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción. También puede conectar redes virtuales a la red local. La imagen siguiente muestra algunas de las funcionalidades del servicio Azure Virtual Network:

![Diagrama de red](./media/virtual-networks-overview/virtual-network-overview.png)

Para más información acerca de las siguientes funcionalidades de Azure Virtual Network, haga clic en la funcionalidad:
- **[Aislamiento:](#isolation)** las redes virtuales están completamente aisladas entre sí. Puede crear redes virtuales independientes para el desarrollo, la prueba y la producción que usan los mismos bloques de direcciones de CIDR. Por el contrario, puede crear varias redes virtuales que usan diferentes bloques de direcciones CIDR y que conectan redes entre sí. También puede segmentar una red virtual en varias subredes. Azure proporciona resolución de nombres interna para máquinas virtuales e instancias de rol de Cloud Services conectadas a una red virtual. Si lo desea, puede configurar una red virtual para usar sus propios servidores DNS, en lugar de utilizar la resolución de nombres interna Azure.
- **[Conectividad a Internet:](#internet)**  todas las máquinas virtuales de Azure y las instancias de rol de Cloud Services conectadas a una red virtual tienen acceso a Internet, de forma predeterminada. También puede habilitar el acceso entrante a recursos específicos, según sea necesario.
- **[Conectividad de los recursos de Azure:](#within-vnet)**  los recursos de Azure como Cloud Services y máquinas virtuales se pueden conectar a la misma red virtual. Los recursos pueden conectarse entre sí mediante direcciones IP privadas, aunque estén en subredes diferentes. Azure proporciona el enrutamiento predeterminado entre subredes, redes virtuales y redes locales, por lo que no tendrá que configurar ni administrar rutas.
- **[Conectividad de red virtual:](#connect-vnets)** las redes virtuales se pueden conectar entre sí, habilitando los recursos conectados a cualquier red virtual para que se comuniquen con cualquier recurso de cualquier red virtual.
- **[Conectividad local:](#connect-on-premises)** las redes virtuales se pueden conectar a redes locales a través de conexiones de redes privadas entre la red y Azure, o a través de una conexión VPN de sitio a sitio a través de Internet.
- **[Filtrado de tráfico:](#filtering)** el tráfico de red de entrada y salida de las instancias de rol de Cloud Services y máquinas virtuales se puede filtrar por dirección IP y puerto de origen, dirección IP y puerto de destino, y por protocolo.
- **[Enrutamiento:](#routing)** opcionalmente, puede invalidar el enrutamiento predeterminado de Azure mediante la configuración de sus propias rutas, o mediante el uso de rutas BGP a través de una puerta de enlace de red.

## <a name = "isolation"></a>Aislamiento de red y segmentación

Puede implementar varias redes virtuales dentro de cada [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [región](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) de Azure. Cada red virtual está aislada de otras redes virtuales. Para cada red virtual, puede:
- Especificar un espacio de direcciones IP privado personalizado mediante direcciones públicas y privadas (RFC 1918). Azure asigna recursos conectados a la red virtual a una dirección IP privada desde el espacio de direcciones que asigne.
- Segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de redes virtuales para cada subred.
- Utilizar la resolución de nombres que proporciona Azure o especificar su propio servidor DNS para su uso por recursos conectados a una red virtual. Para más información sobre la resolución de nombres de las redes virtuales, lea el artículo [Resolución de nombres para las máquinas virtuales y servicios en la nube](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Conexión a Internet
Todos los recursos conectados a una red virtual tienen conectividad saliente a Internet de forma predeterminada. La dirección IP privada del recurso es la dirección de red de origen traducida (SNAT) a una dirección IP pública por la infraestructura de Azure. Para más información acerca de la conectividad de Internet saliente, consulte el artículo de [Comprender las conexiones salientes en Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). Puede cambiar la conectividad predeterminada mediante la implementación del enrutamiento personalizado y del filtrado de tráfico.

Para la comunicación entrante con los recursos de Azure desde Internet, o para la comunicación saliente a Internet sin SNAT, se debe asignar un recurso una dirección IP pública. Para más información sobre las direcciones IP públicas, lea el artículo [Direcciones IP publicas](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Conexión de recursos de Azure
Se pueden conectar varios recursos de Azure a una red virtual, como máquinas virtuales (VM), Cloud Services, entornos de App Service y conjuntos de escalado de máquinas virtuales. Las máquinas virtuales se conectan a una subred dentro de una red virtual a través de una interfaz de red (NIC). Para más información sobre las NIC, consulte el artículo [Interfaces de red](virtual-network-network-interface.md).

## <a name="connect-vnets"></a>Conexión de redes virtuales

Puede conectar redes virtuales entre sí, habilitar recursos conectados a cualquier red virtual para que se comuniquen entre sí a través de redes virtuales. Puede utilizar una o ambas de las siguientes opciones para conectar redes virtuales entre sí:
- **Emparejamiento:** permite que los recursos se conecten a distintas redes virtuales de Azure en la misma ubicación de Azure para comunicarse entre sí. El ancho de banda y la latencia en las redes virtuales son los mismos que si los recursos estuvieran conectados a la misma red virtual. Para aprender más sobre emparejamiento, lea el artículo [Emparejamiento de redes virtuales de Azure](virtual-network-peering-overview.md).
- **Conexión de red virtual a red virtual:** permite recursos conectados a la red virtual de Azure diferentes dentro de las mismas ubicaciones de Azure, o diferentes. A diferencia del emparejamiento, el ancho de banda es limitado entre las redes virtuales porque el tráfico debe pasar a través de una instancia de Azure VPN Gateway. Para más información sobre cómo conectar redes virtuales con una conexión de red virtual a red virtual, consulte el artículo [Configuración de una conexión de red virtual a red virtual](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="connect-on-premises"></a>Conexión a una red local

Puede conectar su red local a una red virtual mediante cualquier combinación de las siguientes opciones:
- **Red privada virtual (VPN) de punto a sitio:** establecido entre un único equipo conectado a la red y a la red virtual. Este tipo de conexión es muy útil cuando se está comenzando con Azure, o para los desarrolladores, porque requiere poco o ningún cambio en la red existente. La conexión utiliza el protocolo SSTP para proporcionar comunicación cifrada a través de Internet entre el equipo y la red virtual. La latencia de una VPN de sitio a punto es imprevisible, ya que el tráfico atraviesa Internet.
- **VPN de sitio a sitio:** se establece entre el dispositivo VPN y una instancia de Azure VPN Gateway. Este tipo de conexión permite cualquier recurso local que autoriza a acceder a una red virtual. La conexión es una VPN de IPSec/IKE que proporciona comunicación cifrada por Internet entre el dispositivo local y la instancia de Azure VPN Gateway. La latencia de una conexión de sitio a sitio es imprevisible, ya que el tráfico atraviesa Internet.
- **Azure ExpressRoute:** establecida entre la red y Azure, a través de un asociado de ExpressRoute. Esta conexión es privada. El tráfico no atraviesa Internet. La latencia de una conexión ExpressRoute es predecible, ya que el tráfico no atraviesa Internet.

Para más información acerca de todas las opciones de la conexión anterior, consulte el artículo [Diagramas de topología de conexión](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrado del tráfico de red
Puede filtrar el tráfico de red entre subredes mediante una o ambas de las siguientes opciones:
- **Grupos de seguridad de red:** cada grupo de seguridad de red puede contener varias reglas de seguridad entrante y saliente, que le permiten filtrar el tráfico por dirección IP, puerto y protocolo de origen y destino. Puede aplicar un grupo de seguridad de red a cada NIC de una máquina virtual. También puede aplicar un grupo de seguridad de red a la subred de una NIC u otro recurso de Azure, a la que está conectado. Consulte el artículo [Grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre los NSG.
- **Dispositivos virtuales de red (NVA):** un dispositivo virtual de red es una máquina virtual que ejecuta software que realiza una función de red, como un firewall. Consulte una lista de dispositivos virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Los dispositivos virtuales de red también están disponibles para proporcionar la optimización de la WAN y otras funciones de tráfico de red. Los dispositivos virtuales de red normalmente se usan con rutas BGP o definidas por el usuario. También puede utilizar un dispositivo virtual de red para filtrar el tráfico entre redes virtuales.

## <a name="routing"></a>Redirección del tráfico de red

Azure crea tablas de rutas que permiten a los recursos conectados a una subred de cualquier red virtual se comuniquen entre sí, de forma predeterminada. Puede implementar una o ambas de las siguientes opciones para reemplazar las rutas predeterminadas que crea Azure:
- **Rutas definidas por el usuario:** puede crear tablas de rutas personalizadas con las rutas que controlan a dónde se enruta el tráfico para cada subred. Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).
- **Rutas BGP:** si conecta la red local a su red local mediante una conexión de Azure VPN Gateway o ExpressRoute, puede propagar las rutas BGP a sus redes virtuales.

## <a name="pricing"></a>Precios

No hay ningún cargo por redes virtuales, subredes, tablas de rutas o grupos de seguridad de red. El uso del ancho de banda saliente de Internet, las direcciones IP públicas, el emparejamiento de redes virtuales, puertas de enlace VPN y ExpressRoute tienen sus propias estructuras de precios. Consulte las páginas de precios de [red virtual](https://azure.microsoft.com/pricing/details/virtual-network), [puerta de enlace VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) y [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) para más información.

## <a name="faq"></a>P+F

Para revisar las preguntas más frecuentes sobre la red Virtual, vea el artículo [P+F de Virtual Network](virtual-networks-faq.md).


## <a name="next-steps"></a>Pasos siguientes

- Cree su primera red virtual y conecte algunas máquinas virtuales a ella, siguiendo los pasos descritos en el artículo [Creación de su primera red virtual](virtual-network-get-started-vnet-subnet.md).
- Cree una conexión de punto a sitio a una red virtual siguiendo los pasos descritos en el artículo [Configuración de una conexión de punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Obtenga más información sobre las demás [funcionalidades de red](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) clave de Azure.
