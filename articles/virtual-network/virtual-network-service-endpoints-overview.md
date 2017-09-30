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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 297c51cca31d981ffadd20cccce1cd3dd69dc81c
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-service-endpoints-preview"></a>Puntos de conexión del servicio Virtual Network (versión preliminar)

Los puntos de conexión de servicio de redes virtuales extienden el espacio de direcciones privadas de la red virtual y la identidad de la red virtual a los servicios de Azure, a través de una conexión directa. Los puntos de conexión permiten proteger los recursos de servicio de Azure críticos únicamente para las redes virtuales. El tráfico desde la red virtual al servicio de Azure siempre permanece en la red troncal de Microsoft Azure.

Esta característica está disponible en __versión preliminar__ para los siguientes servicios y regiones de Azure:

__Azure Storage__: WestCentralUS, WestUS2, EastUS, WestUS, AustraliaEast, AustraliaSouthEast.

__Azure SQL Database__: WestCentralUS, WestUS2, EastUS.

Para las notificaciones de fechas más actualizadas para la versión preliminar, compruebe la página [Actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

>[!NOTE]
Durante la versión preliminar, la característica podría no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en las versiones de disponibilidad general. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Ventajas principales

Los puntos de conexión de servicio proporcionan siguientes ventajas:

- __Seguridad mejorada para los recursos de servicio de Azure__

  Con los puntos de conexión de servicio, se pueden proteger los recursos del servicio de Azure para la red virtual. Esto mejora la seguridad al quitar totalmente acceso público a Internet a estos recursos y al permitir el tráfico solo desde la red virtual.

- __Enrutamiento óptimo para el tráfico del servicio de Azure desde la red virtual__

  En la actualidad, las rutas de la red virtual que fuerzan el tráfico de Internet a través de aplicaciones virtuales o locales, conocidas como tunelización forzada, también fuerzan el tráfico del servicio de Azure para realizar la misma ruta que el tráfico de Internet. Los puntos de conexión de servicio proporcionan un enrutamiento óptimo al tráfico de Azure. 

  Los puntos de conexión siempre toman el tráfico del servicio directamente de la red virtual al servicio en la red troncal de Microsoft Azure. Esto le permite seguir auditando y supervisando el tráfico saliente de Internet desde las redes virtuales, a través de la tunelización forzada, sin que afecte al tráfico del servicio. Obtenga más información sobre las [rutas definidas por el usuario y la tunelización forzada.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)

- __Fácil de configurar con menos sobrecarga de administración__

  Ya no necesita direcciones IP públicas y reservadas en sus redes virtuales para proteger los recursos de Azure a través de un firewall IP. No hay ningún dispositivo NAT o de puerta de enlace necesario para configurar los puntos de conexión de servicio. El punto de conexión de servicio se puede configurar mediante un simple clic en una subred. No hay sobrecarga adicional para mantener los puntos de conexión.

## <a name="limitations"></a>Limitaciones

- Esta característica solo está disponible en las redes virtuales implementadas en el modelo de implementación de Azure Resource Manager.
- Los puntos de conexión están habilitados en subredes configuradas en redes virtuales de Azure. No se pueden usar los puntos de conexión para el tráfico desde el entorno local a los servicios de Azure. Para más información, consulte ["Protección del acceso de servicio de Azure desde el entorno local"](#securing-azure-services-to-virtual-networks).
- El punto de conexión de servicio se aplica solo al tráfico de servicio de Azure dentro de la región de la red virtual. En el caso de Azure Storage, para admitir tráfico RA-GRS y GRS, también se extiende para incluir una región emparejada donde está implementada la red virtual. Más información acerca de las [regiones emparejadas de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="securing-azure-services-to-virtual-networks"></a>Protección de servicios de Azure para las redes virtuales

- El punto de conexión de servicio de red virtual proporciona la identidad de la red virtual al servicio de Azure. Una vez habilitados los puntos de conexión de servicio en la red virtual, puede proteger los recursos de servicio de Azure a la red virtual mediante la adición de una regla de red virtual a los recursos.
- Hoy en día, el tráfico del servicio de Azure desde la red virtual usa direcciones IP públicas como direcciones IP de origen. Con los puntos de conexión de servicio, el tráfico del servicio cambia para utilizar direcciones privadas de red virtual como la direcciones IP de origen al acceder al servicio de Azure desde la red virtual. Este modificador permite acceder a los servicios sin necesidad de direcciones IP públicas y reservadas utilizadas en los firewalls IP.
- __Protección del acceso de servicio de Azure desde el entorno local__:
 
   De forma predeterminada, los recursos de servicio de Azure protegidos para las redes virtuales no son accesibles desde redes locales. Si desea permitir el tráfico desde el entorno local, también debe permitir las direcciones IP de NAT desde los circuitos locales o de ExpressRoute. Las direcciones IP de NAT pueden agregarse a través de la configuración de firewall IP para los recursos de servicio de Azure.
  
   __ExpressRoute__: 

    Si se utiliza [ExpressRoute](/azure/expressroute/expressroute-introduction) (ER) desde el entorno local, cada circuito Expressroute usa dos direcciones IP de NAT, que se aplican al tráfico del servicio de Azure cuando el tráfico entra en la red troncal de Microsoft Azure.  Para permitir el acceso a los recursos de servicio, debe permitir las dos direcciones IP en la configuración del firewall IP de recursos.  Para encontrar las direcciones de IP de circuito de ExpressRoute, [abra un vale de soporte con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a través de Azure Portal.

![Protección de servicios de Azure para las redes virtuales](media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuración

- Los puntos de conexión de servicio se configuran en una subred de una red virtual. Los puntos de conexión funcionan con cualquier tipo de instancias de proceso que se ejecute en esa subred.
- Solo un punto de conexión de servicio puede habilitarse para un servicio específico de una subred. Puede configurar varios puntos de conexión de servicio para todos los servicios de Azure admitidos (por ejemplo, Storage o Sql) en una subred.
- Las redes virtuales deben estar en la misma región que el recurso de servicio de Azure. Para Azure Storage, si se utilizan cuentas GRS y RA-GRS, la cuenta principal debe encontrarse en la misma región que la red virtual.
- La red virtual donde se ha configurado el punto de conexión puede estar en la misma suscripción o en otra diferente que el recurso de servicio de Azure. Para más información sobre los permisos necesarios para configurar los puntos de conexión y proteger los servicios de Azure, consulte a continuación la sección ["Aprovisionamiento"](#Provisioning).
- Para los servicios compatibles, puede proteger los nuevos recursos o los recursos existentes a las redes virtuales con puntos de conexión de servicio.

### <a name="considerations"></a>Consideraciones

- Al habilitar un punto de conexión de servicio, las direcciones IP de origen de las máquinas virtuales en la subred pasan de utilizar las direcciones IPv4 públicas a usar su dirección IPv4 privada, cuando se comunican con el servicio desde dicha subred.
  - Las conexiones TCP abiertas existentes en el servicio se cerrarán durante este cambio. Asegúrese de que no se esté ejecutando ninguna tarea crítica al habilitar o deshabilitar un punto de conexión de servicio en un servicio para una subred. Además, asegúrese de que las aplicaciones pueden conectarse automáticamente a los servicios de Azure después de este cambio de dirección IP.
  - El cambio de dirección IP solo afecta a tráfico del servicio desde la red virtual. No afecta a ningún otro tráfico dirigido a direcciones IPV4 públicas, o desde ellas, asignadas a las máquinas virtuales.
  - Para los servicios de Azure, si tiene reglas de firewall existentes que usan direcciones IP públicas de Azure, estas reglas dejan de funcionar con el cambio a las direcciones privadas de red virtual.
- Con los puntos de conexión de servicio, las entradas DNS para los servicios de Azure permanecen tal y como están ahora y seguirán resolviendo las direcciones IP asignadas al servicio de Azure.
- Grupos de seguridad de red (NSG) con puntos de conexión de servicio:
  - De forma predeterminada, los grupos de seguridad de red permiten el tráfico saliente de Internet y, por lo tanto, también permiten el tráfico desde la red virtual a los servicios de Azure. Esto continúa funcionando tal cual, con puntos de conexión de servicio.
  - Si desea denegar todo el tráfico de Internet saliente y permitir únicamente el tráfico a servicios de Azure específicos, puede hacerlo mediante las __"etiquetas de servicio de Azure"__ de sus grupos de seguridad de red. Puede especificar los servicios de Azure compatibles como destino en las reglas de grupos de seguridad de red y Azure proporciona el mantenimiento de las direcciones IP subyacentes en cada etiqueta. Para más información, consulte las [etiquetas de servicio de Azure para grupos de seguridad de red](https://aka.ms/servicetags).

### <a name="scenarios"></a>Escenarios
- Emparejado, conectado o varias redes virtuales:

Para proteger los servicios de Azure para varias subredes dentro de una red virtual o a través de varias redes virtuales, puede habilitar los puntos de conexión de servicio en cada una de estas subredes independientemente y proteger los recursos de servicio de Azure para todas estas subredes.

- Filtrado de tráfico saliente desde la red virtual a los servicios de Azure:

Si desea inspeccionar o filtrar el tráfico destinado a un servicio de Azure desde la red virtual, puede implementar una aplicación virtual de red (NVA) dentro de esa red virtual. Después, puede aplicar los puntos de conexión de servicio a la subred donde se implementa la aplicación virtual de red y se protegen los recursos de servicio de Azure solo para esta subred. Este escenario puede resultar útil si desea restringir el acceso de servicio de Azure desde la red virtual solo a recursos específicos de Azure, mediante el filtrado de la aplicación virtual de red. Para más información, lea el artículo sobre la [salida con las aplicaciones de redes virtuales](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/nva-ha#egress-with-layer-7-nvas).

- Protección de recursos de Azure con los servicios implementados directamente en redes virtuales:

Se pueden implementar directamente varios servicios de Azure en subredes específicas en sus redes virtuales. Puede proteger los recursos de servicio de Azure para subredes de [servicio administrado](virtual-network-for-azure-services.md), mediante la configuración de un punto de conexión de servicio en la subred de servicio administrada.

### <a name="logging-and-troubleshooting"></a>Registro y solución de problemas

Después de configurar los puntos de conexión de servicio para un servicio específico, valide que la ruta del punto de conexión de servicio está en vigor de la forma siguiente: 

- __Supervisión de diagnósticos del servicio de Azure__: puede confirmar que la solicitud de acceso procede de la red privada, es decir, de la red virtual, al validar la "IP de origen" de cualquier solicitud de servicio en los diagnósticos de servicio. Todas las nuevas solicitudes con puntos de conexión de servicio muestran la "IP de origen" de la solicitud como dirección de red virtual privada, asignada al cliente que realiza la solicitud desde la red virtual. Sin el punto de conexión, esta dirección será una IP pública de Azure.

- Las __rutas eficaces__ en una NIC de la subred muestran una ruta "predeterminada" más específica para el rango de prefijos de direcciones de ese servicio. La ruta tiene una clase nextHopType como "VirtualNetworkServiceEndpoint". Esta ruta indica que está en vigor más de una conexión directa al servicio, comparado con cualquier-ruta de tunelización forzada.

>[!NOTE]
La ruta del punto de conexión de servicio invalida las rutas BGP o UDR para la coincidencia del prefijo de dirección, como el servicio de Azure. Obtenga más información sobre la [solución de problemas con rutas eficaces](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

## <a name="provisioning"></a>Aprovisionamiento

Los puntos de conexión de servicio pueden configurarse en redes virtuales de forma independiente por un usuario con acceso de escritura a la red virtual.

Para proteger los recursos de servicio de Azure a una red virtual, el usuario debe tener permiso "Microsoft.Network/JoinServicetoaSubnet" para las subredes que se van a agregar. De forma predeterminada, este permiso se incluye en los roles de administrador de servicios integrado y puede modificarse mediante la creación de roles personalizados.

Obtenga más información sobre los [roles integrados](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) y la asignación de permisos específicos a [roles personalizados](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles).

Las redes virtuales y los recursos de servicio de Azure pueden encontrarse en la misma o en diferentes suscripciones. Si los recursos de servicio de Azure y de red virtual en distintas suscripciones, los recursos deben estar en el mismo inquilino de Active Directory (AD), durante esta versión preliminar. 

## <a name="pricing-and-limits"></a>Precios y límites

No hay ningún cargo adicional para el uso de puntos de conexión de servicio. El modelo de precios vigente para los servicios de Azure (Azure Storage, Azure SQL Database) se aplica tal cual.

No hay límite en el número total de puntos de conexión de servicio en una red virtual.

Para un recurso de servicio de Azure (por ejemplo, la cuenta de Storage), los servicios pueden exigir límites en el número de subredes que se usan para proteger el recurso. Consulte la documentación de varios servicios en ["Pasos siguientes"](#next%20steps) para más información.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [configurar los puntos de conexión de servicio de red virtual](virtual-network-service-endpoints-configure.md)
- Aprenda más sobre [protección de cuentas de Azure Storage para las redes virtuales](https://docs.microsoft.com/azure/storage/common/storage-network-security).
- Aprenda más sobre [protección de Azure SQL Database para las redes virtuales](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Introducción a la integración de servicio de Azure para redes virtuales](virtual-network-for-azure-services.md)


