---
title: 'Plano técnico de seguridad y cumplimiento de Azure: análisis de FedRAMP'
description: 'Plano técnico de seguridad y cumplimiento de Azure: análisis de FedRAMP'
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: db9e49cc4dc02b6864bee2dc4b73ff3c085f5380
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206721"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Plano técnico de seguridad y cumplimiento de Azure: análisis de FedRAMP

## <a name="overview"></a>Información general

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) es un programa a nivel del gobierno de los Estados Unidos que proporciona un enfoque estandarizado para la evaluación de la seguridad, la concesión de autorizaciones, y la supervisión continua de servicios y productos en la nube. Este plano técnico de seguridad y cumplimiento de Azure proporciona orientación sobre cómo entregar una arquitectura de análisis de Microsoft Azure que ayude a implementar un subconjunto de controles elevados de FedRAMP. Esta solución ofrece una guía sobre la implementación y la configuración de recursos de Azure para una arquitectura de referencia común, la cual muestra las formas en que los clientes pueden cumplir los requisitos específicos de seguridad y cumplimiento, y sirve como base para que los clientes compilen y configuren sus propias soluciones de análisis en Azure.

Esta arquitectura de referencia, las guías de implementación de los controles asociados y los modelos de amenazas están diseñados como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Implementar una aplicación sin modificar en este entorno no es suficiente para satisfacer por completo los requisitos de la línea de base de FedRAMP High. Tenga en cuenta lo siguiente:
- Esta arquitectura proporciona una línea de base para ayudar a los clientes a implementar cargas de trabajo en Azure de manera compatible con FedRAMP.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura

Esta solución proporciona una plataforma analítica sobre la cual los clientes pueden crear sus propias herramientas de análisis. La arquitectura de referencia esboza un caso de uso genérico en el que los clientes introducen datos ya sea a través de importaciones de datos masivos por parte del administrador de datos/SQL o mediante actualizaciones de datos operativos a través de un usuario operativo. Ambos flujos de trabajo incorporan [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) para importar datos en SQL Database. Azure Functions debe estar configurado por el cliente a través de Azure Portal para controlar las tareas de importación únicas a los requisitos de análisis propios de cada cliente.

Microsoft Azure ofrece una variedad de servicios de informes y análisis al cliente; sin embargo, esta solución incorpora los servicios de Azure Analysis Services en conjunto con Azure SQL Database para examinar rápidamente los datos y entregar resultados más rápidos a través de un modelado más inteligente de los datos del cliente. Los servicios de Azure Analysis Service es una forma de aprendizaje automático destinada a aumentar la velocidad de las consultas mediante el descubrimiento de nuevas relaciones entre conjuntos de datos. Una vez que los datos se han entrenados a través de varias funciones estadísticas, se pueden sincronizar hasta siete grupos de consulta adicionales (ocho en total incluyendo el servidor del cliente) con los mismos modelos tabulares para distribuir la carga de trabajo de la consulta y reducir el tiempo de respuesta.

Para mejorar el análisis y la generación de informes, las bases de datos SQL Database pueden configurarse con índices de almacén de columnas. Tanto los servicios de Azure Analytics Service como las bases de datos de SQL Database se pueden escalar, reducir o apagarse completamente en respuesta al uso del cliente. Todo el tráfico SQL está cifrado con SSL mediante la inclusión de certificados autofirmados. Como procedimiento recomendado, Azure recomienda el uso de una entidad de certificación de confianza para mejorar la seguridad.

Cuando se suben los datos a la instancia de Azure SQL Database y se entrenan por los servicios de Azure Analysis Service, los digiere el usuario operativo y el administrador de datos o SQL con Power BI. Power BI muestra los datos de forma intuitiva y reúne la información a través de múltiples conjuntos de datos para obtener un mayor conocimiento. Su alto grado de adaptabilidad y fácil integración con Azure SQL Database asegura que los clientes puedan configurarlo para manejar una amplia gama de escenarios según sus necesidades de negocio.

La solución completa se basa en un servicio de Azure Storage que los clientes de la cuenta configuran desde Azure Portal. Azure Storage cifra todos los datos con Storage Service Encryption para mantener la confidencialidad de los datos en reposo.  El almacenamiento con redundancia geográfica garantiza que un evento adverso en el centro de datos principal del cliente no tendrá como resultado una pérdida de datos, ya que una segunda copia se almacenará en una ubicación separada a cientos de kilómetros de distancia.

Para mejorar la seguridad, esta arquitectura administra los recursos con Azure Active Directory y Azure Key Vault. El estado del sistema se supervisa mediante Operations Management Suite (OMS) y Azure Monitor. Los clientes configuran ambos servicios de monitorización para capturar registros y mostrar el estado del sistema en un único panel de fácil navegación.

Azure SQL Database se administra comúnmente mediante SQL Server Management Studio (SSMS), que se ejecuta desde una máquina local configurada para acceder a Azure SQL Database a través de una conexión segura VPN o ExpressRoute. **Azure recomienda configurar una conexión VPN o de Azure ExpressRoute para la administración y la importación de datos en el grupo de recursos de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de análisis para FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics for FedRAMP reference architecture diagram")

### <a name="roles"></a>Roles
El plano técnico de análisis describe un escenario con tres tipos de usuario general: el usuario operativo, el administrador de datos/SQL y el ingeniero de sistemas. El control de acceso basado en rol de Azure (RBAC) permite la implementación de una administración de acceso precisa mediante roles personalizados integrados. Los recursos están disponibles para configurar el [control de acceso basado en rol](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) y para describir e implementar [roles predefinidos](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

#### <a name="systems-engineer"></a>Ingeniero de sistemas
El ingeniero de sistemas es el propietario de la suscripción del cliente de Azure y configura la implementación de la solución mediante Azure Portal.

#### <a name="sqldata-administrator"></a>Administrador de datos/SQL
El administrador de datos/SQL establece la función de importación de datos masivos y la función de actualización de datos operativos para cargar a la base de datos SQL de Azure. El administrador de datos/SQL no es responsable de las actualizaciones de datos operativos en la base de datos, pero podrá ver los datos mediante Power BI.

#### <a name="operational-user"></a>Usuario operativo
El usuario operativo actualiza los datos con regularidad y posee la generación de datos diarias. El usuario operativo también interpreta los resultados mediante Power BI.

### <a name="azure-services"></a>Servicios de Azure

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).
- Azure Functions
- Azure SQL Database
- Azure Analysis Service
- Azure Active Directory
- Azure Key Vault
- OMS
- Azure Monitor
- Azure Storage
- ExpressRoute/VPN Gateway
- Panel de Power BI

## <a name="deployment-architecture"></a>Arquitectura de implementación
En la siguiente sección se detallan los elementos de desarrollo e implementación.

![texto alternativo](images/fedramp-analytics-components.png?raw=true "Diagrama de componentes de análisis de FedRAMP")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) son soluciones para ejecutar pequeños fragmentos de código en la nube a través de la mayoría de lenguajes de programación. En esta solución, Functions se integra con Azure Storage para extraer automáticamente los datos de los clientes a la nube, con lo que se facilita la integración con otros servicios de Azure. Functions es fácilmente escalable y solo incurre en un costo cuando se ejecuta.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) proporciona modelado de datos empresariales e integración con los servicios de la plataforma de datos de Azure. Azure Analysis Service acelera la navegación a través de cantidades masivas de datos mediante la combinación de datos de múltiples orígenes en un único modelo de datos.

**Power BI**: [Power BI ](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) proporciona funcionalidad de análisis y de informes para los clientes que intentan obtener un mayor conocimiento de sus esfuerzos de procesamiento de datos.

### <a name="networking"></a>Redes
**Grupos de seguridad de red**: [los grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) se configuran para administrar el tráfico dirigido a los servicios y recursos implementados. Los grupos de seguridad de red se establecen en un esquema de denegación de forma predeterminada y solo permiten el tráfico contenido en la lista de control de acceso (ACL) preconfigurada.

Cada uno de los grupos de seguridad de red tiene puertos y protocolos específicos abiertos para que la solución pueda funcionar de forma segura y correcta. Además, las siguientes opciones de configuración están habilitadas para cada NSG:
  - Los [eventos y registros de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) están habilitados y se almacenan en la cuenta de almacenamiento.
  - [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) de OMS está conectado a los registros de diagnósticos del grupo de seguridad de red.

### <a name="data-at-rest"></a>Datos en reposo
La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Replicación de datos** Azure Government tiene dos opciones para [la replicación de datos](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - La configuración predeterminada para la replicación de datos es el **almacenamiento con redundancia geográfica (GRS)**, que almacena asincrónicamente los datos de los clientes en un centro de datos independiente fuera de la región primaria. Esto asegura la recuperación de datos en un evento de pérdida total para el centro de datos principal.
 - El **almacenamiento con redundancia local (LRS)** también se puede configurar mediante la cuenta de Azure Storage. LRS replica los datos dentro de una unidad de escalado de almacenamiento, que se hospeda en la misma región en la que el cliente crea la cuenta. Todos los datos se replican al mismo tiempo, lo que garantiza que no se pierde ningún dato de copia de seguridad en un error de unidad de escalado de almacenamiento principal.

**Azure Storage** Para cumplir los requisitos de datos cifrados en reposo, todos los servicios implementados en esta arquitectura de referencia se aprovechan de [Azure Storage ](https://azure.microsoft.com/services/storage/), que almacena datos con [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos y sistema operativo. La solución se integra con Azure Key Vault para ayudarle a controlar y administrar las claves de cifrado del disco.

**Azure SQL Database** La instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   [SQL Database Auditing](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de Azure Storage.
-   SQL Database está configurado para usar el [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado de archivos de datos y de registro en tiempo real para proteger la información en reposo. TDE garantiza que los datos almacenados no hayan estado sujetos a un acceso no autorizado.
-   Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   [Detección de amenazas SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite la detección y respuesta ante posibles amenazas a medida que se producen, al proporcionar alertas de seguridad para actividades sospechosas en la base de datos, o puntos vulnerables potenciales, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas de Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantiza que los datos confidenciales nunca van a aparecer como texto no cifrado dentro del sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
-   El [enmascaramiento de los datos dinámicos de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) puede realizarse después de la implementación de la arquitectura de referencia. Los clientes deberán ajustar la configuración del enmascaramiento de los datos dinámicos para ajustarse al esquema de la base de datos.

### <a name="logging-and-audit"></a>Registro y auditoría
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) genera una visualización completa de los datos de supervisión, incluidos registros de actividad, métricas y datos de diagnóstico, lo que permite a los clientes crear una imagen completa del estado del sistema.  
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.
- **Registros de actividad:** [los registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, Azure Blob Storage, tablas y registros de cola.
- **Registros de firewall:** Application Gateway proporciona registros de diagnóstico y acceso completos. Los registros de firewall están disponibles para los recursos de Application Gateway con WAF habilitado.
- **Archivado de registros:** todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada y que así queden archivados durante un período de retención definido de dos días. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se incluyen las siguientes soluciones de OMS:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la solución Azure Automation almacena, ejecuta y administra runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel de Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure.

### <a name="identity-management"></a>Administración de identidades
-   La autenticación para acceder a la aplicación se realiza con Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos usa Azure AD para autenticar la aplicación Azure SQL Database. Para más información, consulte cómo [proteger datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.
-   El [control de acceso basado en rol de Azure (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite administrar el control de acceso de Azure. El acceso de suscripción se limita al administrador de esta.

Para obtener más información sobre el uso de las características de seguridad de Azure SQL Database, consulte el ejemplo de la [aplicación de demostración de Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Seguridad
**Administración de secretos**: la solución usa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube.

## <a name="guidance-and-recommendations"></a>Guía y recomendaciones

### <a name="expressroute-and-vpn"></a>ExpressRoute y VPN
Debe configurarse [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o un túnel VPN seguro para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de almacén de datos. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Al configurar ExpressRoute o una VPN adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

### <a name="azure-active-directory-setup"></a>Configuración de Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es esencial para la administración de la implementación y la concesión de acceso a las personas que interactúan con el entorno. Se puede integrar una instancia de Windows Server Active Directory con AAD en [cuatro clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Los clientes también pueden enlazar la infraestructura de Active Directory implementada (controladores de dominio) con una instancia de AAD existente al convertir la infraestructura de Active Directory implementada en un subdominio de un bosque de AAD.

### <a name="additional-services"></a>Servicios adicionales
#### <a name="iaas---vm-vonsiderations"></a>IaaS - Consideraciones de máquinas virtuales
Esta solución PaaS no incorpora ninguna máquina virtual de IaaS de Azure. Un cliente podría crear una máquina virtual de Azure para ejecutar muchos de estos servicios PaaS. En este caso, se podrían aprovechar características y servicios específicos para la continuidad del negocio y OMS:

##### <a name="business-continuity"></a>Continuidad del negocio
- **Alta disponibilidad**: las cargas de trabajo del servidor se agrupan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ayudar a garantizar una alta disponibilidad de las máquinas virtuales de Azure. Durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual está disponible para cumplir el 99,95 % del Acuerdo de Nivel de Servicio de Azure.

- **Almacén de Recovery Services**: el [almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) aloja datos de copia de seguridad y protege todas las configuraciones de Azure Virtual machines en esta arquitectura. Con los almacenes de Recovery Services, los clientes pueden restaurar archivos y carpetas desde una máquina virtual de IaaS sin tener que restaurar toda la máquina virtual, lo que permite unos tiempos de restauración más rápidos.

##### <a name="oms"></a>OMS
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución Antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, que incluye el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

##### <a name="security"></a>Seguridad
- **Protección contra malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuales proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.
- **Administración de revisiones**: las máquinas virtuales Windows implementadas como parte de esta arquitectura de referencia se configuran de forma predeterminada para recibir actualizaciones automáticas desde Windows Update Service. Esta solución también incluye el servicio [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) de OMS, a través del cual se pueden crear implementaciones actualizadas de las máquinas virtuales cuando sea necesario.

#### <a name="azure-commercial"></a>Azure Commercial
Aunque esta arquitectura de análisis de datos no está diseñada para su implementación en el entorno de [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), se pueden lograr objetivos similares a través de los servicios que se describen en esta arquitectura de referencia, así como de otros servicios disponibles solo en el entorno de Azure Commercial. Tenga en cuenta que el entorno comercial de Azure mantiene FedRAMP JAB P-ATO en el nivel de impacto moderado, lo cual permite a los organismos gubernamentales y los asociados implementar información moderadamente confidencial en la nube aprovechando el entorno de Azure Commercial.

Azure Commercial ofrece una amplia variedad de servicios de análisis para extraer información de grandes cantidades de datos:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), un componente de [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), desarrolla un modelo de análisis predictivo a partir de uno o más orígenes de datos. Las funciones estadísticas se utilizan a lo largo de varias iteraciones para generar un modelo efectivo que aplicaciones como Power BI pueden consumir.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos. Azure Data Lake Store es compatible con la mayoría de componentes de código abierto del ecosistema Hadoop y se integra perfectamente con otros servicios de Azure.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos (DFD) de esta arquitectura de referencia está disponible para su [descarga](https://aka.ms/blueprintanalyticsthreatmodel) y se encuentra a continuación:

## <a name="compliance-documentation"></a>Documentación de cumplimiento
En la [matriz de responsabilidades elevadas de los clientes del plano técnico de seguridad y cumplimiento de Azure: FedRAMP](https://aka.ms/blueprinthighcrm) se enumeran todos los controles de seguridad necesarios para la línea de base elevada de FedRAMP. Análogamente, en la [matriz de responsabilidades moderadas de los clientes del plano técnico de seguridad y cumplimiento de Azure: FedRAMP](https://aka.ms/blueprintanalyticscimmod) se enumeran todos los controles de seguridad necesarios para la línea de base moderada de FedRAMP. Ambos documentos incluyen información detallada sobre si la implementación de cada control es la responsabilidad de Microsoft,d el cliente o de ambos.

La [matriz de implementación de control elevado del plano técnico de seguridad y cumplimiento de Azure: FedRAMP](https://aka.ms/blueprintanalyticscimhigh) y la [matriz de implementación control moderado del plano técnico de seguridad y cumplimiento de Azure: FedRAMP](https://aka.ms/blueprintanalyticscimmod) proporcionan información sobre los controles cubiertos por la arquitectura de análisis para cada línea de base de FedRAMP, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada control cubierto.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
