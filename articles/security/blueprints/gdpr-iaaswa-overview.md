---
title: 'Plano técnico de seguridad y cumplimiento de Azure: aplicación web IaaS para RGPD'
description: 'Plano técnico de seguridad y cumplimiento de Azure: aplicación web IaaS para RGPD'
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c418664fe94ee2801a24df59b9ef3451f3985cdb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161858"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Plano técnico de seguridad y cumplimiento de Azure: aplicación web IaaS para RGPD

## <a name="overview"></a>Información general
El Reglamento general de protección de datos (RGPD) contiene muchos requisitos sobre la recopilación, almacenamiento y uso de información personal, incluida la forma en que las organizaciones identifican y protegen los datos personales, se adaptan a los requisitos de transparencia, detectan e informan de las infracciones de datos personales, y ofrecen cursos sobre la privacidad al personal y a otros empleados. RGPD ofrece un mayor control sobre los datos personales e impone muchas nuevas obligaciones a las organizaciones que recopilan, controlan o analizan datos personales. El RGPD impone nuevas reglas sobre las organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes de la UE. El RGPD se aplica con independencia de la ubicación de la organización.

Microsoft ha diseñado Azure con medidas de seguridad y directivas de privacidad líderes del sector para proteger los datos en la nube, incluidas las categorías de datos personales identificadas por el RGPD. Los [términos contractuales](http://aka.ms/Online-Services-Terms) de Microsoft comprometen a Microsoft con los requisitos de los procesadores.

El plano técnico de seguridad y cumplimiento de Azure proporciona instrucciones para implementar un entorno de infraestructura como servicio (IaaS) adecuado para una aplicación web simple accesible desde Internet. Esta solución muestra las formas en que los clientes pueden cumplir con los requisitos específicos de seguridad y cumplimiento del RGPD y sirve como base para que los clientes creen y configuren sus propias soluciones de aplicaciones web IaaS en Azure. Los clientes pueden utilizar esta arquitectura de referencia y seguir el [proceso de cuatro pasos](https://aka.ms/gdprebook) de Microsoft en su recorrido hacia el cumplimiento del RGPD:
1. Descubrir: identificar qué datos personales existen y dónde se encuentran.
2. Administrar: controlar cómo se utilizan los datos personales y cómo se accede a ellos.
3. Proteger: establecer controles de seguridad para prevenir, detectar y responder a vulnerabilidades e infracciones de datos.
4. Informar: guardar la documentación necesaria y administrar las solicitudes de datos y las notificaciones de infracciones.

Esta arquitectura de referencia, las guías de implementación asociadas y el modelo de amenazas están diseñados como base para que los clientes puedan adaptarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Tenga en cuenta lo siguiente:
- Esta arquitectura proporciona una línea de base para ayudar a los clientes a implementar cargas de trabajo en Azure de manera compatible con RGPD.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura
Esta solución implementa una arquitectura de referencia para una aplicación web de IaaS con un back-end de SQL Server. La arquitectura incluye un nivel de web, un nivel de datos, infraestructura de Active Directory, Application Gateway y Load Balancer. Las máquinas virtuales implementadas en los niveles de web y de datos se configuran en un conjunto de disponibilidad y las instancias de SQL Server se configuran en un grupo de disponibilidad AlwaysOn de alta disponibilidad. Las máquinas virtuales están unidas a un dominio y las directivas de grupo de Active Directory se usan para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo. Un host de tipo bastión de administración proporciona una conexión segura para que los administradores accedan a los recursos implementados. **Azure recomienda configurar una conexión VPN o de ExpressRoute para la administración y la importación de datos en la subred de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de aplicaciones web de IaaS para RGPD](images/gdpr-iaaswa-architecture.png?raw=true "IaaS Web Applicaiton for GDPR reference architecture diagram")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

- Azure Virtual Machines
    - (1) administración/bastión (Windows Server 2016 Datacenter)
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
- Testigo de Cloud
- Almacén de Recovery Services
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Operations Management Suite (OMS)
- Azure Security Center

## <a name="deployment-architecture"></a>Arquitectura de implementación
En la siguiente sección se detallan los elementos de desarrollo e implementación.

**Host de tipo bastión**: el host de tipo bastión es el único punto de entrada que permite a los usuarios acceder a los recursos implementados en este entorno. El host de tipo bastión proporciona una conexión segura a los recursos implementados al permitir solo el tráfico remoto desde las direcciones IP públicas de una lista segura. Para permitir el tráfico de escritorio remoto (RDP), el origen del tráfico debe definirse en el grupo de seguridad de red (NSG).

Esta solución crea una máquina virtual como host de tipo bastión unido mediante dominio con las siguientes configuraciones:
-   [Extensión antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensión de OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensión de Diagnósticos de Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) con Azure Key Vault
-   Una [directiva de apagado automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reducir el consumo de recursos de la máquina virtual cuando no esté en uso.
-   [Credential Guard de Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que las credenciales y otros secretos se ejecuten en un entorno protegido aislado del sistema operativo en ejecución

### <a name="virtual-network"></a>Red virtual
La arquitectura define una red virtual privada con un espacio de direcciones de 10.200.0.0/16.

**Grupos de seguridad de red**: esta solución implementa los recursos en una arquitectura con una subred web independiente, la subred de la base de datos, la subred de Active Directory y la subred de administración dentro de una red virtual. Las subredes se separan de forma lógica mediante las reglas del grupo de seguridad de red aplicadas a las subredes individuales para restringir el tráfico entre subredes unicamente según sea necesario para la funcionalidad de administración y del sistema.

Vea la configuración de los [grupos de seguridad de red](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementados con esta solución. Las organizaciones pueden configurar grupos de seguridad de red mediante la edición del archivo anterior usando [esta documentación](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guía.

Cada una de las subredes tiene un grupo de seguridad de red dedicado (NSG):
- 1 NSG para Application Gateway (LBNSG)
- 1 NSG para host de tipo bastión (MGTNSG)
- 1 NSG para los controladores de dominio principal y de copia de seguridad (ADNSG)
- 1 NSG para servidores SQL Server y el testigo de Cloud (SQLNSG)
- 1 NSG para el nivel de web (WEBNSG)

### <a name="data-in-transit"></a>Datos en tránsito
De manera predeterminada, Azure cifra todas las comunicaciones hacia y desde los centros de datos de Azure. Además, todas las transacciones a Azure Storage a través de Azure Portal se realizan mediante HTTPS.

### <a name="data-at-rest"></a>Datos en reposo
La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Azure Storage**: para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esto ayuda a proteger los datos personales en cumplimiento de los compromisos de seguridad de la organización y los requisitos de cumplimiento definidos por el RGPD.

**Azure Disk Encryption**: Azure Disk Encryption se usa para los discos cifrados de máquinas virtuales de IaaS de Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos y sistema operativo. La solución se integra con Azure Key Vault para ayudarle a controlar y administrar las claves de cifrado del disco.

**SQL Server**: la instancia de SQL Server usa las siguientes medidas de seguridad de base de datos:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   [Auditoría de Base de datos SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
-   SQL Database está configurado para usar el [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado de la base de datos, de las copias de seguridad asociadas y de archivos de registro de transacciones para proteger la información en reposo. TDE garantiza que los datos personales almacenados no hayan estado sujetos a un acceso no autorizado.
-   Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden todo acceso a los servidores de base de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   [Detección de amenazas SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite la detección y respuesta ante posibles amenazas a medida que se producen, ya que proporciona alertas de seguridad para actividades sospechosas en la base de datos, vulnerabilidades potenciales, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas de Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos personales confidenciales nunca van a aparecer como texto no cifrado dentro del sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
- La característica [Propiedades extendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) puede utilizarse para interrumpir el procesamiento de los titulares de los datos, ya que permite a los usuarios agregar propiedades personalizadas a los objetos de la base de datos y etiquetar los datos como "interrumpidos" para admitir la lógica de la aplicación y evitar el procesamiento de los datos personales asociados.
- La [seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite a los usuarios definir directivas para restringir el acceso a los datos a fin de interrumpir su procesamiento.
- [Enmascaramiento dinámico de datos (DDM) de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de los datos personales al enmascarar los datos para usuarios o aplicaciones sin privilegios. DDM puede detectar datos potencialmente confidenciales y sugerir las máscaras adecuadas que se pueden aplicar. Esto ayuda a identificar los datos personales que cumplen los requisitos para la protección del RGPD y a reducir el acceso de tal manera que no salga de la base de datos mediante un acceso no autorizado. **Nota: Los clientes deberán ajustar la configuración de DDM para ajustarse al esquema de la base de datos.**

### <a name="identity-management"></a>Administración de identidades
Las siguientes tecnologías proporcionan funcionalidades de administración del acceso a datos personales en el entorno de Azure.
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios para esta solución se crean en AAD, incluidos los usuarios que acceden a SQL Server.
-   La autenticación para acceder a la aplicación se realiza con AAD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos usa AAD para autenticar la aplicación en SQL Server. Para más información, consulte cómo [proteger los datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [El control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite a los administradores definir permisos de acceso bien definidos para conceder solo el nivel de acceso que los usuarios necesitan para realizar su trabajo. En lugar de dar a cada usuario permisos ilimitados para los recursos de Azure, los administradores pueden permitir solo ciertas acciones para acceder a los datos personales. El acceso de suscripción se limita al administrador de esta.
- [AAD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite a los clientes minimizar el número de usuarios con acceso a determinados recursos.  Los administradores pueden usar AAD Privileged Identity Management para detectar, restringir y supervisar las identidades con privilegios y su acceso a los recursos. Esta funcionalidad también puede utilizarse para aplicar un acceso administrativo a petición, Just-in-Time, cuando sea necesario.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.
- Una instancia implementada de Active Directory de IaaS proporciona administración de identidades en el nivel de sistema operativo para máquinas virtuales implementadas de IaaS.

### <a name="security"></a>Seguridad
**Administración de secretos**: la solución utiliza [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Las siguientes funcionalidades de Azure Key Vault ayudan a los clientes a proteger los datos personales y el acceso a dichos datos:
- Se configuran directivas de acceso avanzadas según las necesidades.
- Se definen directivas de acceso a Key Vault con los permisos mínimos requeridos para las claves y los secretos.
- Todas las claves y los secretos en Key Vault tienen fechas de expiración.
- Todas las claves de Key Vault están protegidas por módulos de seguridad de hardware especializados (HSM). El tipo de clave es una clave RSA de 2048 bits protegida por HSM.
- Se otorgan los permisos necesarios mínimos a todos los usuarios e identidades que utilizan RBAC.
- Los registros de diagnóstico de Key Vault están habilitados con un período de retención de al menos 365 días.
- Las operaciones criptográficas permitidas para las claves están restringidas únicamente a las requeridas
- La solución se integra con Azure Key Vault para administrar los secretos y las claves de cifrado del disco de la máquina virtual de IaaS.

**Administración de revisiones**: las máquinas virtuales Windows implementadas como parte de esta arquitectura de referencia se configuran de forma predeterminada para recibir actualizaciones automáticas desde Windows Update Service. Esta solución también incluye el servicio [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) de OMS, a través del cual se pueden crear implementaciones actualizadas para aplicar revisiones a las máquinas virtuales cuando sea necesario.

**Protección contra malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Virtual Machines proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.

**Alertas de seguridad**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) permite a los clientes supervisar el tráfico, recopilar registros y analizar orígenes de datos en busca de amenazas. Además, Azure Security Center accede a la configuración existente de los servicios de Azure para proporcionar recomendaciones de configuración y servicio que ayuden a mejorar la postura de seguridad y a proteger los datos personales. Azure Security Center incluye un [informe de inteligencia de amenazas](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) por cada amenaza detectada para ayudar a los equipos de respuesta a incidentes a investigar y corregir las amenazas.

**Application Gateway**: la arquitectura reduce el riesgo de sufrir vulnerabilidades de seguridad, ya que usa Application Gateway con el firewall de aplicaciones web (WAF) y tiene el conjunto de reglas OWASP habilitado. Entre estas funcionalidades, cabe destacar:

- [SSL de un extremo a otro](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Opción para habilitar la [descarga SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Opción para deshabilitar [TLS v1.0 y v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicaciones web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevención](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con el conjunto de reglas OWASP 3.0
- Opción para habilitar el [registro de diagnósticos](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Sondeos de estado personalizados](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) y [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) que proporcionan protección adicional y notificaciones. Asimismo, Azure Security Center también proporciona un sistema de reputación.

### <a name="business-continuity"></a>Continuidad del negocio

**Alta disponibilidad**: la solución implementa todas las máquinas virtuales en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Los conjuntos de disponibilidad garantizan que las máquinas virtuales se distribuyan entre varios clústeres de hardware aislados para mejorar la disponibilidad. Durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual está disponible para cumplir el 99,95 % del Acuerdo de Nivel de Servicio de Azure.

**Almacén de Recovery Services**: el [almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) aloja datos de copia de seguridad y protege todas las configuraciones de Azure Virtual machines en esta arquitectura. Con los almacenes de Recovery Services, los clientes pueden restaurar archivos y carpetas desde una VM de IaaS sin tener que restaurar toda la VM, lo que permite unos tiempos de restauración más rápidos.

**Testigo en la nube**: el [testigo en la nube](https://docs.microsoft.com/en-us/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) es un tipo de testigo del cuórum de clúster de conmutación por error en Windows Server 2016 que usa Azure como punto de arbitraje. El testigo en la nube, como cualquier otro testigo del cuórum, tiene voto y puede participar en los cálculos del cuórum, pero usa Azure Blob Storage estándar públicamente disponible. Con esto, se elimina la sobrecarga de mantenimiento adicional de las VM hospedadas en una nube pública.

### <a name="logging-and-auditing"></a>Registro y auditoría

OMS proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.
- **Registros de actividad:** [los registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Storage, registros de auditoría de Key Vault, y registros de firewall y acceso a Application Gateway.
- **Archivado de registros**: todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para su archivado. El usuario puede configurar la retención hasta 730 días para cumplir los requisitos de retención específicos de una organización. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se incluyen las siguientes soluciones de OMS:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución Antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la solución Azure Automation almacena, ejecuta y administra runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel de Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, incluido el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure para un cliente.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos (DFD) de esta arquitectura de referencia está disponible para [descarga](https://aka.ms/gdprIaaSdfd) y se encuentra a continuación. El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Modelo de amenazas de la aplicación web de IaaS para RGPD](images/gdpr-iaaswa-threat-model.png?raw=true "IaaS Web Applicaiton for GDPR threat model")

## <a name="compliance-documentation"></a>Documentación de cumplimiento

El [Plano técnico de seguridad y cumplimiento de Azure: matriz de responsabilidades de clientes de RGPD](https://aka.ms/gdprCRM) enumera las responsabilidades de controlador y de procesador para todos los artículos del RGPD. Tenga en cuenta que, para los servicios de Azure, el cliente suele ser el controlador y Microsoft actúa como procesador.

El [plano técnico de seguridad y cumplimiento de Azure: matriz de implementación de aplicaciones web de IaaS de RGPD](https://aka.ms/gdprIaaSweb) proporciona información sobre qué artículos de RGPD aborda la arquitectura de aplicaciones web de IaaS, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada artículo cubierto.

## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute
Debe configurarse un túnel VPN seguro o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de aplicaciones web de IaaS. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicada entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Hay [disponibles](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

## <a name="disclaimer"></a>Renuncia de responsabilidades

- Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.  
- Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.  
- Los clientes pueden copiar y usar este documento con fines internos y de referencia.  
- Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.  
- Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
- Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
