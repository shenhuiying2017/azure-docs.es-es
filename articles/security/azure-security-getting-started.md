---
title: "Introducción a la seguridad de Microsoft Azure | Microsoft Docs"
description: "Este artículo proporciona información general sobre las funcionalidades de seguridad de Microsoft Azure y aspectos generales que deben tener en cuentas las organizaciones que van a migrar sus recursos a un proveedor de nube."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 8d8a0088-c85a-48e7-bd04-2bc7b78b0691
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: e1ee07f2284df925b8bbd9050de7ae40fa66bf65
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="getting-started-with-microsoft-azure-security"></a>Introducción a la seguridad de Microsoft Azure
Al crear o migrar recursos de TI a un proveedor de nube, confía en las capacidades de la organización para proteger las aplicaciones y los datos con los servicios y los controles que facilitan para administrar la seguridad de sus recursos en la nube.

La infraestructura de Azure está diseñada desde la instalación hasta las aplicaciones para hospedar millones de clientes simultáneamente, y proporciona una base de confianza en la que las empresas pueden satisfacer sus necesidades de seguridad. Además, Azure ofrece una amplia gama de opciones de seguridad configurables, así como la posibilidad de controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos únicos de sus implementaciones.

En este artículo de información general sobre la seguridad de Azure, veremos:

* Servicios y características de Azure que puede usar para ayudar a proteger sus servicios y datos en Azure
* Cómo Microsoft protege la infraestructura de Azure para ayudar a proteger sus datos y aplicaciones

## <a name="identity-and-access-management"></a>Administración de identidades y acceso
Controlar el acceso a la infraestructura de TI, los datos y las aplicaciones es fundamental. Microsoft Azure proporciona estas funcionalidades con servicios como Azure Active Directory (Azure AD) o Azure Storage, y admite numerosos estándares y API.

[Azure AD](../active-directory/active-directory-whatis.md) es un repositorio de identidades y un motor que proporciona autenticación, autorización y control de acceso para usuarios, grupos y objetos de una organización. Azure AD ofrece a los desarrolladores una forma eficaz de integrar la administración de identidades en sus aplicaciones. Protocolos estándar del sector como [SAML 2.0](https://en.wikipedia.org/wiki/SAML_2.0), [WS-Federation](https://msdn.microsoft.com/library/bb498017.aspx) y [OpenID Connect](http://openid.net/connect/) hacen posible el inicio de sesión en plataformas como .NET, Java, Node.js y PHP.

La API Graph basada en REST permite a los desarrolladores leer y escribir en el directorio desde cualquier plataforma. Gracias a la compatibilidad con [OAuth 2.0](http://oauth.net/2/), los desarrolladores pueden compilar aplicaciones móviles y web que se integran con las API web de Microsoft y de terceros, así como crear API web seguras propias. Hay bibliotecas de cliente de código abierto disponibles para .NET, la Tienda Windows, iOS y Android, así como otras bibliotecas en fase de desarrollo.

### <a name="how-azure-enables-identity-and-access-management"></a>Cómo habilita Azure la administración de identidades y acceso
Azure AD se puede usar como un directorio en la nube independiente para su organización, o bien como una solución integrada con su Active Directory local existente. Algunas características de integración incluyen la sincronización de directorios y el inicio de sesión único (SSO). Estos extienden la cobertura de sus identidades locales existentes a la nube y mejoran la experiencia del administrador y del usuario.

Otras capacidades de administración de identidades y acceso incluyen:

* Azure AD habilita [SSO](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) para las aplicaciones SaaS, independientemente de donde estén hospedadas. Algunas aplicaciones están federadas con Azure AD y otras usan SSO con contraseña. Las aplicaciones federadas también pueden admitir el aprovisionamiento de usuarios y almacén de contraseñas.
* El acceso a datos en [Azure Storage](https://azure.microsoft.com/services/storage/) se controla mediante la autenticación. Cada cuenta de almacenamiento tiene una clave principal ([clave de la cuenta de almacenamiento](https://msdn.microsoft.com/library/azure/ee460785.aspx), o SAK) y una clave secreta secundaria (la firma de acceso compartido, o SAS).
* Azure AD proporciona identidad como servicio a través de la federación (mediante los [Servicios de federación de Active Directory](../active-directory/fundamentals-identity.md)), sincronización y replicación de directorios locales.
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) es el servicio de autenticación multifactor que requiere que los usuarios también comprueben los inicios de sesión mediante una aplicación móvil, una llamada de teléfono o un mensaje de texto. Se puede usar con Azure AD como ayuda para proteger los recursos locales con el servidor de Azure Multi-Factor Authentication, y también con aplicaciones y directorios personalizados mediante el SDK.
* [Servicios de dominio de Azure AD ](https://azure.microsoft.com/services/active-directory-ds/) permite unir máquinas virtuales de Azure a un dominio sin necesidad de implementar controladores de dominio. Puede conectarse a estas máquinas virtuales con sus credenciales corporativas de Active Directory y administrar las máquinas virtuales unidas a un dominio mediante una directiva de grupo para aplicar una base de referencia de seguridad en todas sus máquinas virtuales de Azure.
* [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) proporciona un servicio de administración de identidades global y de alta disponibilidad para aplicaciones de consumo que se escalan a cientos de millones de identidades. Se puede integrar en plataformas móviles y web. Los consumidores pueden conectarse a todas sus aplicaciones con una experiencia totalmente personalizable, usando sus cuentas de las redes sociales o mediante credenciales nuevas.

## <a name="data-access-control-and-encryption"></a>Control de acceso de datos y cifrado
Microsoft emplea los principios de separación de funciones y [privilegios](https://en.wikipedia.org/wiki/Principle_of_least_privilege) mínimos en las operaciones de Azure. El acceso a los datos por el personal de soporte técnico de Azure requiere su permiso explícito y se concede de forma "just-in-time" que se registra y audita; a continuación, revoca tras la finalización de la contratación.

Azure también proporciona varias funcionalidades para proteger los datos en tránsito y en reposo. Por ejemplo, cifrado de datos, archivos, aplicaciones, servicios, comunicaciones y unidades. Puede cifrar la información antes de colocarla en Azure y también almacenar las claves en sus centros de datos locales.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig1.PNG)

### <a name="azure-encryption-technologies"></a>Tecnologías de cifrado de Azure
Puede recopilar detalles sobre el acceso administrativo a su entorno de suscripción mediante el uso de [informes de Azure AD](../active-directory/active-directory-reporting-audit-events.md). Puede configurar [Cifrado de unidad BitLocker](https://technet.microsoft.com/library/cc732774.aspx) en los discos duros virtuales que contienen información confidencial en Azure.

Otras funciones de Azure que le ayuda a proteger los datos incluyen:

* Los desarrolladores de aplicaciones pueden integrar cifrado en las aplicaciones que se implementan en Azure mediante [CryptoAPI](https://msdn.microsoft.com/library/ms867086.aspx) de Windows y .NET Framework.
* Controle completamente las claves con el cifrado en el cliente para Azure Blob Storage. El servicio de almacenamiento no ve nunca las claves y no es capaz de descifrar los datos.
* [Azure Rights Management (Azure RMS)](https://technet.microsoft.com/library/jj585026.aspx) (con [RMS SDK](https://msdn.microsoft.com/library/dn758244.aspx)) proporciona cifrado de nivel de datos y archivos y prevención de pérdida de datos mediante la administración de acceso basada en directivas.
* Azure es compatible con el [cifrado de nivel de tabla y columna (TDE/CLE)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database.aspx) en máquinas virtuales de SQL Server y admite servidores de administración de claves locales de terceros en centros de datos.
* Las claves de cuenta de almacenamiento, las firmas de acceso compartido, los certificados de administración y otras claves son únicas para cada inquilino de Azure.
* El almacenamiento híbrido de Azure [StorSimple](http://www.microsoft.com/server-cloud/products/storsimple/overview.aspx) cifra los datos mediante un par de claves pública y privada antes de actualizarlos a Azure Storage.
* Azure admite y usa numerosos mecanismos de cifrado, como SSL/TLS, IPsec y AES, en función de los tipos de datos, los contenedores y los transportes.

## <a name="virtualization"></a>Virtualización
La plataforma de Azure usa un entorno virtualizado. Las instancias de usuario funcionan como máquinas virtuales independientes que no tienen acceso a un servidor host físico, y este aislamiento se aplica mediante [niveles de privilegios de procesador (anillo 0/anillo 3)](https://en.wikipedia.org/wiki/Protection_ring) físicos.

El anillo 0 tiene los mayores privilegios y el anillo 3 tiene los menores. El sistema operativo invitado se ejecuta en el anillo 1, con menos privilegios, y las aplicaciones en el anillo 3, el que menos privilegios tiene. Esta virtualización de recursos físicos conduce a una separación clara entre el sistema operativo invitado e hipervisor, lo que resulta en la separación de seguridad adicional entre los dos.

El hipervisor de Azure actúa como un micronúcleo y pasa todas las solicitudes de acceso al hardware desde las máquinas virtuales invitadas hasta el host para procesarlas mediante una interfaz de memoria compartida denominada VMBus. Esto impide que los usuarios obtengan acceso de lectura/escritura/ejecución sin procesar en el sistema y reduce el riesgo de compartir recursos del sistema.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig2.PNG)

### <a name="how-azure-implements-virtualization"></a>Cómo implementa Azure la virtualización
Azure usa un firewall de hipervisor (filtro de paquetes), que se implementa en el hipervisor y se configura mediante un agente de controlador de tejido. Esto ayuda a proteger los inquilinos contra accesos no autorizados. De forma predeterminada, cuando se crea una máquina virtual, se bloquea todo el tráfico y luego el agente de controlador de tejido configura el filtro de paquetes para agregar *reglas y excepciones* a fin de permitir el tráfico autorizado.

Existen dos categorías de reglas que se programan aquí:

* **Reglas de configuración de la máquina o la infraestructura**: de forma predeterminada, se bloquea toda la comunicación. Existen excepciones para permitir que una máquina virtual envíe y reciba tráfico DHCP y DNS. Las máquinas virtuales también pueden enviar tráfico a la Internet "pública" y a otras máquinas virtuales del clúster y el servidor de activación del sistema operativo. La lista de máquinas virtuales de destinos de salida permitidos no incluye subredes de enrutador de Azure, back-end de administración de Azure y otras propiedades de Microsoft.
* **Archivo de configuración de roles**: define las listas de control de acceso (ACL) de entrada según el modelo de servicio del inquilino. Por ejemplo, si un inquilino tiene un front-end web en el puerto 80 de una determinada máquina virtual, Azure abre el puerto TCP 80 para todas las direcciones IP si configura un punto de conexión en el [modelo de implementación clásica de Azure](../azure-resource-manager/resource-manager-deployment-model.md). Si la máquina virtual tiene un back-end o un rol de trabajo en funcionamiento, se abre el rol de trabajo solo para la máquina virtual dentro del mismo inquilino.

## <a name="isolation"></a>Aislamiento
Otro importante requisito de seguridad de la nube es la separación entre implementaciones para evitar la transferencia no autorizada y accidental de información en una arquitectura multiinquilino compartida.

Azure implementa el [control de acceso de red](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/) y la segregación mediante aislamiento de VLAN, listas ACL, equilibradores de carga y filtros IP. Restringe el tráfico externo que entra en los puertos y protocolos en las máquinas virtuales que defina. Azure implementa filtrado de red para impedir tráfico falsificado y restringe el tráfico entrante y saliente a los componentes de plataforma segura. Se implementan directivas de flujo de tráfico en los dispositivos de protección de límites que deniegan el tráfico de forma predeterminada.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig3.PNG)

La traducción de direcciones de red (NAT) se usa para separar el tráfico de red interno de tráfico externo. El tráfico interno no es enrutable externamente. Las [direcciones IP virtuales](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que sean enrutables externamente se traducen en [direcciones IP dinámicas internas](http://blogs.msdn.com/b/cloud_solution_architect/archive/2014/11/08/vips-dips-and-pips-in-microsoft-azure.aspx) que solo se pueden enrutar en Azure.

El tráfico externo a máquinas virtuales de Azure atraviesa el firewall mediante listas ACL en enrutadores, equilibradores de carga y conmutadores de nivel 3. Solo se permiten determinados protocolos conocidos. Las ACL se usan para limitar el tráfico procedente de las máquinas virtuales invitadas a las otras VLAN usadas para la administración. Además, el tráfico filtrado a través de filtros IP en el sistema operativo host limita aún más el tráfico en vínculos de datos y niveles de red.

### <a name="how-azure-implements-isolation"></a>Cómo implementa Azure el aislamiento
El controlador de tejido de Azure es responsable de asignar recursos de infraestructura a cargas de trabajo de inquilinos y administra las comunicaciones unidireccionales desde el host hasta las máquinas virtuales. El hipervisor de Azure fuerza la separación de la memoria y el proceso entre las máquinas virtuales y enruta de forma segura el tráfico de red a los inquilinos del sistema operativo invitado. Azure también implementa el aislamiento de inquilinos, el almacenamiento y las redes virtuales.

* Cada inquilino de Azure AD está aislado de forma lógica mediante límites de seguridad.
* Las cuentas de Azure Storage son únicas para cada suscripción y el acceso debe autenticarse con una clave de cuenta de almacenamiento.
* Las redes virtuales están aisladas de forma lógica mediante una combinación de direcciones IP privadas únicas, firewalls y listas ACL de IP. Los equilibradores de carga enrutan el tráfico a los inquilinos adecuados en función de las definiciones de puntos de conexión.

## <a name="virtual-networks-and-firewalls"></a>Redes virtuales y firewalls
Las [redes distribuidas y virtuales](http://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) de Azure ayudan a garantizar que el tráfico de red privada se aísla de forma lógica del tráfico en otras redes virtuales de Azure.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig4.PNG)

Su suscripción puede contener varias redes privadas aisladas (e incluir firewall, equilibrio de carga y traducción de direcciones de red).

Azure proporciona tres niveles principales de segregación de red en cada clúster de Azure para separar el tráfico lógicamente. Las [redes de área local virtuales](https://azure.microsoft.com/services/virtual-network/) (VLAN) se usan para separar el tráfico del cliente del resto de la red de Azure. El acceso a la red de Azure desde fuera del clúster se restringe a través de equilibradores de carga.

El tráfico de red hacia y desde máquinas virtuales debe pasar por el conmutador virtual del hipervisor. El componente del filtro IP en el sistema operativo raíz aísla la máquina virtual raíz de las máquinas virtuales invitadas y las máquinas virtuales invitadas entre sí. Además, realiza el filtrado del tráfico para restringir la comunicación entre los nodos del inquilino y la Internet pública (según la configuración del servicio del cliente), y los separa de otros inquilinos.

El filtro IP ayuda a impedir que las máquinas virtuales:

* Generen tráfico falsificado
* Reciban tráfico que no está dirigido a ellas
* Dirijan tráfico a puntos de conexión protegidos de la infraestructura
* Envíen o reciban tráfico de difusión inadecuado

Puede colocar las máquinas virtuales en [Azure Virtual Networks](https://azure.microsoft.com/documentation/services/virtual-network/). Estas redes virtuales son similares a las redes que se configuran en entornos locales, que normalmente se asocian a un conmutador virtual. Las máquinas virtuales conectadas a la misma red virtual pueden comunicarse entre sí sin ninguna configuración adicional. También puede configurar subredes diferentes dentro de la red virtual.

Puede usar las siguientes tecnologías de Azure Virtual Network para ayudar a proteger las comunicaciones de la red virtual:

* [**Grupos de seguridad de red (NSG)**](../virtual-network/virtual-networks-nsg.md) Puede usar un grupo de seguridad de red para controlar el tráfico a una o más instancias de máquina virtual en la red virtual. Un grupo de seguridad de red contiene reglas de control de acceso que permitan o denieguen el tráfico según la dirección del tráfico, el protocolo, la dirección de origen y el puerto, y la dirección de destino y el puerto.
* [**Enrutamiento definido por el usuario**](../virtual-network/virtual-networks-udr-overview.md) Puede controlar el enrutamiento de paquetes a través de un dispositivo virtual mediante la creación de rutas definidas por el usuario que especifican el próximo salto de los paquetes que fluyen a una subred específica para que así vayan a su dispositivo de seguridad de red virtual.
* [**Reenvío de IP**](../virtual-network/virtual-networks-udr-overview.md) Un dispositivo de seguridad de red virtual debe ser capaz de recibir el tráfico entrante que no se dirige a sí mismo. Para permitir que una máquina virtual reciba el tráfico dirigido a otros destinos, debe habilitar el reenvío IP en la máquina virtual.
* [**Tunelización forzada**](../vpn-gateway/vpn-gateway-about-forced-tunneling.md). La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet generado por las máquinas virtuales en una red virtual de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría.
* [**Listas ACL de puntos de conexión**](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Puede controlar en qué equipos se permiten las conexiones entrantes de Internet a una máquina virtual en la red virtual mediante la definición de las listas ACL de puntos de conexión.
* [**Soluciones de seguridad de red de asociados**](https://azure.microsoft.com/marketplace/). Hay una serie de soluciones de seguridad de red de asociados a la que puede tener acceso desde Azure Marketplace.

### <a name="how-azure-implements-virtual-networks-and-firewalls"></a>Cómo implementa Azure las redes virtuales y los firewalls
Azure implementa firewalls de filtrado de paquetes en todas las máquinas virtuales host e invitadas de forma predeterminada. Las imágenes del sistema operativo Windows desde Azure Marketplace también tienen el Firewall de Windows habilitado de forma predeterminada. Los equilibradores de carga en el perímetro de redes públicas de Azure controlan las comunicaciones según las listas ACL de IP administradas por los administradores del cliente.

Si Azure mueve los datos del cliente como parte de las operaciones normales o durante un desastre, lo hace a través de canales de comunicaciones privados y cifrados. Otras funcionalidades que aprovecha Azure para usar en redes virtuales y firewalls son:

* **Firewall de host nativo**: Azure Service Fabric y Azure Storage se ejecutan en un sistema operativo nativo que no tiene ningún hipervisor. De ahí que el firewall de Windows esté configurado con los dos conjuntos de reglas anteriores. El almacenamiento se ejecuta nativo para optimizar el rendimiento.
* **Firewall de host**: el firewall del host sirve para proteger el sistema operativo host que ejecuta el hipervisor. Las reglas se programan para permitir que solo el controlador de Service Fabric y los cuadros de salto se comuniquen con el sistema operativo del host en un puerto específico. Las otras excepciones son para permitir la respuesta DHCP y las respuestas DNS. Azure usa un archivo de configuración de la máquina que tiene la plantilla de reglas de firewall para el sistema operativo host. El propio host está protegido contra ataques externos por un firewall de Windows configurado para permitir únicamente la comunicación de orígenes conocidos y autenticados.
* **Firewall de invitado**: replica las reglas del filtro de paquetes del conmutador de máquina virtual, pero programado con otro software (es decir, el Firewall de Windows del sistema operativo invitado). El firewall de la máquina virtual invitada puede configurarse para restringir las comunicaciones hacia o desde la máquina virtual invitada, incluso si la configuración permite la comunicación en el filtro IP del host. Por ejemplo, puede usar el firewall de la máquina virtual invitada para restringir la comunicación entre dos de sus redes virtuales que se han configurado para conectarse entre sí.
* **Firewall de almacenamiento (FW)**: el firewall en el front-end de almacenamiento filtra el tráfico solo en los puertos 80/443 y otros puertos de utilidad necesarios. El firewall en el back-end de almacenamiento restringe las comunicaciones a solo las que proceden de los servidores front-end de almacenamiento.
* **Puerta de enlace de red virtual**: las [puertas de enlace de red virtual de Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) sirven como puertas de enlace entre entornos que conectan las cargas de trabajo de Azure Virtual Network con sus sitios locales. Es necesaria para conectarse a los sitios locales a través de [túneles VPN de sitio a sitio IPsec](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) o a través de circuitos [ExpressRoute](../expressroute/expressroute-introduction.md). Para los túneles VPN de IPsec/IKE, las puertas de enlace realizan protocolos de enlace de IKE y establecen los túneles VPN de IPsec S2S entre las redes virtuales y los sitios locales. Las puertas de enlace de red virtual también terminan las [VPN de punto a sitio](../vpn-gateway/vpn-gateway-point-to-site-create.md).

## <a name="secure-remote-access"></a>Acceso remoto seguro
Los datos almacenados en la nube deben tener suficientes medidas de seguridad habilitadas para evitar vulnerabilidades de seguridad y mantener la confidencialidad e integridad durante el tránsito. Esto incluye controles de red que se enlazan con los mecanismos de administración de acceso e identidades auditables basados en las directivas de una organización.

La tecnología de cifrado integrada permite cifrar las comunicaciones dentro y entre las implementaciones, entre las regiones de Azure y de Azure a centros de datos locales. El acceso de administrador a máquinas virtuales mediante [sesiones de escritorio remoto](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), [Windows PowerShell remoto](http://blogs.technet.com/b/heyscriptingguy/archive/2013/09/07/weekend-scripter-remoting-the-cloud-with-windows-azure-and-powershell.aspx) y Azure Portal está siempre cifrado.

Para ampliar su centro de datos local a la nube de forma segura, Azure proporciona [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) y [VPN de punto a sitio](../vpn-gateway/vpn-gateway-point-to-site-create.md), así como vínculos dedicados con [ExpressRoute](../expressroute/expressroute-introduction.md) (las conexiones a Azure Virtual Network a través de VPN están cifradas).

### <a name="how-azure-implements-secure-remote-access"></a>Cómo implementa Azure el acceso remoto seguro
Las conexiones a Azure Portal siempre se deben autenticar, y requieren SSL/TLS. Puede configurar certificados de administración para habilitar la administración segura. Los protocolos seguros estándar del sector [SSTP](https://technet.microsoft.com/magazine/2007.06.cableguy.aspx) e [IPsec](https://en.wikipedia.org/wiki/IPsec) son totalmente compatibles.

[Azure ExpressRoute](../expressroute/expressroute-introduction.md) permite crear conexiones privadas entre los centros de datos de Azure y la infraestructura de su entorno local o de un entorno de ubicación compartida. Las conexiones ExpressRoute no pasan por la red pública de Internet. Ofrecen más confiabilidad, velocidades más rápidas, latencias más bajas y mayor seguridad que los vínculos típicos basados en Internet. En algunos casos, el uso de conexiones de ExpressRoute para transferir datos entre ubicaciones locales y Azure también puede aportar beneficios económicos importantes.

## <a name="logging-and-monitoring"></a>Registro y supervisión
Azure proporciona un registro autenticado de eventos relevantes para la seguridad que genera una pista de auditoría y está diseñada para resistir manipulaciones. Esto incluye información del sistema, como registros de eventos de seguridad en las máquinas virtuales de infraestructura de Azure y Azure AD. La supervisión de eventos de seguridad incluye la recopilación de eventos, como cambios en direcciones IP del servidor DNS o DHCP, intentos de acceso a los puertos, protocolos o direcciones IP que están bloqueadas por diseño, cambios en la configuración del firewall o la directiva de seguridad, creación de cuentas o grupos, procesos inesperados o instalación de controladores.

![Microsoft Antimalware en Azure](./media/azure-security-getting-started/sec-azgsfig5.PNG)

La grabación de registros de auditoría de accesos de usuario con privilegios y actividades, intentos de acceso autorizados y no autorizados, excepciones del sistema e información de eventos de seguridad se conservan durante un período de tiempo establecido. El mantenimiento de los registros depende de usted, ya que usted configura la recopilación y mantenimiento de los registros de acuerdo con sus propios requisitos.

### <a name="how-azure-implements-logging-and-monitoring"></a>Cómo implementa Azure el registro y la supervisión
Azure implementa los agentes de administración (MA) y el agente Monitor de seguridad de Azure (ASM) para cada proceso, almacenamiento o nodo de tejido en la administración, ya sean nativos o virtuales. Cada agente de administración está configurado para autenticarse en una cuenta de almacenamiento del equipo de servicio con un certificado obtenido en el almacén de certificados de Azure y reenvía el diagnóstico preconfigurados y los datos de eventos a la cuenta de almacenamiento. Estos agentes no se implementan en las máquinas virtuales de los clientes.

Los administradores de Azure tienen acceso a los registros a través de un portal web para el acceso autenticado y controlado a los registros. Un administrador puede analizar, filtrar, correlacionar y analizar los registros. Las cuentas de almacenamiento del equipo de servicio de Azure para los registros están protegidas contra el acceso directo del administrador para ayudar a evitar la alteración del registro.

Microsoft recopila registros de dispositivos de red mediante el protocolo Syslog y de servidores host mediante los servicios de recopilación de auditorías (ACS) de Microsoft. Estos registros se colocan en una base de datos de registros desde la que se generan alertas para eventos sospechosos. El administrador puede acceder a estos registros y analizarlos.

[Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) es una característica de Azure que permite recopilar datos de diagnóstico de una aplicación que se está ejecutando en Azure. Se trata de datos de diagnóstico para depurar y solucionar problemas, medir el rendimiento, supervisar el uso de los recursos, analizar el tráfico y planificar la capacidad y realizar auditorías. Después de recopilar los datos de diagnóstico, se pueden transferir a una cuenta de almacenamiento de Azure para la persistencia. Las transferencias pueden se programadas o a petición.

## <a name="threat-mitigation"></a>Mitigación de amenazas
Además del aislamiento, el cifrado y el filtrado, Azure emplea a una serie de mecanismos y procesos de mitigación de amenazas para proteger la infraestructura y los servicios. Estos incluyen tecnologías y controles internos usados para detectar y corregir amenazas avanzadas como DDoS, aumento de privilegios y [OWASP Top-10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

Los controles de seguridad y la administración de riesgos implantados por Microsoft para proteger su infraestructura en la nube reducen el riesgo de incidentes de seguridad. En caso de que se produzca un incidente, el equipo de administración de incidentes de seguridad (SIM) del equipo de los servicios en línea de seguridad y cumplimiento de Microsoft (OSSC) está listo para responder 24 horas al día los 7 días de la semana.

### <a name="how-azure-implements-threat-mitigation"></a>Cómo implementa Azure la mitigación de amenazas
Azure tiene controles de seguridad para implementar la mitigación de amenazas y ayudar a los clientes a mitigar posibles amenazas en sus entornos. La lista siguiente resume las funcionalidades de mitigación de amenazas ofrecidas por Azure:

* [Azure Anti-Malware](azure-security-antimalware.md) está habilitado de forma predeterminada en todos los servidores de infraestructura. Opcionalmente, puede habilitarlo en sus propias máquinas virtuales.
* Microsoft mantiene una supervisión continua a través de los servidores, las redes y las aplicaciones para detectar las amenazas y evitar vulnerabilidades de seguridad. Las alertas automatizadas notifican a los administradores acerca de comportamientos anómalos, lo que les permite tomar medidas correctivas contra amenazas internas y externas.
* Puede implementar soluciones de seguridad de terceros dentro de sus suscripciones, como pueden ser los firewalls de aplicaciones web de [Barracuda](https://techlib.barracuda.com/ng54/deployonazure).
* El enfoque de Microsoft para pruebas de penetración incluye "[formación de equipos de Red](http://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)", lo que implica que los profesionales de seguridad de Microsoft atacan sistemas de producción activos (no de clientes) en Azure para probar las defensas contra amenazas persistentes reales y avanzadas.
* Los sistemas de implementación integrados administran la distribución e instalación de revisiones de seguridad en la plataforma Azure.

## <a name="next-steps"></a>Pasos siguientes
[Centro de confianza de Azure](https://azure.microsoft.com/support/trust-center/)

[Blog del equipo de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/)

[Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

[Blog de Active Directory](http://blogs.technet.com/b/ad/)
