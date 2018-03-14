---
title: Preguntas frecuentes acerca de Azure Virtual Network | Microsoft Docs
description: "Las respuestas para las preguntas más frecuentes acerca de las redes virtuales de Microsoft Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.openlocfilehash: 8800dc59306c349daba8f4d9703e0c713eed06ec
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F) acerca de Azure Virtual Network

## <a name="virtual-network-basics"></a>Conceptos básicos de Virtual Network

### <a name="what-is-an-azure-virtual-network-vnet"></a>¿Qué es Azure Virtual Network?
Azure Virtual Network es una representación de su propia red en la nube. Es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Puede usar las redes virtuales para aprovisionar y administrar redes privadas virtuales (VPN) en Azure y, opcionalmente, vincular las redes virtuales con otras redes virtuales en Azure o con sus infraestructura de TI local para crear soluciones híbridas o entre entornos. Cada red virtual que se crea tiene su propio bloque CIDR y se puede vincular a otras redes locales y redes virtuales, siempre que los bloques CIDR no se superpongan. También tiene el control de la configuración del servidor DNS para redes virtuales y de la segmentación de la red virtual en subredes.

Use las redes virtuales para:

* Crear una red virtual solo en una nube privada dedicada. A veces no se requiere una configuración entre entornos para una solución. Cuando se crea una red virtual, los servicios y las máquinas virtuales de la red virtual pueden comunicarse de forma directa y segura entre ellas en la nube. Sin embargo, puede configurar conexiones de punto de conexión para las máquinas virtuales y los servicios que requieren la comunicación a Internet, como parte de la solución.
* Ampliar su centro de datos de forma segura. Con las redes virtuales, puede crear VPN de sitio a sitio (S2S) tradicionales para ampliar la capacidad de cualquier centro de datos de forma segura. Las redes virtuales de sitio a sitio (S2S) usan IPSEC para proporcionar una conexión segura entre la puerta de enlace VPN corporativa y Azure.
* Habilitar escenarios en la nube híbrida. Las redes virtuales proporcionan la flexibilidad necesaria para admitir una variedad de escenarios en la nube híbrida. Puede conectar de forma segura aplicaciones basadas en la nube a cualquier tipo de sistema local como grandes sistemas y sistemas Unix.

### <a name="how-do-i-get-started"></a>¿Cómo empiezo?
Para comenzar, consulte [Documentación de Virtual Network](https://docs.microsoft.com/azure/virtual-network/). En este artículo encontrará información general y de implementación de todas las características de una red virtual.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>¿Puedo usar redes virtuales sin conectividad entre entornos?
Sí. Puede usar una red virtual sin necesidad de conectarse a su entorno local. Por ejemplo, podría ejecutar controladores de dominio de Active Directory para Microsoft Windows Server y granjas de servidores de SharePoint únicamente en una red virtual de Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>¿Se puede realizar la optimización de una WAN entre redes virtuales o entre una red virtual y un centro de datos local?

Sí. A través de Azure Marketplace es posible implementar una [aplicación virtual de red de optimización de la WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) de varios proveedores.

## <a name="configuration"></a>Configuración

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>¿Qué herramientas debo usar para crear una red virtual?
Para crear o configurar una red virtual se pueden usar las siguientes herramientas:

* Azure Portal
* PowerShell
* Azure CLI
* Un archivo de configuración de red (netcfg; solo para redes virtuales clásicas). Consulte el artículo [Configuración de una red virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>¿Qué intervalos de direcciones puedo usar en mis redes virtuales?
Cualquier intervalo de direcciones IP definido en [RFC 1918](http://tools.ietf.org/html/rfc1918). Por ejemplo, 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>¿Puedo tener direcciones IP públicas en mis redes virtuales?
Sí. Para más información sobre los intervalos de direcciones IP públicas, consulte [Creación de una red virtual](virtual-network-manage-network.md#create-a-virtual-network). Las direcciones IP públicas no son accesibles directamente desde Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>¿Existe algún límite en el número de subredes de que puede tener una red virtual?
Sí. Para más información, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Los espacios de direcciones de las subredes no pueden superponerse entre sí.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?
Sí. Azure reserva algunas direcciones IP dentro de cada subred. Las direcciones IP primera y última de cada subred están reservadas para la conformidad de los protocolos, junto con las direcciones x.x.x.1-x.x.x.3 de cada subred, que se usan para los servicios de Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>¿Qué tamaños mínimo y máximo pueden tener las redes virtuales y las subredes?
La subred más pequeña admitida es /29 y la más grande /8 (mediante definiciones de subred CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>¿Puedo llevar mis VLAN a Azure mediante redes virtuales?
Nº Las redes virtuales son superposiciones de nivel 3. Azure no admite ninguna semántica de nivel 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>¿Puedo especificar directivas de enrutamiento personalizadas en mis redes virtuales y subredes?
Sí. Puede crear una tabla de rutas y asociarla a una subred. Para más información sobre el enrutamiento en Azure, consulte [Introducción al enrutamiento](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>¿Las redes virtuales admiten la multidifusión o la difusión?
Nº No se admite difusión ni multidifusión.

### <a name="what-protocols-can-i-use-within-vnets"></a>¿Qué protocolos puedo usar en las redes virtuales?
En las redes virtuales se pueden usar los protocolos TCP, UDP e ICMP TCP/IP. La unidifusión se admite dentro de las redes virtuales, a excepción del Protocolo de configuración dinámica de host (DCHP) a través de unidifusión (puerto de origen UDP/68 / puerto de destino UDP/67). La multidifusión, la difusión, los paquetes encapsulados IP en IP y los paquetes de encapsulación de enrutamiento genérico (GRE) se bloquean en las subredes. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>¿Puedo hacer ping a mis enrutadores predeterminados dentro de una red virtual?
Nº

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>¿Puedo usar tracert para diagnosticar la conectividad?
Nº

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>¿Puedo agregar subredes una vez creada la red virtual?
Sí. Se pueden agregar subredes a redes virtuales en cualquier momento siempre y cuando el intervalo de direcciones de subred no sea parte de otra subred y se haya dejado espacio disponible en el intervalo de direcciones de la red virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>¿Puedo modificar el tamaño de la subred después de crearla?
Sí. Puede agregar, quitar, expandir o contraer una subred si no hay máquinas virtuales ni servicios implementados en ella.

### <a name="can-i-modify-subnets-after-i-created-them"></a>¿Puedo modificar subredes después de crearlas?
Sí. Puede agregar, quitar y modificar los bloques CIDR usados por una red virtual.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>¿Puedo conectarme a Internet si ejecuto mis servicios en una red virtual?
Sí. Todos los servicios implementados dentro de una red virtual pueden establecer una conexión a Internet saliente. Para más información sobre las conexiones a Internet salientes en Azure, consulte [Conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si quiere establecer una conexión entrante a un recurso implementado mediante Resource Manager, el recurso debe tener asignada una dirección IP pública. Para más información sobre las direcciones IP públicas, consulte [Direcciones IP publicas](virtual-network-public-ip-address.md). Cada servicio en la nube de Azure implementado en Azure tiene asignada una dirección IP virtual direccionable de forma pública. Para permitir que estos servicios acepten conexiones de Internet, se definen puntos de conexión de entrada para roles y puntos de conexión de PaaS en las máquinas virtuales.

### <a name="do-vnets-support-ipv6"></a>¿Las redes virtuales admiten IPv6?
Nº No se admite IPv6 con redes virtuales en este momento. Sin embargo, puede asignar direcciones IPv6 a equilibradores de carga de Azure para equilibrar la carga en las máquinas virtuales. Para más información, consulte [Información general de IPv6 para Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>¿Puede una red virtual abarcar varias regiones?
Nº Una red virtual está limitada a una única región. Una red virtual, sin embargo, abarca zonas de disponibilidad. Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Puede conectar redes virtuales de diferentes regiones con el emparejamiento de redes virtuales. Para más información, consulte [Emparejamiento de redes virtuales](virtual-network-peering-overview.md).

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>¿Puedo conectar una red virtual a otra red virtual en Azure?
Sí. Puede conectar una red virtual a otra mediante los métodos siguientes:
- **Emparejamiento de red virtual**: para más información, consulte [Emparejamiento de redes virtuales](virtual-network-peering-overview.md).
- **Una instancia de VPN Gateway**: para más información, consulte [cómo configurar una conexión de red virtual a red virtual](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>resolución de nombres DNS

### <a name="what-are-my-dns-options-for-vnets"></a>¿Qué opciones de DNS hay para las redes virtuales?
Use la tabla de decisiones de la página [Resolución de nombres para las máquinas virtuales e instancias de rol](virtual-networks-name-resolution-for-vms-and-role-instances.md) ; le guiará por todas las opciones de DNS disponibles.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>¿Puedo especificar servidores DNS para una red virtual?
Sí. Puede especificar direcciones IP de servidor DNS en la configuración de la red virtual. La configuración se aplica al servidor DNS predeterminado en todas las máquinas virtuales de la red virtual.

### <a name="how-many-dns-servers-can-i-specify"></a>¿Cuántos servidores DNS puedo especificar?
Consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>¿Puedo modificar mis servidores DNS después de haber creado la red?
Sí. Puede cambiar la lista de servidores DNS de la red virtual en cualquier momento. Si cambia la lista de servidores DNS, deberá reiniciar todas las máquinas virtuales de la red virtual para que puedan elegir el nuevo servidor DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>¿Qué es un DNS proporcionado por Azure? ¿Funciona con las redes virtuales?
Un DNS proporcionado por Azure es un servicio DNS multiempresa ofrecido por Microsoft. Azure registra en este servicio todas las máquinas virtuales y las instancias de rol de servicio en la nube. Este servicio proporciona la resolución de nombres mediante nombre de host para las máquinas virtuales y las instancias de rol contenidas en el mismo servicio en la nube y mediante FQDN para las máquinas virtuales y las instancias de rol en la misma red virtual. Para más información sobre DNS, consulte [Resolución de nombres para las máquinas virtuales y las instancias de rol de Cloud Services](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Existe una limitación de los 100 primeros servicios en la nube en una red virtual para la resolución de nombres entre inquilinos mediante DNS proporcionado por Azure. Si usa  su propio servidor DNS, esta limitación no es aplicable.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>¿Puedo invalidar mi configuración de DNS por máquina virtual o servicio en la nube?
Sí. Puede configurar los servidores DNS por máquina virtual o servicio en la nube para invalidar la configuración de red predeterminada. Sin embargo, se recomienda usar DNS en toda la red siempre que sea posible.

### <a name="can-i-bring-my-own-dns-suffix"></a>¿Puedo usar mi propio sufijo DNS?
Nº No puede especificar un sufijo DNS personalizado para sus redes virtuales.

## <a name="connecting-virtual-machines"></a>Conexión de máquinas virtuales

### <a name="can-i-deploy-vms-to-a-vnet"></a>¿Puedo implementar máquinas virtuales en una red virtual?
Sí. Todas las interfaces de red (NIC) conectadas a una máquina virtual implementada a través del modelo de implementación de Resource Manager deben estar conectadas a una red virtual. Las máquinas virtuales implementadas a través del modelo de implementación clásica también se pueden conectar a una red virtual.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>¿Cuáles son los distintos tipos de direcciones IP que se pueden asignar a máquinas virtuales?
* **Privada:** se asigna a cada uno de los NIC de todas las máquinas virtuales. Para asignar la dirección se puede usar el método estático o el dinámico. La direcciones IP privadas se asignan del intervalo especificado en la configuración de subred de la red virtual. A los recursos implementados a través del modelo de implementación clásica se les asignan direcciones IP privadas, aunque no estén conectadas a una red virtual. El comportamiento del método de asignación es diferente en función de si se ha implementado un recurso con el modelo de implementación clásica o de Resource Manager: 

  - **Resource Manager**: una dirección IP privada asignada con el método dinámico o estático permanece asignada a una máquina virtual (Resource Manager) hasta que se elimina el recurso. La diferencia es que cuando se usa el método estático el usuario selecciona la dirección que se asigna y cuando se usa el dinámico es Azure quien la elige. 
  - **Clásico**: las direcciones IP privadas asignadas con el método dinámico pueden cambiar cuando se reinicia una máquina virtual (clásico) después de haber estado en estado detenido (desasignada). Si necesita asegurarse de que la dirección IP privada de un recurso implementado mediante el modelo de implementación clásica no cambie nunca, asigne una dirección IP privada con el método estático.

* **Pública:** opcionalmente, se puede asignar a NIC conectadas a máquinas virtuales implementadas a través del modelo de implementación de Azure Resource Manager. La dirección se puede asignar con el método de asignación estática o el de asignación dinámica. Todas las máquinas virtuales y las instancias de rol de Cloud Services implementadas a través del modelo de implementación clásica existen en un servicio en la nube, al que se asigna una dirección IP virtual (VIP) pública y *dinámica*. Si se desea, una dirección IP pública *estática*, que se denomina [dirección IP reservada](virtual-networks-reserved-public-ip.md), puede asignarse como si fuera una VIP. Las direcciones IP públicas se pueden asignar a máquinas virtuales o instancias de rol de Cloud Services individuales implementadas mediante el modelo de implementación clásica. Estas direcciones se denominan direcciones [IP públicas a nivel de instancia (ILPIP)](virtual-networks-instance-level-public-ip.md) y se puede asignar dinámicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>¿Puedo reservar una dirección IP interna para una máquina virtual que crearé más adelante?
Nº Las direcciones IP privadas no se pueden reservar. Si hay una dirección IP privada disponible, el servidor DHCP la asigna a una máquina virtual o una instancia de rol. La máquina virtual puede ser, o no, aquella a la que quiere que se asigne la dirección IP privada. Sin embargo, la dirección IP privada de una máquina virtual ya creada se puede cambiar por cualquier dirección IP privada disponible.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>¿Cambian las direcciones IP privadas de las máquinas virtuales en una red virtual?
Depende. Si se ha implementado la máquina virtual mediante Resource Manager, no, con independencia de si la dirección IP se asignó con el método de asignación estático o dinámico. Si la máquina virtual se implementó mediante el modelo de implementación clásica, se pueden cambiar las direcciones IP dinámicas cuando se inicia una máquina virtual después de haber estado en estado detenido (desasignado). La dirección se libera de una máquina virtual implementada con cualquiera de los modelos de implementación cuando se elimina la máquina.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>¿Se pueden asignar manualmente direcciones IP a las NIC del sistema operativo de la máquina virtual?
Sí, pero no se recomienda a menos que es necesario, por ejemplo, al asignar varias direcciones IP a una máquina virtual. Para más información, consulte [Adición de varias direcciones IP a una máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Si la dirección IP asignada a una NIC de Azure asociada a una máquina virtual cambia, y la dirección IP en el sistema operativo de la máquina virtual es diferente, se pierde la conectividad a la máquina virtual.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>¿Qué les sucede a mis direcciones IP si se detiene una ranura de implementación de un servicio en la nube o se apaga una máquina virtual desde el sistema operativo?
Nada. Las direcciones IP (VIP pública, pública y privada) siguen estando asignadas a la ranura de implementación de un servicio en la nube o a la máquina virtual.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>¿Puedo mover las máquinas virtuales de una subred a otra en una red virtual sin volver a implementarla?
Sí. Puede encontrar más información en el artículo [Traslado de una máquina virtual o una instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>¿Puedo configurar una dirección MAC estática para mi máquina virtual?
Nº Una dirección MAC no se puede configurar de forma estática.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>¿Seguirá siendo la dirección MAC la misma en mi máquina virtual una vez que se ha creado?
Sí, la dirección MAC de una máquina virtual no cambia, independientemente de que esta se haya implementado a través del modelo de implementación clásica o de Resource Manager, hasta que se elimina. Antes, la dirección MAC se liberaba si la máquina virtual se detenía (se desasignaba), pero ahora la dirección MAC se conserva aunque la máquina virtual se encuentre en estado desasignada.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>¿Puedo conectarme a Internet desde una máquina virtual de una red virtual?
Sí. Todas las máquinas virtuales y las instancias de rol de Cloud Services implementadas dentro de una red virtual pueden conectarse a Internet.

## <a name="azure-services-that-connect-to-vnets"></a>Servicios de Azure que se conectan a redes virtuales

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>¿Se puede usar Azure App Service Web Apps con una red virtual?
Sí. Web Apps se puede implementar en una red virtual mediante ASE (App Service Environment). Si tiene una conexión de punto a sitio configurada para la red virtual, todas las aplicaciones web se pueden conectar a los recursos de la red virtual y acceder a ellos de forma segura. Para más información, consulte los siguientes artículos.

* [Creación de Web Apps en un entorno de App Service Environment](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integración de una aplicación con Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Uso de la integración de la red virtual y de conexiones híbridas con Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>¿Puedo implementar Cloud Services con los roles web y de trabajo (PaaS) en una red virtual?
Sí. Opcionalmente, es posible implementar instancias de rol de Cloud Services en redes virtuales. Para hacerlo, es preciso especificar el nombre de la red virtual y las asignaciones de rol/subred en la sección de configuración de red de la configuración del servicio. No es necesario actualizar ninguno de los archivos binarios.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>¿Se puede conectar un conjunto de escalado de máquinas virtuales (VMSS) a una red virtual?
Sí. Debe conectar un VMSS a una red virtual.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>¿Hay una lista completa de servicios de Azure de la que pueda implementar recursos desde una red virtual?

Sí. Para más información, consulte [Integración de red virtual para los servicios de Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>¿A qué recursos de PaaS de Azure se puede restringir el acceso desde una red virtual?

Se puede restringir el acceso a los recursos implementados mediante algunos servicios de PaaS de Azure (como Azure Storage y Azure SQL Database) en una red virtual solo mediante el uso de puntos de conexión de servicio de red virtual. Para más información, consulte [Puntos de conexión del servicio de redes virtuales](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>¿Puedo mover mis servicios dentro y fuera de las redes virtuales?
Nº No se pueden mover los servicios dentro y fuera de las redes virtuales. Para mover un recurso a otra red virtual, tendrá que eliminar el recurso y volver a implementarlo.

## <a name="security"></a>Seguridad

### <a name="what-is-the-security-model-for-vnets"></a>¿Cuál es el modelo de seguridad de las redes virtuales?
Las redes virtuales están aisladas unas de otras y de otros servicios hospedados en la infraestructura de Azure. Una máquina virtual es un límite de confianza.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>¿Puedo restringir el flujo de tráfico de entrada o salida a los recursos conectados a la red virtual?
Sí. Puede aplicar [grupos de seguridad de red](security-overview.md) a subredes individuales de una red virtual, a los NIC conectados a una red virtual, o a ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>¿Se puede implementar un firewall entre los recursos conectados a una red virtual?
Sí. A través de Azure Marketplace es posible implementar una [aplicación virtual de red de firewall](https://azure.microsoft.com/marketplace/?term=firewall) de varios proveedores.

### <a name="is-there-information-available-about-securing-vnets"></a>¿Hay información disponible acerca la protección de las redes virtuales?
Sí. Para más información, consulte [Información general sobre Azure Network Security](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>API, esquemas y herramientas

### <a name="can-i-manage-vnets-from-code"></a>¿Puedo administrar redes virtuales mediante programación?
Sí. Puede usar las API REST para redes virtuales en los modelos de implementación de [Azure Resource Manager](/rest/api/virtual-network) y [clásica (Service Management)](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>¿Hay compatibilidad con las herramientas para redes virtuales?
Sí. Más información acerca del uso de:
- Azure Portal para implementar redes virtuales a través de los modelos de implementación con [Azure Resource Manager](virtual-networks-create-vnet-arm-pportal.md) y [clásica](virtual-networks-create-vnet-classic-pportal.md).
- PowerShell para administrar redes virtuales que se implementan a través de los modelos de implementación con [Resource Manager](/powershell/module/azurerm.network) y [clásica](/powershell/module/azure/?view=azuresmps-3.7.0).
- La interfaz de la línea de comandos (CLI) de Azure para implementar y administrar redes virtuales implementadas mediante los modelos de implementación de [Resource Manager](/cli/azure/network/vnet) y [clásica](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources).  
