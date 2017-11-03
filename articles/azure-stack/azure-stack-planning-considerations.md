---
title: Consideraciones de planeamiento para sistemas integrados de Azure Stack | Microsoft Docs
description: "Obtenga información acerca de lo que puede hacer para planear ahora y prepararse para Azure Stack de varios nodos."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: twooley
ms.openlocfilehash: 8484f7947f23a00c05b34babf13cd75f9d227740
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Consideraciones de planeamiento para sistemas integrados de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Si está interesado en un sistema integrado de Azure Stack, querrá comprender algunas de las principales consideraciones de planeamiento acerca de la implementación y el modo en que el sistema se adapta a su centro de datos. Este tema proporciona información general de alto nivel de estas consideraciones.

Si decide comprar un sistema integrado, el proveedor de hardware del fabricante de equipo original (OEM) le guiará a través de gran parte del proceso de planeamiento con más detalle. Asimismo, le ayudará a realizar la implementación real.

## <a name="management-considerations"></a>Consideraciones de administración

Azure Stack es un sistema sellado en el cual la infraestructura está bloqueada desde una perspectiva de red y de permisos. Las listas de control de acceso (ACL) de red se aplican para bloquear todo el tráfico entrante no autorizado y todas las comunicaciones innecesarias entre los componentes de infraestructura. Esto dificulta el acceso al sistema de los usuarios no autorizados.

Para la administración y las operaciones diarias, no hay ningún acceso de administrador a la infraestructura sin restricciones. Los operadores de Azure Stack deben administrar el sistema a través del portal de administrador o de Azure Resource Manager (a través de PowerShell o de la API de REST). No hay ningún acceso al sistema mediante otras herramientas de administración, como el Administrador de Hyper-V o el Administrador de clústeres de conmutación por error. Para ayudar a proteger el sistema, no se puede instalar software de terceros (por ejemplo, agentes) dentro de los componentes de la infraestructura de Azure Stack. La interoperabilidad con el software de seguridad y la administración externa se produce a través de PowerShell o la API de REST.

Cuando se necesita un mayor nivel de acceso para la solución de problemas que no se resuelven a través de los pasos de mediación de alerta, debe trabajar con el soporte técnico. A través del soporte técnico, hay un método para proporcionar acceso de administrador completo temporal en el sistema para realizar operaciones más avanzadas. 

## <a name="deploy-connected-or-disconnected"></a>Conectado o desconectado
 
Puede implementar Azure Stack, ya sea conectado a Internet (y a Azure) o desconectado. Para obtener el máximo provecho de Azure Stack, incluidos los escenarios híbridos entre Azure Stack y Azure, querrá implementar conectado a Azure. En la tabla siguiente, le ayudamos a resumir las diferencias principales entre los modos de implementación.

| Ámbito | Modo conectado | Modo desconectado |
| -------- | ------------- | ----------|
| Proveedor de identidades | Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS) | Solo AD FS |
| Redifusión de Marketplace | Descargar los elementos directamente de Azure Marketplace al catálogo de soluciones de Azure Stack | Requiere la administración manual del catálogo de soluciones de Azure Stack |
| Modelo de licencia | Pague por uso o según la capacidad | Solo según la capacidad|
| Revisiones y actualizaciones  | Descargar los paquetes de actualización directamente de Azure Stack | Requiere medios extraíbles y un dispositivo independiente conectado |
| | | |

No puede cambiar el modo de implementación más adelante sin volver a implementar todo el sistema.

## <a name="identity-considerations"></a>Consideraciones de identidad

### <a name="choose-identity-provider"></a>Elegir el proveedor de identidades

Debe tener en cuenta qué proveedor de identidades desea usar para la implementación de Azure Stack, Azure AD o AD FS. No puede cambiar los proveedores de identidades tras la implementación sin volver a implementar todo el sistema.

Su elección del proveedor de identidades no repercute en máquinas virtuales de inquilinos, en el sistema de identidades ni en las cuentas que utilizan, en si puede unirse a un dominio de Active Directory, etc. Esto es independiente.

**Motivos para considerar el uso de Azure AD**

- Ya tiene inversiones existentes en Azure AD (por ejemplo, Azure u Office 365).
- Quiere usar la misma identidad en las nubes de Azure y de Azure Stack.
- Quiere admitir escenarios de varios inquilinos, en los cuales puede hospedar organizaciones diferentes en la misma instancia de Azure Stack.
- Quiere usar la administración de directorios basada en REST a través de Azure AD Graph para aprovisionar usuarios, grupos, etc. a través de API.

> [!NOTE]
> No puede conectar una implementación de Azure Stack a una instancia de Azure AD y a una instancia existente de AD FS al mismo tiempo. Si implementa con Azure AD y le gustaría usar una instancia existente de AD FS, puede federar la instancia de AD FS local con Azure AD.

**Motivos para considerar el uso de AD FS**

- No hay conectividad a Internet o solo parcial.
- Existen requisitos legales o de soberanía.
- Quiere utilizar su propio sistema de identidad (por ejemplo, su Active Directory corporativo) para cuentas de usuario y de operador. Para ello, puede federar con una instancia existente de AD FS (en Windows Server 2012, 2012 R2 o 2016) que está respaldada por Active Directory.
- No hay ninguna inversión existente de Azure y no desea usar Azure AD.

> [!NOTE]
> Solo puede federar Azure Stack con otra instancia de AD FS que esté respaldada por Active Directory. No se admiten otros proveedores de identidades. Si dispone de otros proveedores de identidades que le gustaría utilizar con Azure Stack, considere la posibilidad de usar la implementación basada en Azure AD en su lugar.

### <a name="ad-fs-and-graph-integration"></a>Integración de AD FS y Graph

Si decide implementar Azure Stack mediante AD FS como proveedor de identidades, debe integrar la instancia de AD FS en Azure Stack con una instancia existente de AD FS a través de una confianza de federación. Esto permite autenticar las identidades de un bosque de Active Directory existente con los recursos de Azure Stack.

También puede integrar el servicio de Graph en Azure Stack con el Active Directory existente. Esto le permite administrar el control de acceso basado en rol (RBAC) en Azure Stack. Cuando se delega el acceso a un recurso, el componente de Graph busca la cuenta de usuario en el bosque de Active Directory existente mediante el protocolo LDAP.

El siguiente diagrama muestra el flujo de tráfico de AD FS y Graph integrado.
![Diagrama que muestra el flujo de tráfico de AD FS y Graph](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modelo de licencia

Debe decidir qué modelo de licencia desea usar. Para una implementación conectada, puede elegir licencias de pago por uso o según la capacidad. El pago por uso requiere una conexión a Azure para informar del uso, que, a continuación, se factura a través del comercio de Azure. La licencia según la capacidad solo se admite si implementa desconectado desde Internet. Para obtener más información acerca de los modelos de licencias, consulte [Paquetes y precios de Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Decisiones de nomenclaturas

Debe pensar en cómo desea planear el espacio de nombres de Azure Stack, especialmente el nombre de la región y del dominio externo. El nombre de dominio completo (FQDN) de la implementación de Azure Stack para puntos de conexión de acceso público es la combinación de estos dos nombres, &lt; *región*&gt;&lt;*external_FQDN* &gt;, por ejemplo, *east.cloud.fabrikam.com*. En este ejemplo, los portales de Azure Stack estarían disponibles en las direcciones URL siguientes:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

En la tabla siguiente se resumen estas decisiones de nomenclatura de dominio.

| Nombre | Descripción | 
| -------- | ------------- | 
|Nombre de la región | Nombre de su primera región de Azure Stack. Este nombre se usa como parte del FQDN para las direcciones IP virtuales (VIP) públicas que administra Azure Stack. Normalmente, el nombre de la región sería un identificador de ubicación física, como una ubicación de centro de datos. | 
| Nombre de dominio externo | Nombre de la zona de Sistema de nombres de dominio (DNS) para los puntos de conexión con direcciones VIP de uso externo. Se utiliza en el FQDN para estas VIP públicas. | 
| Nombre de dominio (interno) privado | Nombre del dominio (y de la zona DNS interna) creado en Azure Stack para la administración de la infraestructura. 
| | |

## <a name="certificate-requirements"></a>Requisitos de certificados

Para la implementación, debe proporcionar los certificados de Capa de sockets seguros (SSL) para puntos de conexión de acceso público. En un nivel más alto, los certificados tienen los siguientes requisitos:

> [!IMPORTANT]
> La información del certificado en este artículo se proporciona únicamente como guía general. Antes de adquirir los certificados para Azure Stack, trabaje con los asociados de hardware OEM. Le proporcionarán una orientación más detallada de los certificados y los requisitos.

- Puede usar un único certificado de carácter comodín o bien un conjunto de certificados dedicados y utilizar caracteres comodín solo para los puntos de conexión, como el almacenamiento y Key Vault.
- Los certificados los debe emitir una entidad de certificación (CA) de confianza pública o una CA administrada por el cliente.
 
La siguiente tabla muestra los servicios y el número de puntos de conexión de acceso público que requieren certificados para la implementación inicial de Azure Stack. 

| Se usa para | Extremo 
| -------- | ------------- | 
| Azure Resource Manager (administrador) | adminmanagment.[region].[external_domain] |
| Azure Resource Manager (usuario) | management.[region].[external_domain] |
| Portal (administrador) | adminportal.[region].[external_domain] |
| Portal (usuario) | portal. [region].[external_domain] |
| Key Vault (usuario) | &#42;.vault.[region].[external_domain] |
| Key Vault (administrador) | &#42;.adminvault.[region].[external_domain] |
| Storage | &#42;.blob.[region].[external_domain]<br>&#42;.table.[region].[external_domain]<br>&#42;.queue.[region].[external_domain]  |
| Graph** | graph.[region].[external_domain] |
| AD FS** | adfs.[region].[external_domain] |
| | |

**Los certificados para puntos de conexión de Graph y AD FS solo son necesarios para las implementaciones de AD FS.

Si desea usar un único certificado de carácter comodín, necesita un total de seis nombres alternativos del sujeto (SAN) para la implementación inicial de Azure Stack. A continuación, se indican dichos SAN: 

- &#42;.[region].[external_domain]
- &#42;.vault.[region].[external_domain]
- &#42;.adminvault.[region].[external_domain]
- &#42;.blob.[region].[external_domain]
- &#42;.table.[region].[external_domain] 
- &#42;.queue.[region].[external_domain]

## <a name="time-synchronization"></a>Sincronización de la hora

Debe sincronizar el servidor de hora de Azure Stack con un servidor de hora externo que se pueda resolver a través de la dirección IP. Se requiere un servidor de hora en la red corporativa para una implementación desconectada.

## <a name="network-connectivity"></a>Conectividad de red

### <a name="dns-integration"></a>Integración de DNS

Para resolver nombres DNS externos de Azure Stack (por ejemplo, www.bing.com), deberá proporcionar servidores DNS que Azure Stack pueda usar para reenviar las solicitudes DNS para las que Azure Stack no es relevante. Como entradas de la implementación, debe proporcionar al menos dos servidores que se usará como reenviadores DNS para tolerancia a errores.

Para poder resolver nombres DNS para puntos de conexión de Azure Stack desde fuera de Azure Stack (por ejemplo, desde el bosque corporativo), debe integrar los servidores DNS que hospedan la zona DNS externa para Azure Stack con los servidores DNS que hospedan la zona principal que quiere usar. Esto requiere la resolución de nombres DNS entre Azure Stack y las zonas DNS existentes en el centro de datos. Para ello, deberá usar métodos como la delegación de zona o el reenvío condicional. Se recomienda el reenvío condicional si tiene el control directo de los servidores DNS que hospedan la zona principal del espacio de nombres DNS externo de Azure Stack. (Si la zona de DNS externa de Azure Stack aparece como un dominio secundario de su nombre de dominio corporativo (por ejemplo, azurestack.contoso.com y contoso.com), debe usar la delegación de zona en su lugar.

### <a name="network-infrastructure"></a>Infraestructura de red

La infraestructura de red de Azure Stack consta de varias redes lógicas que están configuradas en los conmutadores. El diagrama siguiente muestra estas redes lógicas y cómo integrarlas con la parte superior del rack (Tor), el controlador de administración de placa base (BMC) y los conmutadores (red de cliente) de borde.

![Conexiones del conmutador y diagrama de red lógica](media/azure-stack-planning-considerations/NetworkDiagram.png)

La siguiente tabla muestra las redes lógicas y los intervalos de subred IPv4 asociados para los que se debe planificar.

| Red lógica | Descripción | Tamaño | 
| -------- | ------------- | ------------ | 
| VIP pública | Direcciones IP públicas para un pequeño conjunto de servicios de Azure Stack, con las demás usadas por máquinas virtuales de inquilinos. La infraestructura de Azure Stack utiliza 32 direcciones de esta red. Si tiene previsto usar App Service y los proveedores de recursos SQL, se usan más de 7. | /26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestructura del conmutador | Direcciones IP de punto a punto con fines de enrutamiento, interfaces de administración de conmutador dedicado y direcciones de bucle invertido asignadas al conmutador. | /26 | 
| Infraestructura | Se utiliza para que los componentes internos de Azure Stack se comuniquen. | /24 |
| Privada | Se utiliza para la red de almacenamiento y las VIP privadas. | /24 | 
| BMC | Se utiliza para comunicarse con los BMC en los hosts físicos. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Vínculo superior a los conmutadores de borde

Necesitará un vínculo superior configurado para los conmutadores de borde en el centro de datos. Puede redirigir el tráfico de esta capa 3 de los conmutadores de la parte superior del rack (Tor) que forman parte de un sistema integrado de Azure Stack mediante cualquiera de los métodos siguientes:

- Protocolo de puerta de enlace de borde (BGP) 
- enrutamiento estático

Se recomienda BGP porque permite la actualización automática de las rutas publicadas por el multiplexor de equilibrio de carga de software (SLB MUX) en las redes externas. Esto es importante si agrega los intervalos de direcciones IP públicas tras la implementación. Si hace el emparejamiento desde los conmutadores Tor al conmutador agregado al que estos están conectados, los intervalos de direcciones IP públicas que agregue tras la implementación se anuncian automáticamente al conmutador agregado sin intervención manual. Si usa el enrutamiento estático, debe actualizar manualmente las rutas de los intervalos nuevos cada vez que agregue un bloque de direcciones IP públicas.

### <a name="proxy-server"></a>Servidor proxy

Azure Stack solo admite servidores proxy transparentes. Un proxy transparente intercepta las solicitudes en el nivel de red sin necesidad de ninguna configuración especial de cliente.

### <a name="publish-azure-stack-services"></a>Publicar servicios de Azure Stack

Debe poner disponibles los servicios de Azure Stack para los usuarios externos de Azure Stack. Azure Stack configura varios puntos de conexión para sus roles de infraestructura. Estos puntos de conexión se asignan a VIP desde el grupo de direcciones IP públicas. Se crea una entrada DNS para cada punto de conexión de la zona DNS externa, que se ha especificado durante la implementación. Por ejemplo, el portal de usuarios se asigna a la entrada de host de DNS de "portal.<*region*>.<*external_FQDN*>." 

#### <a name="ports-and-urls"></a>Puertos y direcciones URL

Para que los servicios de Azure Stack (como los portales, Azure Resource Manager, DNS, etc.) estén disponible para las redes externas, debe permitir tráfico entrante en estos puntos de conexión para las direcciones URL, puertos y protocolos específicos.
 
En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional, debe permitir los siguientes puertos y direcciones URL para la comunicación saliente. Estos incluyen puertos y direcciones URL de identidad, distribución de Marketplace, revisión y actualización, registro y datos de uso.

Para más información, consulte:
- [Protocolos y puertos de entrada](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Direcciones URL y puertos de salida](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Conectar Azure Stack a Azure

Para escenarios de nube híbridos, debe planear cómo conectar Azure Stack a Azure. Hay dos métodos admitidos para conectar redes virtuales de Azure Stack a redes virtuales de Azure: 
- **De sitio a sitio**. Una conexión de red privada virtual (VPN) sobre el protocolo de seguridad de Internet (IKE v1 e IKE v2). Este tipo de conexión requiere un dispositivo VPN o un servicio de enrutamiento y acceso remoto (RRAS). Para más información acerca de VPN Gateway, consulte [Acerca de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). La comunicación a través de este túnel está cifrada y es segura. Sin embargo, el ancho de banda está limitado por el rendimiento máximo del túnel (de 100 a 200 Mbps).
- **NAT de salida**. De forma predeterminada, todas las máquinas virtuales de Azure Stack tendrán conectividad a redes externas a través de NAT de salida. Cada red virtual que se crea en Azure Stack obtiene una dirección IP pública asignada. Si la máquina virtual se asigna directamente a una dirección IP pública o está detrás de un equilibrador de carga con una dirección IP pública, tendrá acceso de salida a través de NAT de salida mediante la dirección VIP de la red virtual. Esto funciona solo para la comunicación que inicia la máquina virtual y que se destina a redes externas (Internet o intranet). No se puede utilizar para comunicarse con la máquina virtual desde fuera.

#### <a name="hybrid-connectivity-options"></a>Opciones de conectividad híbrida

Para la conectividad híbrida, es importante tener en cuenta qué tipo de implementación desea ofrecer y donde se implementará. Debe tener en cuenta si necesita aislar el tráfico de red por inquilino, y si tendrá una implementación de intranet o Internet.

- **Azure Stack de inquilino único**. Una implementación de Azure Stack que busca, al menos desde una perspectiva de la red, como si fuera un inquilino. Puede haber muchas suscripciones de inquilinos, pero al igual que con cualquier servicio de intranet, todo el tráfico se transmite a través de las mismas redes. El tráfico de red de una suscripción se transmite a través de la misma conexión de red que otra suscripción y no es necesario que esté aislado a través de un túnel cifrado.

- **Azure Stack de varios inquilinos**. Implementación de Azure Stack donde el tráfico de suscripción de cada inquilino que está limitado por redes externas a Azure Stack debe aislarse del tráfico de red de otros inquilinos.
 
- **Implementación en intranet**. Una implementación de Azure Stack que se encuentra en una intranet corporativa, normalmente en el espacio de direcciones IP privadas y detrás de uno o varios firewalls. Las direcciones IP públicas no son realmente públicas, ya que no se pueden redirigir directamente a través de la red pública de Internet.

- **Implementación en Internet**. Implementación de Azure Stack que está conectada a la red pública de Internet y usa las direcciones IP públicas que se pueden redirigir por Internet para el intervalo de dirección VIP público. La implementación todavía puede ubicarse detrás de un firewall, pero el intervalo de dirección VIP público es accesible directamente desde Azure y la red pública de Internet.
 
En la tabla siguiente se resumen los escenarios de conectividad híbrida, con las ventajas, las desventajas y los casos de uso.

| Escenario | Método de conectividad | Ventajas | Desventajas | Bueno para |
| -- | -- | --| -- | --|
| Azure Stack de inquilino único, implementación en intranet | NAT de salida | Mejor ancho de banda para transferencias más rápidas. Fácil de implementar; no se necesitan puertas de enlace. | Tráfico no cifrado; sin aislamiento ni cifrado más allá de Tor. | Implementaciones empresariales en las que se confía por igual en todos los inquilinos.<br><br>Empresas que tienen un circuito Azure ExpressRoute en Azure. |
| Azure Stack de varios inquilinos, implementación en intranet | VPN de sitio a sitio | El tráfico de la red virtual del inquilino al destino es seguro. | El ancho de banda está limitado por el túnel VPN de sitio a sitio.<br><br>Requiere una puerta de enlace en la red virtual y un dispositivo VPN en la red de destino. | Implementaciones empresariales en las que cierto tráfico de inquilinos debe protegerse de otros inquilinos. |
| Azure Stack de inquilino único, implementación en Internet | NAT de salida | Mejor ancho de banda para transferencias más rápidas. | Tráfico no cifrado; sin aislamiento ni cifrado más allá de Tor. | Escenarios de hospedaje en los que el inquilino obtiene su propia implementación de Azure Stack y un circuito dedicado en el entorno de Azure Stack. Por ejemplo, ExpressRoute y conmutación de etiquetas de multiprotocolo (MPLS).
| Azure Stack de varios inquilinos, implementación en Internet | VPN de sitio a sitio | El tráfico de la red virtual del inquilino al destino es seguro. | El ancho de banda está limitado por el túnel VPN de sitio a sitio.<br><br>Requiere una puerta de enlace en la red virtual y un dispositivo VPN en la red de destino. | Escenarios de hospedaje en los que el proveedor desea ofrecer una nube de varios inquilinos, donde los inquilinos no confían entre sí y el tráfico debe cifrarse.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>Uso de ExpressRoute

Puede conectar Azure Stack a Azure a través de [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para escenarios de intranet de inquilino único y de varios inquilinos. Necesitará un circuito ExpressRoute aprovisionado a través de [un proveedor de conectividad](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

El siguiente diagrama muestra ExpressRoute para un escenario de inquilino único (donde "Conexión del cliente" es el circuito de ExpressRoute).

![Diagrama que muestra un escenario de ExpressRoute de inquilino único](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

El siguiente diagrama muestra ExpressRoute para un escenario de varios inquilinos.

![Diagrama que muestra un escenario de ExpressRoute de varios inquilinos](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Supervisión externa
Para obtener una vista única de todas las alertas de la implementación de Azure Stack y dispositivos, así como para integrar las alertas en flujos de trabajo existentes de administración de servicio de TI para el control de vales, puede integrar Azure Stack con soluciones de supervisión del centro de datos externo.

Incluido con la solución de Azure Stack, el host de ciclo de vida de hardware es un equipo situado fuera de Azure Stack que ejecuta herramientas de administración proporcionadas por el distribuidor OEM para hardware. Puede usar estas herramientas u otras soluciones que se integran directamente con las soluciones de supervisión existentes en su centro de datos.

En la tabla siguiente se resume la lista de opciones disponibles actualmente.

| Ámbito | Solución de supervisión externa |
| -- | -- |
| Software de Azure Stack | - [Módulo de administración de Azure Stack para Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Complemento de Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>- Llamadas API basadas en REST | 
| Servidores físicos (BMC a través de IPMI) | - Módulo de administración del distribuidor de Operations Manager<br>- Solución suministrada por el distribuidor de hardware OEM<br>- Complementos de Nagios del distribuidor de hardware | Solución de supervisión admitida por el asociado de OEM (incluido) | 
| Dispositivos de red (SNMP) | -Detección de dispositivos de red de Operations Manager<br>- Solución suministrada por el distribuidor de hardware OEM<br>-Complemento conmutador de Nagios |
| Supervisión del estado de suscripción del inquilino | - [Módulo de administración de System Center para Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Tenga en cuenta los siguientes requisitos:
- La solución que use debe ser sin agente. No puede instalar agentes de terceros dentro de componentes de Azure Stack. 
- Si desea usar System Center Operations Manager, se requiere Operations Manager 2012 R2 u Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Copia de seguridad y recuperación ante desastres

El planeamiento de copia de seguridad y recuperación ante desastres implica planear tanto para la infraestructura subyacente de Azure Stack que hospeda máquinas virtuales de IaaS y servicios de PaaS como para los datos y las aplicaciones de inquilinos. Debe realizar estos planeamientos por separado.

### <a name="protect-infrastructure-components"></a>Protección de los componentes de infraestructura

Azure Stack realiza una copia de seguridad de los componentes de infraestructura en el recurso compartido que especifique.

- Necesitará un recurso compartido de archivos SMB externo en un servidor de archivos existente basado en Windows o un dispositivo de terceros.
- Debe usar este mismo recurso compartido para la copia de seguridad de los conmutadores de red y el host de ciclo de vida de hardware. El distribuidor de hardware OEM le servirá como guía para la copia de seguridad y la restauración de estos componentes ya que son externos a Azure Stack. Es responsable de ejecutar los flujos de trabajo de copia de seguridad según la recomendación del distribuidor de OEM.

Si se produce una pérdida de datos catastrófica, puede usar la copia de seguridad de la infraestructura para propagar los datos de la implementación, como las entradas de la implementación y los identificadores, las cuentas de servicio, el certificado de raíz de CA, los recursos federados (en implementaciones desconectadas), los planes, las ofertas, las suscripciones, las cuotas, las asignaciones de rol y directivas RBAC y los secretos de Key Vault.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Protección de las aplicaciones del inquilino en máquinas virtuales de IaaS

Azure Stack no realiza copia de seguridad de los datos ni de las aplicaciones de inquilinos. Debe planear la copia de seguridad y la protección de recuperación ante desastres para un destino externo a Azure Stack. La protección de inquilino es una actividad controlada por el inquilino. En el caso de las máquinas virtuales de IaaS, los inquilinos pueden usar tecnologías de invitado para proteger las carpetas de archivos, los datos de la aplicación y el estado de sistema. Sin embargo, como proveedor empresarial o de un servicio, puede que desee ofrecer una solución de copia de seguridad y recuperación en el mismo centro de datos o externamente en una nube.

Para realizar copia de seguridad de máquinas virtuales de Linux o IaaS de Windows, debe usar productos de copia de seguridad con acceso al sistema operativo del invitado para proteger el archivo, la carpeta, el estado del sistema operativo y los datos de la aplicación. Puede usar Azure Backup, System Center Data Protection Manager o los productos de terceros compatibles.

Para replicar datos en una ubicación secundaria y orquestar la conmutación por error de la aplicación si se produce un desastre, puede usar Azure Site Recovery o productos de terceros compatibles. (En la versión inicial de los sistemas integrados, Azure Site Recovery no admite conmutación por recuperación. Sin embargo, puede lograr dicha conmutación a través de un proceso manual.). Además, las aplicaciones que admitan la replicación nativa (por ejemplo, Microsoft SQL Server) podrán replicar datos a otra ubicación donde se ejecute la aplicación.

> [!IMPORTANT]
> En la versión inicial de los sistemas integrados, se admiten las tecnologías de protección que funcionan en el nivel de invitado de una máquina virtual de IaaS. No se pueden instalar agentes en los servidores de infraestructura subyacente.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de los casos de uso, las compras, los asociados y los distribuidores de hardware de OEM, consulte la página del producto de [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Para obtener información acerca del mapa de ruta y la disponibilidad geográfica para los sistemas integrados de Azure Stack, consulte las notas del producto: [Azure Stack: una extensión de Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
