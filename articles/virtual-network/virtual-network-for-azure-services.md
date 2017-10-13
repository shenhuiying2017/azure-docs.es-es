---
title: Red virtual para los servicios de Azure | Microsoft Docs
description: "Obtenga información sobre las ventajas de la implementación de recursos en una red virtual. Los recursos de redes virtuales pueden comunicarse entre sí, y con recursos locales, sin tráfico que recorra Internet."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 2c3ffb8432fae41b376cc71bb600a0b1c490f345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-integration-for-azure-services"></a>Integración de red virtual para los servicios de Azure

La integración de los servicios de Azure en una instancia de Azure Virtual Network permite el acceso privado desde instancias de un servicio implementado en la red virtual.

Puede integrar los servicios de Azure en la red virtual con las siguientes opciones:
- Implementando directamente instancias dedicadas del servicio en una red virtual. Las instancias dedicadas de estos servicios pueden acceder de forma privada dentro de la red virtual y desde redes locales.
- Mediante la extensión de una red virtual al servicio, a través de puntos de conexión de servicio. Los puntos de conexión de servicio permite que los recursos de servicio individuales se protejan en la red virtual.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implementación de servicios de Azure en las redes virtuales

Puede comunicarse con la mayoría de recursos de Azure por Internet a través de direcciones IP públicas. Al implementar los servicios de Azure en una [red virtual](virtual-networks-overview.md), puede comunicarse con los recursos de servicio de forma privada a través de direcciones IP privadas.

![Servicios implementados en una red virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

La implementación de servicios dentro de una red virtual ofrece las siguientes funcionalidades:

- Los recursos dentro de la red virtual pueden comunicarse entre sí de forma privada a través de direcciones IP privadas. Ejemplo: transferencia directa de datos entre HDInsight y SQL Server que se ejecutan en una máquina virtual, en la red virtual.
- Los recursos locales pueden acceder a recursos de una red virtual mediante direcciones IP privadas a través de [VPN de sitio a sitio (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) o [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Las redes virtuales pueden estar [emparejadas](virtual-network-peering-overview.md) para habilitar que los recursos de las redes virtuales se comuniquen entre sí mediante direcciones IP privadas.
- Las instancias de servicio de una red virtual están totalmente administradas por el servicio de Azure, para supervisar el estado de dichas instancias y proporcionar la escala necesaria según la carga.
- Las instancias de servicio se implementan en una subred dedicada en una red virtual. El acceso entrante y saliente de la red se debe abrir a través de [grupos de seguridad de red](security-overview.md#network-security-groups) para la subred, siguiendo las instrucciones proporcionadas por los servicios.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Servicios que pueden implementarse en una red virtual

Cada servicio implementado directamente en la red virtual tiene requisitos específicos para el enrutamiento y los tipos de tráfico que deben permitirse dentro y fuera de subredes. Para más información, consulte: 
 
- Máquinas virtuales: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Entorno de App Service](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway (interna)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Motor de Azure Container Service](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): Azure Container Service crea una red virtual predeterminada. Puede crear una red virtual personalizada para usar con el [motor de Azure Container Service](https://github.com/Azure/acs-engine/tree/master/examples/vnet).
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): solo red virtual (clásica)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration): solo red virtual (clásica)
- [Servicios en la nube](https://msdn.microsoft.com/library/azure/jj156091): solo red virtual (clásica)

Puede implementar un [recurso interno de Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para equilibrar la carga de muchos de los recursos de la lista anterior. En algunos casos, el servicio crea e implementa automáticamente un equilibrador de carga cuando se crea un recurso.

## <a name="service-endpoints-for-azure-services"></a>Puntos de conexión de servicio para servicios de Azure

Algunos servicios de Azure no se pueden implementar en redes virtuales. Puede restringir el acceso a algunos de los recursos de servicio solo a subredes de red virtual específicas, si lo desea, habilitando un punto de conexión de servicio de red virtual. Más información sobre [puntos de conexión de servicio de redes virtuales](virtual-network-service-endpoints-overview.md).

Actualmente, se admiten puntos de conexión de servicio para los siguientes servicios: 
- **Azure Storage**: [protección de cuentas de Azure Storage para las redes virtuales](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- **Azure SQL Database**: [protección de Azure SQL Database para redes virtuales](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integración de redes virtuales en varios servicios de Azure

Puede implementar un servicio de Azure en una subred de una red virtual y proteger recursos de servicio críticos para esa subred. Por ejemplo, puede implementar HDInsight en la red virtual y proteger una cuenta de almacenamiento para la subred de HDInsight.





