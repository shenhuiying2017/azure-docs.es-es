---
title: 'Azure Security y Compliance Blueprint: aplicación web para FedRAMP'
description: 'Azure Security y Compliance Blueprint: aplicación web para FedRAMP'
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: b7a81db6a1caf11ac4a85a5202c5ed943225e849
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941942"
---
# <a name="azure-security-and-compliance-blueprint-web-application-for-fedramp"></a>Azure Security y Compliance Blueprint: aplicación web para FedRAMP

## <a name="overview"></a>Información general

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) es un programa a nivel del gobierno de los Estados Unidos que proporciona un enfoque estandarizado para la evaluación de la seguridad, la concesión de autorizaciones y la supervisión continua de servicios y productos en la nube. Azure Security and Compliance Blueprint Automation proporciona instrucciones para poder implementar un entorno de infraestructura como servicio (IaaS) compatible con FedRAMP y que sea adecuado para una aplicación web simple accesible desde Internet. Esta solución automatiza la implementación y la configuración de recursos de Azure para una arquitectura de referencia común, la cual muestra las formas en que los clientes pueden cumplir los requisitos específicos de seguridad y cumplimiento y sirve como base para que los clientes creen y configuren sus propias soluciones en Azure. La solución implementa un subconjunto de controles de la línea de base de FedRAMP High basada en NIST SP 800-53. Para más información sobre los requisitos de FedRAMP y esta solución, consulte la [documentación de cumplimiento](#compliance-documentation).
> [!NOTE]
> Esta solución se implementa en Azure Government.

Azure Security and Compliance Blueprint Automation implementa automáticamente una arquitectura de referencia de aplicación web de IaaS con controles de seguridad configurados previamente para ayudar a los clientes a cumplir con los requisitos de FedRAMP. La solución consta de plantillas de Azure Resource Manager y scripts de PowerShell que guían la configuración y la implementación de recursos.

Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Implementar una aplicación sin modificar en este entorno no es suficiente para satisfacer por completo los requisitos de la línea de base de FedRAMP High. Tenga en cuenta lo siguiente:
- Esta arquitectura fundamental proporciona una línea de base para que los clientes puedan usar Azure de forma que sea compatible con FedRAMP.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

Para obtener información general de cómo funciona esta solución, vea este [vídeo](https://aka.ms/fedrampblueprintvideo) en el que se explica y demuestra cómo realizar la implementación.

Haga clic [aquí](https://aka.ms/fedrampblueprintrepo) para ver las instrucciones de implementación.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura
Esta solución implementa una arquitectura de referencia para una aplicación web de IaaS con un back-end de SQL Server. La arquitectura incluye un nivel de web, un nivel de datos, infraestructura de Active Directory, Application Gateway y Load Balancer. Las máquinas virtuales implementadas en los niveles de web y de datos se configuran en un conjunto de disponibilidad y las instancias de SQL Server se configuran en un grupo de disponibilidad AlwaysOn de alta disponibilidad. Las máquinas virtuales están unidas a un dominio y las directivas de grupo de Active Directory se usan para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo. Un host de tipo bastión proporciona una conexión segura para que los administradores accedan a los recursos implementados. **Azure recomienda configurar una conexión VPN o de Azure ExpressRoute para la administración y la importación de datos de en la subred de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de aplicación web de IaaS para FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Diagrama de arquitectura de referencia de aplicación web de IaaS para FedRAMP")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

- Azure Virtual Machines
    - (1) host de tipo bastión (Windows Server 2016 Datacenter)
    - (2) Controlador de dominio de Active Directory (Windows Server 2016 Datacenter)
    - (2) nodo de clúster de SQL Server (SQL Server 2017 en Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Conjuntos de disponibilidad
    - (1) Controladores de dominio de Active Directory
    - (1) nodos de clúster de SQL
    - (1) Web/IIS
- Azure Virtual Network
    - (1) /16 redes virtuales
    - (5) /24 subredes
    - Las configuraciones de DNS están establecidas en ambos controladores de dominio
- Azure Load Balancer
- Azure Application Gateway
    - (1) Habilitado para Application Gateway de WAF
        - modo de firewall: prevención
        - conjunto de reglas: OWASP 3.0
        - agente de escucha: puerto 443
- Azure Storage
    - (7) Cuentas de almacenamiento con redundancia geográfica
- Testigo de Azure Cloud
- Almacén de Recovery Services
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Azure Monitor

## <a name="deployment-architecture"></a>Arquitectura de implementación

En la siguiente sección se detallan los elementos de desarrollo e implementación.

**Host de tipo bastión**: el host de tipo bastión es el único punto de entrada que proporciona una conexión segura para que los administradores accedan a los recursos implementados. El NSG del host de tipo bastión solo permite conexiones en el puerto TCP 3389 para RDP. Los clientes pueden seguir configurando el host de tipo bastión para cumplir con los requisitos de protección del sistema de la organización.

### <a name="virtual-network"></a>Red virtual
La arquitectura define una red virtual privada con un espacio de direcciones de 10.200.0.0/16.

**Grupos de seguridad de red**: esta solución implementa los recursos en una arquitectura con una subred web independiente, la subred de la base de datos, la subred de Active Directory y la subred de administración dentro de una red virtual. Las subredes se separan de forma lógica mediante las reglas del grupo de seguridad de red aplicadas a las subredes individuales para restringir el tráfico entre subredes unicamente según sea necesario para la funcionalidad de administración y del sistema.

Consulte la configuración de los [grupos de seguridad de red](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementados con esta solución. Los clientes pueden configurar grupos de seguridad de red mediante la edición del archivo anterior usando [esta documentación](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guía.

Cada una de las subredes tiene un grupo de seguridad de red dedicado (NSG):
- 1 NSG para Application Gateway (LBNSG)
- 1 NSG para host de tipo bastión (MGTNSG)
- 1 NSG para los controladores de dominio principal y de copia de seguridad (ADNSG)
- 1 NSG para servidores SQL Server (SQLNSG)
- 1 NSG para el nivel de web (WEBNSG)

**Subredes**: asegúrese de que cada subred esté asociada a su grupo de seguridad de red correspondiente.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante varias medidas de cifrado.

**Azure Storage**: para cumplir con los requisitos de cifrado de los datos en reposo, todas las cuentas de almacenamiento usan [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server está configurado para usar [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) que realiza el cifrado y descifrado de archivos de datos y de registro en tiempo real para proteger la información en reposo. TDE garantiza que los datos almacenados no hayan estado sujetos a un acceso no autorizado.

Los clientes también pueden configurar las medidas de seguridad de SQL Server siguientes:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   [Auditoría de SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de los eventos de las bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
-   Las [reglas de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   [Detección de amenazas SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-threat-detection-get-started) permite la detección y respuesta ante posibles amenazas a medida que se producen, al proporcionar alertas de seguridad para actividades sospechosas en la base de datos, vulnerabilidades potenciales, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas de Always Encrypted](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos confidenciales nunca aparecen como texto no cifrado dentro del sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
-   El [enmascaramiento de los datos dinámicos de SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-dynamic-data-masking-get-started) puede realizarse después de la implementación de la arquitectura de referencia. Los clientes deberán ajustar la configuración del enmascaramiento de los datos dinámicos para ajustarse al esquema de la base de datos.

**Azure Disk Encryption**: Azure Disk Encryption se usa para los discos cifrados de máquinas virtuales de IaaS de Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos y sistema operativo. La solución se integra con Azure Key Vault para ayudarle a controlar y administrar las claves de cifrado del disco.

### <a name="identity-management"></a>Administración de identidades

Las siguientes tecnologías proporcionan funcionalidades de administración de identidad en el entorno de Azure:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft.
- La autenticación para una aplicación web implementada por el cliente puede realizarse mediante Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- El [control de acceso basado en rol de Azure (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permite administrar al detalle el control de acceso de Azure. El acceso a la suscripción se limita al administrador de la suscripción y el acceso a los recursos se puede limitar según el rol de usuario.
- Una instancia implementada de Active Directory de IaaS proporciona administración de identidades en el nivel de sistema operativo para máquinas virtuales implementadas de IaaS.

### <a name="security"></a>Seguridad
**Administración de secretos**: la solución usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Azure Key Vault ayuda a administrar los secretos y las claves de cifrado del disco de la máquina virtual de IaaS para esta arquitectura de referencia.

**Administración de revisiones**: las máquinas virtuales Windows que implementó esta instancia de Azure Security and Compliance Blueprint Automation se configuran de forma predeterminada para recibir actualizaciones automáticas del servicio Windows Update. Esta solución también implementa la solución Azure Automation mediante la cual se pueden crear implementaciones de actualizaciones para implementar revisiones en servidores Windows cuando sea necesario.

**Protección contra malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Virtual Machines proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.

**Application Gateway**: la arquitectura reduce el riesgo de sufrir vulnerabilidades de seguridad, ya que usa Application Gateway con el firewall de aplicaciones web (WAF) y tiene el conjunto de reglas OWASP habilitado. Entre estas funcionalidades, cabe destacar:

- [SSL de un extremo a otro](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Opción para habilitar la [descarga SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Opción para deshabilitar [TLS v1.0 y v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicaciones web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevención](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con el conjunto de reglas OWASP 3.0

### <a name="business-continuity"></a>Continuidad del negocio

**Alta disponibilidad**: durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual está disponible para cumplir el 99,95 % del Acuerdo de Nivel de Servicio de Azure. La solución implementa todas las máquinas virtuales del nivel de web y la capa de datos de un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Los conjuntos de disponibilidad garantizan que las máquinas virtuales se distribuyan entre varios clústeres de hardware aislados para mejorar la disponibilidad. Además, esta solución implementa las máquinas virtuales de SQL Server en un conjunto de disponibilidad como un [grupo de disponibilidad AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). La función de grupo de disponibilidad AlwaysOn proporciona funcionalidades de alta disponibilidad y recuperación ante desastres.

**Almacén de Recovery Services**: el [almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) aloja datos de copia de seguridad y protege todas las configuraciones de Azure Virtual machines en esta arquitectura. Con los almacenes de Recovery Services, los clientes pueden restaurar archivos y carpetas desde una máquina virtual de IaaS sin tener que restaurar toda la máquina virtual, lo que permite unos tiempos de restauración más rápidos.

**Testigo en la nube**: el [testigo en la nube](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) es un tipo de testigo del cuórum de clúster de conmutación por error en Windows Server 2016 que usa Azure como el punto de arbitraje. El testigo en la nube, como cualquier otro testigo del cuórum, tiene voto y puede participar en los cálculos del cuórum, pero usa Azure Blob Storage estándar públicamente disponible. Con esto se elimina la sobrecarga de mantenimiento adicional de las máquinas virtuales hospedadas en una nube pública.

### <a name="logging-and-auditing"></a>Registro y auditoría

OMS proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.

- **Registros de actividad:** los [registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) son todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Storage, registros de auditoría de Key Vault, y registros de firewall y acceso a Application Gateway.
- **Archivado de registros**: todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para su archivado. El usuario puede configurar la retención hasta 730 días para cumplir los requisitos de retención específicos de una organización. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se instalan las siguientes soluciones de OMS. Tenga en cuenta que es responsabilidad del cliente configurar estas soluciones para alinearlas con los controles de seguridad de FedRAMP:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución Antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): solución Azure Automation almacena, ejecuta y administra runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel de Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, que incluye el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) ayuda a los usuarios a realizar un seguimiento del rendimiento, mantener la seguridad e identificar tendencias permitiendo a las organizaciones auditar, crear alertas y archivar datos, incluido el seguimiento de las llamadas a la API en los recursos Azure de los clientes.

## <a name="threat-model"></a>Modelo de amenazas
El diagrama de flujo de datos de esta arquitectura de referencia está disponible para [descarga](https://aka.ms/fedrampWAdfd) y se encuentra a continuación. El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Modelo de amenazas de la aplicación web de IaaS para FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "Modelo de amenazas de la aplicación web de IaaS para FedRAMP")

## <a name="compliance-documentation"></a>Documentación de cumplimiento

En la [matriz de responsabilidades de los clientes de FedRAMP High de Azure Security and Compliance Blueprint](https://aka.ms/blueprinthighcrm) se enumeran todos los controles de seguridad necesarios para la línea de base de FedRAMP High. La matriz denota si la implementación de cada control es la responsabilidad de Microsoft, del cliente o de ambos.

En la [matriz de implementación de alto control de la aplicación web de IaaS para FedRAMP de Azure Security y Compliance Blueprint](https://aka.ms/blueprintwacim) se enumeran todos los controles de seguridad necesarios para la línea de base de FedRAMP High. La matriz proporciona información sobre qué controles cubre la arquitectura de aplicación web de IaaS, incluidas descripciones detalladas de cómo la implementación satisface los requisitos de cada control cubierto.

## <a name="deploy-the-solution"></a>Implementación de la solución

Esta instancia de Azure Security and Compliance Blueprint Automation se compone de archivos de configuración de JSON y scripts de PowerShell que se controlan mediante el servicio de API de Azure Resource Manager para implementar recursos en Azure. Puede encontrar instrucciones detalladas acerca de la implementación [aquí](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Esta solución se implementa en Azure Government.

#### <a name="quickstart"></a>Guía de inicio rápido
1. Clone o descargue [este](https://aka.ms/fedrampblueprintrepo) repositorio de GitHub en su estación de trabajo local.

2. Ejecute el script de PowerShell anterior a la implementación: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Haga clic en el botón siguiente, inicie sesión en Azure Portal, escriba los parámetros de plantilla de ARM necesarios y haga clic en **Comprar**.

    [![Implementación en Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute
Debe configurarse un túnel VPN seguro o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de aplicaciones web de IaaS. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como un mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicada entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Están [disponibles](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

## <a name="disclaimer"></a>Renuncia de responsabilidades

- Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.  
- Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.  
- Los clientes pueden copiar y usar este documento con fines internos y de referencia.  
- Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.  
- Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
- Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
