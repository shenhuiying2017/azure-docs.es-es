---
title: "Puntos de conexión de servicio de Azure Virtual Network | Microsoft Docs"
description: "Aprenda a habilitar el acceso directo a los recursos de Azure desde una red virtual con puntos de conexión de servicio."
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 7b5675dacd1d9effd73f3bc51ea4efc0ea6be029
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="virtual-network-service-endpoints-preview"></a>Puntos de conexión del servicio Virtual Network (versión preliminar)

Los puntos de conexión del servicio Virtual Network (red virtual) extienden el espacio de direcciones privadas de la red virtual y la identidad de la red virtual a los servicios de Azure a través de una conexión directa. Los puntos de conexión permiten proteger los recursos de servicio de Azure críticos únicamente para las redes virtuales. El tráfico desde la red virtual al servicio de Azure siempre permanece en la red troncal de Microsoft Azure.

Esta característica está disponible en versión preliminar para los servicios y regiones de Azure siguientes:

- **Azure Storage**: todas las regiones de la nube pública de Azure.
- **Azure SQL**: todas las regiones de la nube pública de Azure.

Para las notificaciones más actualizadas sobre la versión preliminar, revise la página [Actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

>[!NOTE]
> Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Ventajas principales

Los puntos de conexión de servicio proporcionan las siguientes ventajas:

- **Seguridad mejorada para los recursos de servicio de Azure**: con los puntos de conexión de servicio, se pueden proteger los recursos del servicio de Azure en la red virtual. Proteger los recursos del servicio en una red virtual mejora la seguridad al quitar totalmente el acceso a través de Internet a estos recursos y al permitir el tráfico solo desde la red virtual.
- **Enrutamiento óptimo para el tráfico del servicio de Azure desde la red virtual**: en la actualidad, las rutas de la red virtual que fuerzan el tráfico de Internet a las aplicaciones virtuales o locales, un proceso conocido como tunelización forzada, también fuerzan el tráfico del servicio de Azure para realizar la misma ruta que el tráfico de Internet. Los puntos de conexión de servicio proporcionan un enrutamiento óptimo al tráfico de Azure. 

  Los puntos de conexión siempre toman el tráfico del servicio directamente de la red virtual al servicio en la red troncal de Microsoft Azure. Mantener el tráfico en la red troncal de Azure permite seguir auditando y supervisando el tráfico saliente de Internet desde las redes virtuales, a través de la tunelización forzada, sin que afecte al tráfico del servicio. Obtenga más información sobre las [rutas definidas por el usuario y la tunelización forzada](virtual-networks-udr-overview.md).
- **Fácil de configurar con menos sobrecarga de administración**: ya no es necesario tener direcciones IP públicas reservadas en las redes virtuales para proteger los recursos de Azure a través del firewall IP. No hay ningún dispositivo NAT o de puerta de enlace necesario para configurar los puntos de conexión de servicio. Los puntos de conexión de servicio se pueden configurar con un simple clic en una subred. No hay sobrecarga adicional para mantener los puntos de conexión.

## <a name="limitations"></a>Limitaciones

- Esta característica solo está disponible en las redes virtuales implementadas con el modelo de implementación de Azure Resource Manager.
- Los puntos de conexión están habilitados en subredes configuradas en redes virtuales de Azure. No se pueden usar los puntos de conexión para el tráfico desde las instalaciones a los servicios de Azure. Para más información, consulte [Protección del acceso del servicio de Azure desde el entorno local](#securing-azure-services-to-virtual-networks)
- Un punto de conexión de servicio se aplica solo al tráfico de servicio de Azure dentro de la región de la red virtual. Para admitir tráfico RA-GRS y GRS para Azure Storage, los puntos de conexión también se extienden para incluir regiones emparejadas donde está implementada la red virtual. Más información acerca de las [regiones emparejadas de Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).

## <a name="securing-azure-services-to-virtual-networks"></a>Protección de servicios de Azure para las redes virtuales

- Un punto de conexión de servicio de la red virtual proporciona la identidad de la red virtual al servicio de Azure. Una vez que los puntos de conexión de servicio se habilitan en la red virtual, puede proteger los recursos de servicio de Azure en la red virtual mediante la incorporación de una regla de red virtual a los recursos.
- Actualmente, el tráfico del servicio de Azure desde una red virtual usa direcciones IP públicas como direcciones IP de origen. Con los puntos de conexión de servicio, el tráfico del servicio cambia para usar direcciones privadas de red virtual como la direcciones IP de origen al acceder al servicio de Azure desde una red virtual. Este modificador permite acceder a los servicios sin necesidad de direcciones IP públicas y reservadas utilizadas en los firewalls IP.
- __Protección del acceso de servicio de Azure desde el entorno local__:

  De forma predeterminada, los recursos de servicio de Azure protegidos para las redes virtuales no son accesibles desde redes locales. Si desea permitir el tráfico desde el entorno local, también debe permitir las direcciones IP públicas (normalmente NAT) desde el entorno local o ExpressRoute. Estas direcciones IP se pueden agregar a través de la configuración del firewall de IP para los recursos de los servicios de Azure.

  ExpressRoute: si utiliza [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de las instalaciones para pares públicos o para el emparejamiento de Microsoft, tiene que identificar las direcciones IP de NAT que se utilizan. Para el emparejamiento público, cada circuito ExpressRoute usa de forma predeterminada dos direcciones IP de NAT que se aplican al tráfico del servicio de Azure cuando el tráfico entra en la red troncal de Microsoft Azure. Para el emparejamiento de Microsoft, las direcciones IP de NAT que se utilizan las proporciona el cliente o el proveedor de servicios. Para permitir el acceso a los recursos de servicio, tiene que permitir estas direcciones IP públicas en la configuración del firewall de IP de recursos. Para encontrar las direcciones de IP de circuito de ExpressRoute de los pares públicos, [abra una incidencia de soporte técnico con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a través de Azure Portal. Obtenga más información sobre [NAT para ExpressRoute para emparejamiento público y de Microsoft.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Protección de servicios de Azure para las redes virtuales](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuración

- Los puntos de conexión de servicio se configuran en una subred de una red virtual. Los puntos de conexión funcionan con cualquier tipo de instancias de proceso que se ejecute en esa subred.
- Solo un punto de conexión de servicio puede habilitarse para un servicio específico de una subred. Puede configurar varios puntos de conexión de servicio para todos los servicios de Azure admitidos (por ejemplo, Azure Storage o Azure SQL Database) en una subred.
- Las redes virtuales deben estar en la misma región que el recurso de servicio de Azure. Si se usan cuentas de Azure Storage para GRS y RA-GRS, la cuenta principal debe encontrarse en la misma región que la red virtual.
- La red virtual donde se ha configurado el punto de conexión puede estar en la misma suscripción o en otra distinta del recurso de servicio de Azure. Para más información sobre los permisos necesarios para configurar los puntos de conexión y proteger los servicios de Azure, consulte [Aprovisionamiento](#Provisioning).
- Para los servicios compatibles, puede proteger los nuevos recursos o los recursos existentes a las redes virtuales con puntos de conexión de servicio.

### <a name="considerations"></a>Consideraciones

- Después de habilitar un punto de conexión de servicio, las direcciones IP de origen de las máquinas virtuales en la subred pasan de utilizar las direcciones IPv4 públicas a usar su dirección IPv4 privada, cuando se comunican con el servicio desde dicha subred. Las conexiones TCP abiertas existentes en el servicio se cierran durante este cambio. Asegúrese de que no se esté ejecutando ninguna tarea crítica al habilitar o deshabilitar un punto de conexión de servicio en un servicio para una subred. Además, asegúrese de que las aplicaciones pueden conectarse automáticamente a los servicios de Azure después del cambio de dirección IP.

  El cambio de dirección IP solo afecta el tráfico del servicio desde la red virtual. No afecta a ningún otro tráfico dirigido a direcciones IPv4 públicas, o desde ellas, asignadas a las máquinas virtuales. Para los servicios de Azure, si tiene reglas de firewall existentes que usan direcciones IP públicas de Azure, estas reglas dejan de funcionar con el cambio a las direcciones privadas de red virtual.
- Con los puntos de conexión de servicio, las entradas DNS para los servicios de Azure permanecen tal y como están ahora y seguirán resolviendo las direcciones IP públicas asignadas al servicio de Azure.
- Grupos de seguridad de red (NSG) con puntos de conexión de servicio:
  - De forma predeterminada, los grupos de seguridad de red permiten el tráfico saliente de Internet y, por lo tanto, también permiten el tráfico desde la red virtual a los servicios de Azure. Esto continúa funcionando tal cual, con puntos de conexión de servicio. 
  - Si desea denegar todo el tráfico de Internet saliente y permitir únicamente el tráfico a servicios de Azure específicos, puede hacerlo mediante las __"etiquetas de servicio de Azure"__ de sus grupos de seguridad de red. Puede especificar los servicios de Azure compatibles como destino en las reglas de grupos de seguridad de red y Azure proporciona el mantenimiento de las direcciones IP subyacentes en cada etiqueta. Para más información, consulte las [etiquetas de servicio de Azure para grupos de seguridad de red](https://aka.ms/servicetags). 

### <a name="scenarios"></a>Escenarios

- **Emparejado, conectado o varias redes virtuales**: para proteger los servicios de Azure a varias subredes dentro de una red virtual o entre varias redes virtuales, puede habilitar los puntos de conexión de servicio en cada una de las subredes de manera independiente y proteger los recursos del servicio de Azure a todas las subredes.
- **Filtrado de tráfico saliente desde una red virtual a los servicios de Azure**: si desea inspeccionar o filtrar el tráfico destinado a un servicio de Azure desde una red virtual, puede implementar una aplicación virtual de red dentro de la red virtual. Después, puede aplicar los puntos de conexión de servicio a la subred donde se implementa la aplicación virtual de red y se protegen los recursos de servicio de Azure solo para esta subred. Este escenario puede resultar útil si desea restringir el acceso de servicio de Azure desde la red virtual solo a recursos específicos de Azure, mediante el filtrado de la aplicación virtual de red. Para más información, consulte el artículo sobre la [salida con las aplicaciones de redes virtuales](/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **Protección de los recursos de Azure para servicios implementados directamente en redes virtuales**: se pueden implementar directamente varios servicios de Azure en subredes específicas en sus redes virtuales. Puede proteger los recursos de servicio de Azure para subredes de [servicio administrado](virtual-network-for-azure-services.md) mediante la configuración de un punto de conexión de servicio en la subred de servicio administrada.

### <a name="logging-and-troubleshooting"></a>Registro y solución de problemas

Después de configurar los puntos de conexión de servicio para un servicio específico, valide que la ruta del punto de conexión de servicio está en vigor de la forma siguiente: 
 
- Valide la dirección IP de origen de todas las solicitudes de servicio en los diagnósticos del servicio. Todas las nuevas solicitudes con puntos de conexión de servicio muestran la dirección IP de origen de la solicitud como la dirección de red virtual privada, asignada al cliente que realiza la solicitud desde la red virtual. Sin el punto de conexión, la dirección es una dirección IP pública de Azure.
- Visualice las rutas eficaces en cualquier interfaz de red de una subred. La ruta al servicio:
  - Muestra una ruta predeterminada más específica a los intervalos de prefijos de la dirección de cada servicio
  - Tiene una clase nextHopType de *VirtualNetworkServiceEndpoint*
  - Indica que está en vigor una conexión más directa al servicio, en comparación con cualquier-ruta de tunelización forzada

>[!NOTE]
> Las rutas del punto de conexión de servicio invalidan las rutas BGP o UDR para la coincidencia del prefijo de dirección de un servicio de Azure. Obtenga más información sobre la [solución de problemas con rutas eficaces](virtual-network-routes-troubleshoot-portal.md#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>Aprovisionamiento

Los puntos de conexión de servicio pueden configurarse en redes virtuales de forma independiente por un usuario con acceso de escritura a una red virtual. Para proteger los recursos de servicio de Azure a una red virtual, el usuario debe tener permiso *Microsoft.Network/JoinServicetoaSubnet* para las subredes que se van a agregar. De forma predeterminada, este permiso se incluye en los roles de administrador de servicios integrado y puede modificarse mediante la creación de roles personalizados.

Obtenga más información sobre los [roles integrados](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y la asignación de permisos específicos a [roles personalizados](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Las redes virtuales y los recursos de servicio de Azure pueden encontrarse en la misma o en diferentes suscripciones. Si los recursos de servicio de Azure y de red virtual en distintas suscripciones, los recursos deben estar en el mismo inquilino de Active Directory (AD), durante la versión preliminar. 

## <a name="pricing-and-limits"></a>Precios y límites

No hay ningún cargo adicional para el uso de puntos de conexión de servicio. El modelo de precios vigente para los servicios de Azure (Azure Storage, Azure SQL Database) se aplica tal cual.

No hay límite en el número total de puntos de conexión de servicio en una red virtual.

Para un recurso de servicio de Azure (por ejemplo, una cuenta de Azure Storage), los servicios pueden exigir límites en el número de subredes que se usan para proteger el recurso. Consulte la documentación de varios servicios en [Pasos siguientes](#next-steps) para más información.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [configurar los puntos de conexión de servicio de la red virtual](virtual-network-service-endpoints-configure.md)
- Obtenga información sobre cómo [proteger una cuenta de Azure Storage para una red virtual](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Obtenga información sobre cómo [proteger una instancia de Azure SQL Database para una red virtual](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Obtenga información sobre la [integración del servicio de Azure en redes virtuales](virtual-network-for-azure-services.md)
-  Guía de inicio rápido: [plantilla de Azure Resource Manager](https://azure.microsoft.com/en-us/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) para establecer el punto de conexión de servicio en una subred de la red virtual y una cuenta segura de Azure Storage para esa subred.

