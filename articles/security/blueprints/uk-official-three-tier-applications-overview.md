---
title: 'Azure Blueprint Automation: aplicaciones web de tres niveles para UK-OFFICIAL'
description: 'Azure Blueprint Automation: aplicaciones web de tres niveles para UK-OFFICIAL'
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5f5694367d9be2ae66c7303cfea063b7f4979307
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation-three-tier-web-applications-for-uk-official"></a>Azure Blueprint Automation: aplicaciones web de tres niveles para UK-OFFICIAL

## <a name="overview"></a>Información general

 En este artículo, se proporcionan instrucciones y scripts de automatización para ofrecer una arquitectura basada en web de tres niveles de Microsoft Azure adecuada para controlar varias cargas de trabajo clasificados como OFFICIAL en el Reino Unido.

 Mediante una infraestructura como enfoque de código, el conjunto de plantillas de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) (ARM) implementa un entorno que cumple con los 14 [Cloud Security Principles](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) (Principios de seguridad en la nube) del National Cyber Security Centre (NCSC) del Reino Unido y con los [Critical Security Controls](https://www.cisecurity.org/critical-controls.cfm) (Controles de seguridad crítica) del Center for Internet Security.

 El NCSC recomienda que los clientes usen sus principios de seguridad en la nube para evaluar las propiedades de seguridad del servicio y para comprender mejor la división de responsabilidad entre el cliente y el proveedor. Se proporciona información sobre cada uno de estos principios para ayudarle a comprender la división de responsabilidades.

 Esta arquitectura y las plantillas de ARM correspondientes cumplen con las notas del producto de Microsoft, [Azure Blueprint for the UK Government](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1) (Azure Blueprint para el gobierno del Reino Unido). Este documento cataloga cómo cumplen los servicios de Azure los 14 principios de seguridad en la nube del NCSC del Reino Unido, lo que permite que las organizaciones realicen un seguimiento rápido de su capacidad de cumplir con sus obligaciones de cumplimiento mediante servicios basados en la nube de forma global y en el Reino Unido en Microsoft Azure Cloud.

 Esta plantilla implementa la infraestructura para la carga de trabajo. Deben estar instalados y configurados el código de la aplicación y el software de capa de datos y nivel de empresa compatible. Puede encontrar instrucciones detalladas [aquí](https://aka.ms/ukwebappblueprintrepo).

 Si no tiene una suscripción de Azure, puede registrarse de forma rápida y fácil: [Comience a usar Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura

 Las plantillas de Azure ofrecen una arquitectura de aplicación web de tres niveles en un entorno en la nube de Azure que admite las cargas de trabajo UK-OFFICIAL. La arquitectura proporciona un entorno híbrido seguro que extiende una red local a Azure, permitiendo que los usuarios corporativos o de Internet obtengan acceso a cargas de trabajo basadas en web de forma segura.

![alt text](images/diagram.png?raw=true "Arquitectura de tres niveles UK-OFFICIAL de Azure")

 Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

(1)/16 red virtual: VNET operativa
- (3)/24 subredes: 3 niveles (web, empresa, datos)
- (1)/27 subred: ADDS
- (1)/27 subred: subred de puerta de enlace
- (1)/29 subred: subred de Application Gateway
- Usa el DNS predeterminado (proporcionado por Azure)
- Emparejamiento habilitado para la VNET de administración
- Grupo de seguridad de red (NSG) para administrar el flujo de tráfico

(1)/24 red virtual: VNET de administración
- (1)/27 subred
- Usa (2) entradas de ADDS DNS y (1) entrada de Azure DNS
- Emparejamiento habilitado para la VNET operativa
- Grupo de seguridad de red (NSG) para administrar el flujo de tráfico

(1) Application Gateway
- WAF: habilitado
- Modo de WAF: prevención
- Conjunto de reglas: OWASP 3.0
- Escucha HTTP en el puerto 80
- Conectividad/tráfico regulados a través del NSG
- Punto de conexión de dirección IP pública definido (Azure)

(1) VPN: basada en rutas, túnel VPN de IPSec de sitio a sitio
- Punto de conexión de dirección IP pública definido (Azure)
- Conectividad/tráfico regulados a través del NSG
- (1) puerta de enlace de red local (punto de conexión local)
- (1) puerta de enlace de red de Azure (punto de conexión de Azure)

(9) máquinas virtuales: todas las máquinas virtuales se implementan con la configuración de DSC antimalware de IaaS de Azure

- (2) controladores de dominio de Active Directory Domain Services (Windows Server 2012 R2)
  - (2) roles de servidor DNS: 1 por máquina virtual
  - (2) NIC conectadas a la red virtual operativa: 1 por máquina virtual
  - Los dos están unidos al dominio definido en la plantilla
    - Dominio creado como parte de la implementación


- (1) máquina virtual de administración de Jumpbox (host bastión)
  - 1 NIC en la red virtual de administración con la dirección IP pública
    - El NSG se usa para limitar el tráfico (de entrada y salida) a orígenes determinados
  - Sin unir al dominio


- (2) máquinas virtuales de nivel web
  - (2) roles de servidor de IIS: 1 por máquina virtual
  - (2) NIC conectadas a la red virtual operativa: 1 por máquina virtual
  - Sin unir al dominio


- (2) máquinas virtuales de nivel de empresa
  - (2) NIC conectadas a la red virtual operativa: 1 por máquina virtual
  - Sin unir al dominio


- (2) máquinas virtuales de capa de datos
  - (2) NIC conectadas a la red virtual operativa: 1 por máquina virtual
  - Sin unir al dominio

Conjuntos de disponibilidad
- (1) conjunto de máquinas virtuales de controlador de dominio de Active Directory: 2 máquinas virtuales
- (1) conjunto de máquinas virtuales de nivel web: 2 máquinas virtuales
- (1) conjunto de máquinas virtuales de nivel de empresa: 2 máquinas virtuales
- (1) conjunto de máquinas virtuales de capa de datos: 2 máquinas virtuales

Load Balancer
- (1) equilibrador de carga de nivel web
- (1) equilibrador de carga de nivel de empresa
- (1) equilibrador de carga de capa de datos

Storage
- (14) cuentas de Azure Storage en total
  - Conjunto de disponibilidad del controlador de dominio de Active Directory
    - (2) cuentas principales de almacenamiento con redundancia local (LRS): 1 para cada máquina virtual  
    - (1) cuenta de almacenamiento con redundancia local (LRS) de diagnóstico para el conjunto de disponibilidad de ADDS
  - Máquina virtual de Jumpbox de administración
    - (1) cuenta principal de almacenamiento con redundancia local (LRS) para la máquina virtual de Jumpbox
    - (1) cuenta de almacenamiento con redundancia local (LRS) de diagnóstico para la máquina virtual de Jumpbox
  - Máquinas virtuales de nivel web
    - (2) cuentas principales de almacenamiento con redundancia local (LRS): 1 para cada máquina virtual  
    - (1) cuenta de almacenamiento con redundancia local (LRS) de diagnóstico para el conjunto de disponibilidad de nivel web
  - Máquinas virtuales de nivel de empresa
    - (2) cuentas principales de almacenamiento con redundancia local (LRS): 1 para cada máquina virtual  
    - (1) cuenta de almacenamiento con redundancia local (LRS) de diagnóstico para el conjunto de disponibilidad de nivel de empresa
  - Máquinas virtuales de capa de datos
    - (2) cuentas principales de almacenamiento con redundancia local (LRS): 1 para cada máquina virtual  
    - (1) cuenta de almacenamiento con redundancia local (LRS) de diagnóstico para el conjunto de disponibilidad de capa de datos

### <a name="deployment-architecture"></a>Arquitectura de implementación:

**Red local**: una red de área local privada implementada en una organización.

**Red virtual de producción**: la [VNET](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (red virtual) de producción hospeda la aplicación y otros recursos operativos que se ejecutan en Azure. Cada red virtual puede contener varias subredes que se usan para aislar y administrar el tráfico de red.

**Nivel de web**: controla las solicitudes HTTP entrantes. Las respuestas se devuelven a través de este nivel.

**Nivel de empresa**: implementa procesos empresariales y cualquier otra lógica funcional para el sistema.

**Nivel de base de datos**: proporciona almacenamiento de datos persistente, con [grupos de disponibilidad AlwaysOn de SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) para lograr la alta disponibilidad. Los clientes pueden usar [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) como una alternativa de PaaS.

**Puerta de enlace**: [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) proporciona conectividad entre los enrutadores de la red local y la red virtual de producción.

**Puerta de enlace de Internet y dirección IP pública**: la puerta de enlace de Internet expone los servicios de aplicación a los usuarios a través de Internet. El tráfico que obtiene acceso a estos servicios se protege mediante [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), que ofrece funcionalidades de equilibrio de carga y enrutamiento de nivel 7 con protección de firewall de aplicaciones web (WAF).

**Red virtual de administración**: esta [VNET](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) contiene recursos que implementan funcionalidades de administración y supervisión para las cargas de trabajo que se ejecutan en la red virtual de producción.

**Jumpbox**: también denominado [host bastión](https://en.wikipedia.org/wiki/Bastion_host), que es una máquina virtual segura en la red que los administradores usan para conectarse a las máquinas virtuales en la red virtual de producción. El Jumpbox tiene un NSG que solo permite el tráfico remoto que procede de direcciones IP públicas de una lista segura. Para permitir el tráfico de escritorio remoto (RDP), el origen del tráfico debe definirse en el NSG. La administración de recursos de producción se realiza a través del RDP mediante una máquina virtual de Jumpbox segura.

**Rutas definidas por el usuario**: las [rutas definidas por el usuario](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) se usan para definir el flujo de tráfico IP en redes virtuales de Azure.

**Redes virtuales emparejadas en red**: las redes virtuales de producción y administración están conectadas mediante el [emparejamiento de redes virtuales](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Estas redes virtuales se siguen administrando como recursos independientes, pero aparecen como uno con fines de conectividad para estas máquinas virtuales. Estas redes se comunican entre sí directamente mediante direcciones IP privadas. El emparejamiento de redes virtuales está sujeto a que las redes virtuales estén en la misma región de Azure.

**Grupos de seguridad de red**: los [NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contienen listas de control de acceso que permiten o deniegan el tráfico en una red virtual. Los NSG pueden usarse para proteger el tráfico en una subred o a nivel de máquina virtual individual.

**Active Directory Domain Services (AD DS)**: esta arquitectura proporciona una implementación dedicada de [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx).

**Registro y auditoría**: [Azure Activity Log](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) captura las operaciones realizadas en los recursos de su suscripción (por ejemplo, quién ha iniciado la operación, cuándo se ha producido la operación, el estado de la operación y los valores de otras propiedades que pueden ayudarle a investigar la operación). Azure Activity Log es un servicio de la plataforma de Azure que captura todas las acciones de una suscripción. Los registros se pueden archivar o exportar si es necesario.

**Supervisión y alertas de red**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) es un servicio de plataforma que proporciona captura de paquetes de red, registro de flujo, herramientas de topología y diagnósticos para tráficos de red dentro de sus redes virtuales.

## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones

### <a name="business-continuity"></a>Continuidad del negocio

**Alta disponibilidad**: las cargas de trabajo del servidor se agrupan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ayudar a garantizar una alta disponibilidad de las máquinas virtuales de Azure. Esta configuración ayuda a garantizar que, durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual estará disponible y cumplirá el 99,95 % de los contratos de nivel de servicio de Azure.

### <a name="logging-and-audit"></a>Registro y auditoría

**Supervisión**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) es el servicio de plataforma que proporciona un único origen para supervisar el registro de actividad, las métricas y los registros de diagnóstico de todos los recursos de Azure. Azure Monitor se puede configurar para visualizar, consultar, enrutar y archivar las métricas y los registros procedentes de los recursos de Azure, así como tomar medidas relacionadas. Se recomienda usar el control de acceso basado en recursos para proteger el registro de auditoría para ayudar a garantizar que los usuarios no pueden modificar los registros.

**Registros de actividad:** configure los [registros de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) para que proporcionen información sobre las operaciones que se han realizado en los recursos de la suscripción.

**Registros de diagnóstico:** los [registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) son todos los registros emitidos por un recurso. Estos registros pueden incluir los registros del sistema de eventos de Windows y los registros de blobs, tablas y colas.

**Registros de firewall:** Application Gateway proporciona registros de diagnóstico y acceso completos. Los registros de firewall están disponibles para los recursos de puerta de enlace de aplicaciones que tienen WAF habilitado.

**Archivado de registros**: el almacenamiento de datos de registros se puede configurar para escribir en una cuenta de Azure Storage centralizada para archivarse y tener un período de retención definido. Los registros se pueden procesar mediante Azure Log Analytics y sistemas SIEM de terceros.

### <a name="identity"></a>Identidad

**Active Directory Domain Services**: esta arquitectura proporciona una implementación de Active Directory Domain Services en Azure. Si necesita recomendaciones específicas sobre cómo implementar Active Directory en Azure, consulte los siguientes artículos:

[Extending Active Directory Domain Services (AD DS) to Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain) (Extensión de Active Directory Domain Services (AD DS) a Azure).

[Directrices para implementar Windows Server Active Directory en Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integración de Active Directory**: como alternativa a una arquitectura de AD DS dedicada, los clientes pueden preferir usar la integración de [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) o [Active Directory en Azure unido a un bosque local](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Seguridad

**Seguridad de administración**: Azure Blueprint permite a los administradores que se conecten a la red virtual y Jumpbox de administración con el RDP desde un origen de confianza. El tráfico de red para la red virtual de administración se controla mediante NSG. El acceso al puerto 3389 está restringido para el tráfico de un intervalo IP de confianza que puede tener acceso a la subred que contiene el Jumpbox.

Los clientes también puede usar un [modelo administrativo de seguridad mejorada](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) para proteger el entorno al conectarse a la red virtual y Jumpbox de administración. Se recomienda que los clientes de seguridad mejorada usen una [estación de trabajo de acceso con privilegios](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) y la configuración de RDGateway. El uso de aplicaciones virtuales de red y DMZ públicos/privados le ofrece más mejoras de seguridad.

**Protección de la red**: los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) se recomiendan para que cada subred proporcione un segundo nivel de protección contra el tráfico entrante que omite una puerta de enlace configurada incorrectamente o deshabilitada. Ejemplo: [Plantilla de ARM para implementar un NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Protección de puntos de conexión públicos**: la puerta de enlace de Internet expone los servicios de aplicación a los usuarios a través de Internet. El tráfico que obtiene acceso a estos servicios se protege mediante [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), que proporciona una administración de protocolo HTTPS y firewall de aplicaciones web.

**Intervalos IP**: los intervalos IP en la arquitectura son intervalos sugeridos. Se recomienda que los clientes consideren la posibilidad de tener su propio entorno y usar intervalos adecuados.

**Conectividad híbrida**: las cargas de trabajo basadas en la nube están conectadas con el centro de datos local a través de VPN con IPSEC mediante Azure VPN Gateway. Los clientes deben asegurarse de que estén usando una instancia de VPN Gateway adecuada para conectarse a Azure. Ejemplo: [Plantilla de ARM de VPN Gateway](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Es posible que los clientes que ejecuten cargas de trabajo críticas a gran escala con requisitos de macrodatos quieran considerar la opción de usar una arquitectura de red híbrida con [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) para la conectividad de red privada con los Servicios en la nube de Microsoft.

**Separación de intereses**: esta arquitectura de referencia separa las redes virtuales para las operaciones empresariales y de administración. Las redes virtuales y subredes independientes admiten la administración del tráfico, incluidas las restricciones de entrada y salida del tráfico, mediante NSG entre segmentos de red de acuerdo con los procedimientos recomendados de [Servicios en la nube de Microsoft y seguridad de red](https://docs.microsoft.com/azure/best-practices-network-security).

**Administración de recursos**: los recursos de Azure (como las máquinas virtuales, las redes virtuales y los equilibradores de carga) se administran agrupándolos en [grupos de recursos de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). Después, pueden asignarse roles de control de acceso basado en recursos a cada grupo de recursos para restringir el acceso solo a usuarios autorizados.

**Restricciones de control de acceso**: use el [control de acceso basado en rol](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) para administrar los recursos de la aplicación mediante [roles personalizados](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). RBAC puede usarse para restringir las operaciones que DevOps puede realizar en cada nivel. Al conceder permisos, use el [principio de los privilegios mínimos](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Registre todas las operaciones administrativas y realice auditorías periódicas para asegurarse de que los cambios de configuración se habían planeado.

**Acceso a Internet**: esta arquitectura de referencia usa [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) como equilibrador de carga y puerta de enlace accesibles desde Internet. Algunos clientes pueden considerar la opción de usar aplicaciones virtuales de red de terceros para niveles adicionales de seguridad de red como alternativa a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) proporciona una perspectiva central del estado de seguridad de los recursos de la suscripción y ofrece recomendaciones que ayudan a evitar que se realicen ataques a los recursos. También se puede usar para habilitar directivas más pormenorizadas. Por ejemplo, las directivas pueden aplicarse a grupos de recursos determinados, lo que permite a la empresa adaptar su postura con respecto al riesgo. Se recomienda que los clientes habiliten Azure Security Center en su suscripción de Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentación de cumplimiento de los principios de seguridad en la nube del NCSC

Crown Commercial Service (una agencia que trabaja para mejorar la actividad comercial y de adquisición del gobierno) ha renovado la clasificación de los servicios en la nube de Microsoft en el ámbito empresarial a G-Cloud v6, que abarca todas sus ofertas en el nivel OFFICIAL. Se pueden encontrar detalles de Azure y G-Cloud en el [resumen de la evaluación de seguridad de G-Cloud de Reino Unido de Azure](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Esta solución UK-OFFICIAL de Azure Blueprint cumple con los 14 principios de seguridad en la nube que se documentan en los [Cloud Security Principles](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) (Principios de seguridad en la nube) para ayudar a garantizar un entorno que admite cargas de trabajo clasificadas como UK-OFFICIAL.

La [Customer Responsibility Matrix](https://aka.ms/blueprintuk-gcrm) (matriz de responsabilidades del cliente, libro de Excel) enumera los 14 principios de seguridad en la nube e indica, para cada principio (o subsección de principio), si la implementación del principio es responsabilidad de Microsoft, del cliente o compartida entre los dos.

La [Principle Implementation Matrix](https://aka.ms/ukwebappblueprintpim) (matriz de implementación de principios, libro de Excel) enumera los 14 principios de seguridad en la nube e indica, para cada principio (o subsección de principio), que se designa una responsabilidad del cliente en la matriz de responsabilidades del cliente, 1) si Azure Blueprint Automation implementa el principio, y 2) una descripción de cómo cumple la implementación con los requisitos del principio. Este contenido también está disponible [aquí](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Además, Cloud Security Alliance (CSA) ha publicado la Cloud Control Matrix (matriz de control en la nube) para ayudar a los clientes en la evaluación de los proveedores de nube y para identificar las preguntas que deben responder antes de pasar a servicios en la nube. En respuesta, Microsoft Azure ha contestado el cuestionario CSA Consensus Assessment Initiative Questionnaire ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), que describe cómo Microsoft aborda los principios sugeridos.

## <a name="deploy-the-solution"></a>Implementación de la solución

Hay dos métodos que los usuarios de la implementación pueden usar para implementar esta solución de Azure Blueprint. El primero usa un script de PowerShell, mientras que el segundo usa Azure Portal para implementar la arquitectura de referencia. Puede encontrar instrucciones detalladas [aquí](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
