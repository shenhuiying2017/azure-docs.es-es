---
title: 'Plano técnico de seguridad y cumplimiento de Azure: aplicación web de PaaS para RGPD'
description: 'Plano técnico de seguridad y cumplimiento de Azure: aplicación web de PaaS para RGPD'
services: security
author: jomolesk
ms.assetid: 229759a1-f984-4985-a3fd-3719a7d1c8ff
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: c338fed118e330280824754277a2fc31a1eaa7ba
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161878"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-gdpr"></a>Plano técnico de seguridad y cumplimiento de Azure: aplicación web de PaaS para RGPD

## <a name="overview"></a>Información general
El Reglamento general de protección de datos (RGPD) contiene muchos requisitos sobre la recopilación, almacenamiento y uso de información personal, incluida la forma en que las organizaciones identifican y protegen los datos personales, se adaptan a los requisitos de transparencia, detectan e informan de las infracciones de datos personales, y forman sobre la privacidad al personal y a otros empleados. El RGPD ofrece un mayor control sobre los datos personales e impone muchas nuevas obligaciones a las organizaciones que recopilan, controlan o analizan datos personales. Este Reglamento impone nuevas reglas sobre las organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes en la UE. El RGPD se aplica con independencia de la ubicación de la organización.

Microsoft ha diseñado Azure con medidas de seguridad y directivas de privacidad líderes del sector para proteger los datos en la nube, incluidas las categorías de datos personales identificadas por el RGPD. Los [términos contractuales](http://aka.ms/Online-Services-Terms) de Microsoft comprometen a Microsoft con los requisitos de los procesadores.

El plano técnico de seguridad y cumplimiento de Azure proporciona instrucciones para implementar un entorno de plataforma como servicio (PaaS) adecuado para una aplicación web simple accesible desde Internet. Esta solución muestra las formas en que los clientes pueden cumplir con los requisitos específicos de seguridad y cumplimiento del RGPD y sirve como base para que los clientes creen y configuren sus propias soluciones de aplicaciones web PaaS en Azure. Los clientes pueden utilizar esta arquitectura de referencia y seguir el [proceso de cuatro pasos](https://aka.ms/gdprebook) de Microsoft en su recorrido al cumplimiento del RGPD:
1. Descubrir: Identificar qué datos personales existen y dónde residen.
2. Administrar: Controlar cómo se utilizan los datos personales y cómo se accede a ellos.
3. Proteger: Establecer controles de seguridad para prevenir, detectar y responder a puntos vulnerables e infracciones de datos.
4. Informar: Guardar la documentación necesaria y administrar las solicitudes de datos y las notificaciones de infracciones.

Esta arquitectura de referencia, las guías de implementación asociadas y los modelos de amenazas están diseñados como base para que los clientes puedan adaptarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Tenga en cuenta lo siguiente:
- Esta arquitectura proporciona una línea de base para ayudar a los clientes a implementar cargas de trabajo en Azure de manera compatible con el Reglamento.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura
Esta solución proporciona una arquitectura de referencia para una aplicación web de PaaS con un back-end de Azure SQL Database. La aplicación web está hospedada en una instancia de Azure App Service Environment aislada, que es un entorno privado y dedicado en un centro de datos de Azure. La carga del entorno equilibra el tráfico para la aplicación web en máquinas virtuales administradas por Azure. Esta arquitectura también incluye grupos de seguridad de red, una instancia de Application Gateway, Azure DNS y Load Balancer. Además, Application Insights proporciona administración de rendimiento de aplicaciones en tiempo real, así como análisis mediante Operations Management Suite (OMS). **Azure recomienda configurar una conexión VPN o de ExpressRoute para la administración y la importación de datos de en la subred de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de aplicaciones web de PaaS para RGPD](images/gdpr-paaswa-architecture.png?raw=true "PaaS Web Applicaiton for GDPR reference architecture diagram")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

- Azure Active Directory (AAD)
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Habilitado para Application Gateway de WAF
        - modo de firewall: prevención
        - conjunto de reglas: OWASP 3.0
        - agente de escucha: puerto 443
- red virtual de Azure
- grupos de seguridad de red
- Azure DNS
- Azure Storage
- Operations Management Suite (OMS)
- Azure Monitor
- Application Insights
- Azure Security Center
- App Service Environment v2
- Azure Load Balancer
- Aplicación web de Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Arquitectura de implementación
En la siguiente sección se detallan los elementos de desarrollo e implementación.

**Azure Resource Manager**: [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) permite a los clientes trabajar con los recursos de la solución como grupo. Los clientes pueden implementar, actualizar o eliminar todos los recursos de la solución en una sola operación coordinada. Para la implementación los clientes utilizan una plantilla, y esta plantilla puede funcionar en diferentes entornos, como pruebas, ensayo y producción. Resource Manager proporciona características de seguridad, auditoría y etiquetado que ayudan a los clientes a administrar los recursos después de la implementación

**App Service Environment v2**: [Azure App Service Environment](https://docs.microsoft.com/en-us/azure/app-service/environment/intro) es una característica de App Service que proporciona un entorno completamente aislado y dedicado para ejecutar de forma segura las aplicaciones de App Service a gran escala.

Las instancias de ASE están aisladas para ejecutar únicamente las aplicaciones de un solo cliente y siempre se implementan en una red virtual. Los clientes tienen un mayor control sobre el tráfico de red entrante y saliente de las aplicaciones, y las aplicaciones pueden establecer conexiones seguras de alta velocidad a los recursos corporativos locales a través de redes virtuales.

Puede usar las instancias de ASE de esta arquitectura en los siguientes controles o configuraciones:

- Un host dentro de una instancia protegida de Azure Virtual Network y reglas de seguridad de red.
- Una instancia de ASE configurada con certificados ILB autofirmados para la comunicación HTTPS.
- En un [modo de equilibrio de carga interno](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3).
- Deshabilitación de [TLS 1.0](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Al cambiar el [cifrado TLS](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Al controlar [puertos N/W de tráfico entrante](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- En [WAF: al restringir los datos](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Al permitir el [tráfico de Azure SQL Database](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web App**: [Azure Web Apps](https://docs.microsoft.com/en-us/azure/app-service/) permite a los clientes crear y hospedar aplicaciones web en el lenguaje de programación que prefiera sin tener que administrar la infraestructura. Ofrece escalado automático y alta disponibilidad, es compatible con Windows y Linux, y permite implementaciones automatizadas desde GitHub, Visual Studio Team Services o cualquier repositorio Git.

### <a name="virtual-network"></a>Virtual Network
La arquitectura define una red virtual privada con un espacio de direcciones de 10.200.0.0/16.

**Grupos de seguridad de red**: los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contienen listas de control de acceso (ACL) que permiten o deniegan el tráfico en una red virtual. Los NSG pueden usarse para proteger el tráfico en una subred o a nivel de máquina virtual individual. Los grupos de seguridad de red siguientes ya existen:
- Un grupo de seguridad de red para Application Gateway
- Un grupo de seguridad de red para App Service Environment
- Un grupo de seguridad de red para Azure SQL Database

Cada uno de los grupos de seguridad de red tiene puertos y protocolos específicos abiertos para que la solución pueda funcionar de forma segura y correcta. Además, las siguientes opciones de configuración están habilitadas para cada NSG:
  - Los [eventos y registros de diagnóstico](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log) están habilitados y se almacenan en la cuenta de almacenamiento.
  - Log Analytics de OMS está conectado a los [diagnósticos del grupo de seguridad de red](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subredes**: asegúrese de que cada subred esté asociada a su grupo de seguridad de red correspondiente.

**Azure DNS**: el Sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP. [Azure DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview) es un servicio de hospedaje para dominios DNS que ofrece resolución de nombres mediante la infraestructura de Azure. Al hospedar dominios en Azure, los usuarios pueden administrar los registros DNS con las mismas credenciales, API, herramientas y facturación que con los demás servicios de Azure. Azure DNS es compatible con dominios DNS privados.

**Azure Load Balancer**: [Azure Load Balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) permite a los clientes escalar las aplicaciones y crear alta disponibilidad para sus servicios. Load Balancer admite escenarios de entrada y de salida, proporciona baja latencia y alta capacidad de proceso, y puede escalar hasta millones de flujos para todas las aplicaciones TCP y UDP.

### <a name="data-in-transit"></a>Datos en tránsito
De manera predeterminada, Azure cifra todas las comunicaciones hacia y desde los centros de datos de Azure. Todas las transacciones a Azure Storage mediante Azure Portal se realizan mediante HTTPS.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Azure Storage**: para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esto ayuda a proteger los datos personales en apoyo de los compromisos de seguridad de la organización y los requisitos de cumplimiento definidos por RGPD.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos. La solución se integra con Azure Key Vault para ayudar a controlar y administrar las claves de cifrado del disco.

**Azure SQL Database**: la instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   La [auditoría de bases de datos SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
-   Azure SQL Database está configurado para usar el [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado de archivos de datos y de registro en tiempo real de la base de datos, de las copias de seguridad asociadas y de archivos de registro de transacciones para proteger la información en reposo. TDE garantiza que los datos personales almacenados no hayan estado sujetos a un acceso no autorizado.
-   Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   La [detección de amenazas de SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar y responder ante posibles amenazas a medida que se producen, mediante el envío de alertas de seguridad para actividades sospechosas en la base de datos, posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas de Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos personales confidenciales nunca van a aparecer como texto no cifrado dentro del sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
- Las [propiedades extendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) pueden utilizarse para interrumpir el procesamiento de los titulares de los datos, ya que permiten a los usuarios agregar propiedades personalizadas a los objetos de la base de datos y etiquetar los datos como "interrumpidos" para admitir la lógica de la aplicación y evitar el procesamiento de los datos personales asociados.
- La [seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite a los usuarios definir directivas para restringir el acceso a los datos a fin de interrumpir su procesamiento.
- [Enmascaramiento dinámico de datos (DDM) de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de los datos personales al enmascarar los datos a aquellos usuarios o aplicaciones sin privilegios. DDM puede detectar datos potencialmente confidenciales y sugerir las máscaras adecuadas que se pueden aplicar. Esto ayuda a identificar los datos personales que cumplen los requisitos para la protección del RGPD y a reducir el acceso de tal manera que no salga de la base de datos mediante un acceso no autorizado. **Nota: Los clientes deberán ajustar la configuración de DDM para ajustarse al esquema de la base de datos.**

### <a name="identity-management"></a>Administración de identidades
Las siguientes tecnologías proporcionan funcionalidades de administración del acceso a datos personales en el entorno de Azure.
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios para esta solución se crean en AAD, incluidos los usuarios que acceden a la base de datos de Azure SQL Database.
-   La autenticación para acceder a la aplicación se realiza con AAD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos usa AAD para autenticar la aplicación Azure SQL Database. Para más información, consulte cómo [proteger los datos confidenciales en Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [El control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite a los administradores definir permisos de acceso bien definidos para conceder solo el nivel de acceso que los usuarios necesitan para realizar su trabajo. En lugar de dar a cada usuario permisos ilimitados para los recursos de Azure, los administradores pueden permitir solo ciertas acciones para acceder a los datos personales. El acceso a la suscripción está limitado al administrador de la suscripción.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite a los clientes minimizar el número de usuarios con acceso a determinada información como, por ejemplo, datos personales.  Los administradores pueden usar AAD Privileged Identity Management para detectar, restringir y supervisar las identidades con privilegios y su acceso a los recursos. Esta funcionalidad también puede utilizarse para aplicar un acceso administrativo a petición, Just-in-Time cuando sea necesario.
- [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.

### <a name="security"></a>Seguridad
**Administración de secretos**: la solución utiliza [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Las siguientes funcionalidades de Azure Key Vault ayudan a los clientes a proteger los datos personales y el acceso a dichos datos:
- Se configuran directivas de acceso avanzadas según las necesidades.
- Se definen directivas de acceso a Key Vault con los permisos mínimos requeridos para las claves y los secretos.
- Todas las claves y los secretos en Key Vault tienen fechas de expiración.
- Todas las claves de Key Vault están protegidas por módulos de seguridad de hardware especializados (HSM). El tipo de clave es una clave RSA de 2048 bits protegida por HSM.
- Se otorgan los permisos necesarios mínimos a todos los usuarios e identidades que utilizan RBAC.
- Los registros de diagnóstico de Key Vault están habilitados con un período de retención de al menos 365 días.
- Las operaciones criptográficas permitidas para las claves están restringidas únicamente a las requeridas.

**Alertas de seguridad**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) permite a los clientes supervisar el tráfico, recopilar registros y analizar orígenes de datos en busca de amenazas. Además, Azure Security Center accede a la configuración existente de los servicios de Azure para proporcionar recomendaciones de configuración y servicio que ayuden a mejorar la postura de seguridad y proteger los datos personales. Azure Security Center incluye un [informe de inteligencia de amenazas ](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) por cada amenaza detectada para ayudar a los equipos de respuesta a incidentes a investigar y corregir las amenazas.

**Application Gateway**: la arquitectura reduce el riesgo de sufrir puntos vulnerables de seguridad, ya que usa Application Gateway con el firewall de aplicaciones web (WAF) y tiene el conjunto de reglas OWASP habilitado. Entre estas funcionalidades, cabe destacar:

- [SSL de un extremo a otro](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Opción para habilitar la [descarga SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Opción para deshabilitar [TLS v1.0 y v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicaciones web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevención](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) con el conjunto de reglas OWASP 3.0
- Opción para habilitar el [registro de diagnósticos](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Sondeos de estado personalizados](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) y [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) que proporcionan protección adicional y notificaciones. Asimismo, Azure Security Center también proporciona un sistema de reputación.

### <a name="logging-and-auditing"></a>Registro y auditoría

OMS proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.
- **Registros de actividad:** [los registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Storage, registros de auditoría de Key Vault, y registros de firewall y acceso a Application Gateway.
- **Archivado de registros**: todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para su archivado. El usuario puede configurar la retención hasta 730 días para cumplir los requisitos de retención específicos de una organización. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se incluyen las siguientes soluciones de OMS:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución de antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la solución Azure Automation almacena, ejecuta y administra runbooks. En esta solución, los runbooks ayudan a recopilar registros de Application Insights y Azure SQL Database.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, que incluye el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure.
-   [Change Tracking](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) ayuda a los usuarios a realizar un seguimiento del rendimiento, mantener la seguridad e identificar tendencias permitiendo a las organizaciones auditar, crear alertas y archivar datos, incluido el seguimiento de las llamadas a la API en los recursos Azure de los clientes.

**Application Insights**
[Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview) es un servicio de Application Performance Management (APM) extensible para desarrolladores web en varias plataformas. Application Insights detecta anomalías de rendimiento y los clientes pueden utilizarlo para supervisar la aplicación web activa. Incluye herramientas de análisis eficaces que ayudan a los clientes a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación. Está diseñado para ayudar a los clientes a mejorar continuamente el rendimiento y la facilidad de uso.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos de esta arquitectura de referencia está disponible para su [descarga](https://aka.ms/gdprPaaSdfd) y se encuentra a continuación: El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Modelo de amenazas de la aplicación web de PaaS para RGPD](images/gdpr-paaswa-threat-model.png?raw=true "PaaS Web Applicaiton for GDPR threat model")

## <a name="compliance-documentation"></a>Documentación de cumplimiento
La [matriz de responsabilidades de clientes del plano técnico de seguridad y cumplimiento de Azure: RGPD](https://aka.ms/gdprCRM) enumera las responsabilidades de controlador y de procesador para todos los artículos del RGPD. Tenga en cuenta que para los servicios de Azure, el cliente suele ser el controlador y Microsoft actúa como procesador.

La [matriz de implementación de aplicaciones web de PaaS del plano técnico de seguridad y cumplimiento de Azure: RGPD](https://aka.ms/gdprPaaSWeb) proporciona información sobre qué artículos de RGPD aborda la arquitectura de aplicaciones web de PaaS, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada artículo cubierto.


## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute
Debe configurarse un túnel VPN seguro o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de aplicaciones web de PaaS. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como un mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicado entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Están [disponibles](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
