---
title: Azure Virtual Network | Microsoft Docs
description: "Más información sobre las características y conceptos de Azure Virtual Network."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jdial
ms.openlocfilehash: 6cc7035e798ef72f69958a7536a741f80939d4fe
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-virtual-network"></a>Azure Virtual Network

El servicio Microsoft Azure Virtual Network permite que los recursos de Azure se comuniquen de manera segura con otros en una red virtual. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Puede conectar redes virtuales a otras redes virtuales o a su red local. La imagen siguiente muestra algunas de las funcionalidades del servicio Azure Virtual Network:

![Diagrama de red](./media/virtual-networks-overview/virtual-network-overview.png)

Para más información acerca de las siguientes funcionalidades de Azure Virtual Network, haga clic en la funcionalidad:
- **[Aislamiento:](#isolation)** las redes virtuales están aisladas entre sí. Puede crear redes virtuales independientes para el desarrollo, la prueba y la producción que usan los mismos bloques de direcciones de CIDR (10.0.0.0/0, por ejemplo). Por el contrario, puede crear varias redes virtuales que usan diferentes bloques de direcciones CIDR y que conectan las redes entre sí. También puede segmentar una red virtual en varias subredes. Azure proporciona resolución de nombres interna para recursos implementados en una red virtual. Si es necesario, puede configurar una red virtual para usar sus propios servidores DNS, en lugar de usar la resolución de nombres interna de Azure.
- **[Comunicación con Internet:](#internet)** los recursos, tales como las máquinas virtuales implementadas en una red virtual, tienen acceso a Internet de manera predeterminada. También puede habilitar el acceso entrante a recursos específicos, según sea necesario.
- **[Comunicación entre recursos de Azure:](#within-vnet)** los recursos de Azure implementados en una red virtual pueden comunicarse entre sí mediante direcciones IP privadas, aunque los recursos estén implementados en subredes diferentes. Azure proporciona el enrutamiento predeterminado entre subredes, redes virtuales conectadas y redes locales, por lo que no tendrá que configurar ni administrar rutas. Si lo desea, puede personalizar el enrutamiento de Azure.
- **[Conectividad de redes virtuales:](#connect-vnets)** las redes virtuales se pueden conectar entre sí, lo que permite que los recursos de cualquier red virtual se comuniquen con los recursos de cualquier otra red virtual.
- **[Conectividad local:](#connect-on-premises)** una red virtual puede estar conectada a una red local, lo que permite que los recursos se comuniquen entre sí.
- **[Filtrado de tráfico:](#filtering)** puede filtrar el tráfico de red hacia y desde los recursos de una red virtual mediante el puerto y la dirección IP de origen, el puerto y la dirección IP de destino, y el protocolo.
- **[Enrutamiento:](#routing)** de manera opcional, puede invalidar el enrutamiento predeterminado de Azure mediante la configuración de sus propias rutas, o bien mediante la propagación de rutas BGP a través de una puerta de enlace de red.

## <a name = "isolation"></a>Aislamiento de red y segmentación

Puede implementar varias redes virtuales dentro de cada [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [región](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) de Azure. Cada red virtual está aislada de las otras redes virtuales. Para cada red virtual puede:
- Especificar un espacio de direcciones IP privado personalizado mediante direcciones públicas y privadas (RFC 1918). Azure asigna los recursos de una red virtual a una dirección IP privada desde el espacio de direcciones que asigne.
- Segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de la red virtual para cada subred.
- Usar la resolución de nombres que proporciona Azure o especificar su propio servidor DNS para que lo usen los recursos conectados a una red virtual. Para más información sobre la resolución de nombres de las redes virtuales, consulte [Resolución de nombres para las máquinas virtuales e instancias de rol](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Comunicación con Internet
Todos los recursos de una red virtual pueden establecer la comunicación saliente con Internet. De manera predeterminada, la dirección IP privada del recurso es la dirección de red de origen traducida (SNAT) a una dirección IP pública seleccionada por la infraestructura de Azure. Para más información acerca de la conectividad de Internet saliente, consulte el artículo de [Comprender las conexiones salientes en Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). Para evitar la conectividad saliente hacia Internet, puede implementar el filtrado de tráfico o rutas personalizadas.

Para la comunicación entrante con los recursos de Azure desde Internet, o para la comunicación saliente a Internet sin SNAT, se debe asignar un recurso una dirección IP pública. Para más información sobre las direcciones IP públicas, lea el artículo [Direcciones IP publicas](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Comunicación segura entre recursos de Azure

Puede implementar las máquinas virtuales dentro de una red virtual. Las máquinas virtuales se comunican con otros recursos de una red virtual a través de una interfaz de red. Para más información sobre las interfaces de red, consulte [Interfaces de red](virtual-network-network-interface.md).

También puede implementar otros tipos de recursos de Azure en una red virtual, como Azure App Service Environment y Azure Virtual Machine Scale Sets. Para una lista completa de los recursos de Azure que puede implementar en una red virtual, consulte el artículo sobre la [integración de servicios de redes virtuales para los servicios de Azure](virtual-network-for-azure-services.md).

Algunos recursos no se pueden implementar en una red virtual, pero le permiten restringir la comunicación con los recursos para que solo se realice dentro de una red virtual. Para más información sobre cómo restringir el acceso a los recursos, consulte [Puntos de conexión del servicio de redes virtuales](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Conexión de redes virtuales

Puede conectar las redes virtuales entre sí, lo que permite que los recursos de cualquier red virtual se comuniquen entre sí mediante el emparejamiento de las redes virtuales. El ancho de banda y la latencia de la comunicación entre los recursos de distintas redes virtuales es el mismo que si los recursos estuviesen en la misma red virtual. Para aprender más sobre emparejamiento, lea el artículo [Emparejamiento de redes virtuales de Azure](virtual-network-peering-overview.md).

## <a name="connect-on-premises"></a>Conexión a una red local

Puede conectar su red local a una red virtual mediante cualquier combinación de las siguientes opciones:
- **Red privada virtual (VPN) de punto a sitio:** se establece entre una red virtual y un equipo único en la red. Cada equipo que quiera establecer conectividad con una red virtual debe configurar su conexión de manera independiente. Este tipo de conexión es muy útil cuando se está comenzando con Azure, o para los desarrolladores, porque requiere poco o ningún cambio en la red existente. La conexión usa el protocolo SSTP para proporciona comunicación cifrada a través de Internet entre el equipo y la red virtual. La latencia de una VPN de sitio a punto es imprevisible, ya que el tráfico atraviesa Internet.
- **VPN de sitio a sitio:** se establece entre el dispositivo VPN y una instancia de Azure VPN Gateway en una red virtual. Este tipo de conexión permite que cualquier recurso local que autoriza a acceder a una red virtual. La conexión es una VPN de IPSec/IKE que proporciona comunicación cifrada por Internet entre el dispositivo local y la instancia de Azure VPN Gateway. La latencia de una conexión de sitio a sitio es imprevisible, ya que el tráfico atraviesa Internet.
- **Azure ExpressRoute:** establecida entre la red y Azure, a través de un asociado de ExpressRoute. Esta conexión es privada. El tráfico no atraviesa Internet. La latencia de una conexión ExpressRoute es predecible, ya que el tráfico no atraviesa Internet.

Para más información acerca de todas las opciones de conexión anteriores, consulte [Diagramas de topología de conexión](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrado del tráfico de red
Puede filtrar el tráfico de red entre subredes mediante una o ambas de las siguientes opciones:
- **Grupos de seguridad de red:** un grupo de seguridad de red puede contener varias reglas de seguridad de entrada y salida, que le permiten filtrar el tráfico por dirección IP, puerto y protocolo de origen y destino. Puede aplicar un grupo de seguridad de red a cada interfaz de red de una máquina virtual. También puede aplicar un grupo de seguridad de red a la subred, u otro recurso de Azure, en que se encuentra una interfaz de red. Para obtener más información sobre los grupos de seguridad de red, consulte [Grupos de seguridad de red](security-overview.md#network-security-groups).
- **Aplicaciones virtuales de red:** una aplicación de red virtual es una máquina virtual que ejecuta software que realiza una función de red, como un firewall. Consulte una lista de las aplicaciones virtuales de red disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). También hay disponibles aplicaciones virtuales de red que proporcionan la optimización de la WAN y otras funciones de tráfico de red. Los dispositivos virtuales de red habitualmente se usan con rutas BGP o definidas por el usuario. También puede usar una aplicación virtual de red para filtrar el tráfico entre las redes virtuales.

## <a name="routing"></a>Redirección del tráfico de red

Azure crea tablas de rutas que permiten que los recursos conectados a una subred de cualquier red virtual se comuniquen entre sí y con Internet, de manera predeterminada. Puede implementar una o ambas de las siguientes opciones para reemplazar las rutas predeterminadas que crea Azure:
- **Rutas definidas por el usuario:** puede crear tablas de rutas personalizadas con las rutas que controlan a dónde se enruta el tráfico para cada subred. Para más información sobre las rutas definidas por el usuario, consulte [Rutas definidas por el usuario](virtual-networks-udr-overview.md#user-defined).
- **Rutas BGP:** si conecta la red virtual a su red local mediante una conexión de Azure VPN Gateway o ExpressRoute, puede propagar las rutas BGP a sus redes virtuales.

## <a name="pricing"></a>Precios

No hay ningún cargo por redes virtuales, subredes, tablas de rutas o grupos de seguridad de red. El uso del ancho de banda saliente de Internet, las direcciones IP públicas, el emparejamiento de redes virtuales, puertas de enlace VPN y ExpressRoute tienen sus propias estructuras de precios. Consulte las páginas de precios de [red virtual](https://azure.microsoft.com/pricing/details/virtual-network), [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway) y [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) para más información.

## <a name="faq"></a>Preguntas más frecuentes

Para revisar las preguntas más frecuentes sobre Azure Virtual Network, consulte el artículo de [preguntas más frecuentes de Virtual Network](virtual-networks-faq.md).

## <a name="next-steps"></a>Pasos siguientes

- Cree su primera red virtual e implemente en ella algunas máquinas virtuales. Para ello, complete los pasos que aparecen en[Creación de su primera red virtual](virtual-network-get-started-vnet-subnet.md).
- Cree una conexión de punto a sitio a una red virtual con los pasos descritos en [Configuración de una conexión de punto a sitio](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Obtenga más información sobre las demás [funcionalidades de red](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) clave de Azure.
