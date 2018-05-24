---
title: 'Plano técnico de seguridad y cumplimiento de Azure: análisis para RGPD'
description: 'Plano técnico de seguridad y cumplimiento de Azure: análisis para RGPD'
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 9114dea1f95198b5ff8dc1bf759be5d1179885fd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161848"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Plano técnico de seguridad y cumplimiento de Azure: análisis para RGPD

## <a name="overview"></a>Información general
El Reglamento general de protección de datos (RGPD) contiene muchos requisitos sobre la recopilación, almacenamiento y uso de información personal, incluida la forma en que las organizaciones identifican y protegen los datos personales, se adaptan a los requisitos de transparencia, detectan e informan de las infracciones de datos personales, y forman sobre la privacidad al personal y a otros empleados. El RGPD ofrece un mayor control sobre los datos personales e impone muchas nuevas obligaciones a las organizaciones que recopilan, controlan o analizan datos personales. Este Reglamento impone nuevas reglas sobre las organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes en la UE. El RGPD se aplica con independencia de la ubicación de la organización.

Microsoft ha diseñado Azure con medidas de seguridad y directivas de privacidad líderes del sector para proteger los datos en la nube, incluidas las categorías de datos personales identificadas por el RGPD. Los [términos contractuales](http://aka.ms/Online-Services-Terms) de Microsoft comprometen a Microsoft con los requisitos de los procesadores.

Este plano técnico de seguridad y cumplimiento de Azure proporciona una guía para implementar una arquitectura de análisis de datos en Azure que ayuda con los requisitos del RGPD. Esta solución muestra las formas en que los clientes pueden cumplir con los requisitos específicos de seguridad y cumplimiento y sirve como base para que los clientes creen y configuren sus propias soluciones de análisis de datos en Azure. Los clientes pueden utilizar esta arquitectura de referencia y seguir el [proceso de cuatro pasos](https://aka.ms/gdprebook) de Microsoft en su recorrido al cumplimiento del RGPD:
1. Descubrir: Identificar qué datos personales existen y dónde residen.
2. Administrar: Controlar cómo se utilizan los datos personales y cómo se accede a ellos.
3. Proteger: Establecer controles de seguridad para prevenir, detectar y responder a puntos vulnerables e infracciones de datos.
4. Informar: Guardar la documentación necesaria y administrar las solicitudes de datos y las notificaciones de infracciones.

Esta arquitectura de referencia, las guías de implementación asociadas y los modelos de amenazas están diseñados como base para que los clientes puedan adaptarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Tenga en cuenta lo siguiente:
- Esta arquitectura proporciona una línea de base para ayudar a los clientes a implementar cargas de trabajo en Azure de manera compatible con el Reglamento.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura
Esta solución proporciona una plataforma analítica sobre la cual los clientes pueden crear sus propias herramientas de análisis. La arquitectura de referencia esboza un caso de uso genérico en el que los clientes introducen datos ya sea mediante importaciones de datos masivos por parte del administrador de datos/SQL o mediante actualizaciones de datos operativos a través de un usuario operativo. Ambos flujos de trabajo incorporan Azure Functions para importar datos en Azure SQL Database. El cliente debe configurar Azure Functions en Azure Portal para controlar las tareas de importación únicas a los requisitos de análisis propios de cada cliente.

Azure ofrece una variedad de servicios de informes y análisis al cliente; sin embargo, esta solución incorpora los servicios de Azure Machine Learning en conjunto con Azure SQL Database para examinar rápidamente los datos y entregar resultados más rápidos mediante un modelado más inteligente de los datos del cliente. Azure Machine Learning es una forma de aprendizaje automático destinada a aumentar la velocidad de las consultas mediante el descubrimiento de nuevas relaciones entre conjuntos de datos. Una vez que los datos se han entrenado mediante varias funciones estadísticas, se pueden sincronizar hasta siete grupos de consulta adicionales (ocho en total incluido el servidor del cliente) con los mismos modelos tabulares para distribuir la carga de trabajo de la consulta y reducir el tiempo de respuesta.

Para mejorar el análisis y los informes, las bases de datos de Azure SQL Database pueden configurarse con índices de almacén de columnas. Tanto Azure Machine Learning como las bases de datos de Azure SQL Database se pueden escalar, reducir o apagarse completamente en respuesta al uso del cliente. Todo el tráfico SQL se cifra con SSL mediante la inclusión de certificados autofirmados. Como procedimiento recomendado, Azure recomienda el uso de una entidad de certificación de confianza para mejorar la seguridad.

Cuando se cargan los datos a la instancia de Azure SQL Database y se entrenan con Azure Machine Learning, los digiere el usuario operativo y el administrador de datos/SQL con Power BI. Power BI muestra los datos de forma intuitiva y reúne la información a través de múltiples conjuntos de datos para obtener un mayor conocimiento. Su alto grado de adaptabilidad y fácil integración con Azure SQL Database asegura que los clientes puedan configurarlo para tratar una amplia gama de escenarios según sus necesidades de negocio.

Toda la solución se basa en el servicio Azure Storage que los clientes configuran desde Azure Portal. Azure Storage cifra todos los datos con Storage Service Encryption para mantener la confidencialidad de los datos en reposo. El almacenamiento con redundancia geográfica garantiza que un evento adverso en el centro de datos principal del cliente no tendrá como resultado una pérdida de datos, ya que una segunda copia se almacenará en una ubicación separada a cientos de kilómetros de distancia.

Para mejorar la seguridad, esta arquitectura administra los recursos con Azure Active Directory y Azure Key Vault. El estado del sistema se supervisa mediante Operations Management Suite (OMS) y Azure Monitor. Los clientes configuran ambos servicios de monitorización para capturar registros y mostrar el estado del sistema en un único panel de fácil navegación.

Azure SQL Database se administra comúnmente mediante SQL Server Management Studio (SSMS), que se ejecuta desde una máquina local configurada para acceder a Azure SQL Database mediante una conexión segura VPN o ExpressRoute. **Azure recomienda configurar una conexión VPN o de Azure ExpressRoute para la administración y la importación de datos en el grupo de recursos de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de análisis para RGPD](images/gdpr-analytics-architecture.png?raw=true "Analytics for GDPR reference architecture diagram")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

- Azure Functions
- Azure SQL Database
- Azure Machine Learning
- Azure Active Directory
- Azure Key Vault
- Operations Management Suite (OMS)
- Azure Monitor
- Azure Storage
- Panel de Power BI
- Azure Data Catalog
- Azure Security Center
- Application Insights
- Azure Event Grid
- grupos de seguridad de red

## <a name="deployment-architecture"></a>Arquitectura de implementación
En la siguiente sección se detallan los elementos de desarrollo e implementación.

**Azure Event Grid**
[Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/overview) permite a los clientes crear fácilmente aplicaciones con arquitecturas basadas en eventos. Los usuarios seleccionan el recurso de Azure al que les gustaría suscribirse y asignan el controlador de eventos o el punto de conexión de webhook para enviar el evento. Los clientes pueden proteger los puntos de conexión de webhook mediante la incorporación de parámetros de consulta a la URL de webhook al crear una suscripción a eventos. Azure Event Grid solo admite puntos de conexión de webhook HTTPS. Azure Event Grid permite a los clientes controlar el nivel de acceso dado a distintos usuarios para realizar diversas operaciones de administración, como enumerar las suscripciones a eventos, crear otras nuevas y generar claves. Event Grid utiliza el control de acceso basado en rol (RBAC) de Azure.

**Azure Functions**
[Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview) es un servicio de proceso sin servidor que permite ejecutar código a petición sin necesidad de aprovisionar ni administrar explícitamente la infraestructura. Use Azure Functions para ejecutar un script o un fragmento de código en respuesta a diversos eventos.

**Azure Machine Learning**
[Azure Machine Learning](https://docs.microsoft.com/en-us/azure/machine-learning/preview/) es una técnica de ciencia de datos que permite a los equipos utilizar datos existentes para prever tendencias, resultados y comportamientos futuros.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) facilita que los usuarios que administran los datos puedan detectar y comprender los orígenes de datos. En los orígenes de datos comunes se pueden registrar, etiquetar y buscar datos personales. Los datos permanecen en la ubicación existente, pero se agrega una copia de sus metadatos a Data Catalog, junto con una referencia a la ubicación del origen de datos. Los metadatos también se indexan no solo para que todos los orígenes de datos se puedan detectar fácilmente a través de la búsqueda, sino también para que los usuarios que los detecten puedan comprenderlos.

### <a name="virtual-network"></a>Red virtual
La arquitectura de referencia define una red virtual privada con un espacio de direcciones de 10.0.0.0/16.

**Grupos de seguridad de red**: los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contienen listas de control de acceso (ACL) que permiten o deniegan el tráfico en una red virtual. Los NSG pueden usarse para proteger el tráfico en una subred o a nivel de máquina virtual individual. Los grupos de seguridad de red siguientes ya existen:
  - Un grupo de seguridad de red para Active Directory
  - Un grupo de seguridad de red para la carga de trabajo

Cada uno de los grupos de seguridad de red tiene puertos y protocolos específicos abiertos para que la solución pueda funcionar de forma segura y correcta. Además, las siguientes opciones de configuración están habilitadas para cada NSG:
  - Los [eventos y registros de diagnóstico](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log) están habilitados y se almacenan en la cuenta de almacenamiento.
  - Log Analytics de OMS está conectado a los [diagnósticos del grupo de seguridad de red](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subredes**: asegúrese de que cada subred esté asociada a su grupo de seguridad de red correspondiente.

### <a name="data-in-transit"></a>Datos en tránsito
De manera predeterminada, Azure cifra todas las comunicaciones hacia y desde los centros de datos de Azure. Todas las transacciones a Azure Storage mediante Azure Portal se realizan mediante HTTPS.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Azure Storage** Para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esto ayuda a proteger los datos personales en apoyo de los compromisos de seguridad de la organización y los requisitos de cumplimiento definidos por RGPD.

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
- [Enmascaramiento dinámico de datos (DDM) de SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de los datos personales al enmascarar los datos a aquellos usuarios o aplicaciones sin privilegios. DDM puede detectar datos potencialmente confidenciales y sugerir las máscaras adecuadas que se pueden aplicar. Esto ayuda a identificar los datos personales que cumplen los requisitos para la protección del RGPD y a reducir el acceso de tal manera que no salga de la base de datos mediante un acceso no autorizado. **Nota: Los clientes deberán ajustar la configuración de DDM para ajustarse al esquema de la base de datos.**

### <a name="identity-management"></a>Administración de identidades
Las siguientes tecnologías proporcionan funcionalidades de administración del acceso a datos personales en el entorno de Azure.
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios para esta solución se crean en AAD, incluidos los usuarios que acceden a la base de datos de Azure SQL Database.
-   La autenticación para acceder a la aplicación se realiza con AAD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos usa AAD para autenticar la aplicación Azure SQL Database. Para más información, consulte cómo [proteger los datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [El control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite a los administradores definir permisos de acceso bien definidos para conceder solo el nivel de acceso que los usuarios necesitan para realizar su trabajo. En lugar de dar a cada usuario permisos ilimitados para los recursos de Azure, los administradores pueden permitir solo ciertas acciones para acceder a los datos personales. El acceso a la suscripción está limitado al administrador de la suscripción.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite a los clientes minimizar el número de usuarios con acceso a determinada información como, por ejemplo, datos personales.  Los administradores pueden usar AAD Privileged Identity Management para detectar, restringir y supervisar las identidades con privilegios y su acceso a los recursos. Esta funcionalidad también puede utilizarse para aplicar un acceso administrativo a petición, Just-in-Time cuando sea necesario.
-   [AAD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.

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

### <a name="logging-and-auditing"></a>Registro y auditoría

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.
- **Registros de actividad:** [los registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos incluyen los registros del sistema de eventos de Windows y los registros de Azure Blob Storage, tablas y cola.
- **Archivado de registros:** todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para que queden archivados durante un período de retención definido de dos días. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se incluyen las siguientes soluciones de OMS:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución de antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la solución Azure Automation almacena, ejecuta y administra runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, que incluye el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure.
-   [Change Tracking](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) ayuda a los clientes a realizar un seguimiento del rendimiento, mantener la seguridad e identificar tendencias permitiendo a las organizaciones auditar, crear alertas y archivar datos, incluido el seguimiento de las llamadas a la API en los recursos Azure de los clientes.

**Application Insights**
[Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/) es un servicio de Application Performance Management (APM) extensible para desarrolladores web en varias plataformas. Úselo para supervisar la aplicación web en directo. Detecta anomalías en el rendimiento e incluye herramientas de análisis eficaces que ayudan a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación. Está diseñado para ayudar a los usuarios a mejorar continuamente el rendimiento y la facilidad de uso.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos de esta arquitectura de referencia está disponible para su [descarga](https://aka.ms/gdprAnalyticsdfd) y se encuentra a continuación: El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Modelo de amenazas de análisis para RGPD](images/gdpr-analytics-threat-model.png?raw=true "Analytics for GDPR threat model")

## <a name="compliance-documentation"></a>Documentación de cumplimiento
La [matriz de responsabilidades de clientes del plano técnico de seguridad y cumplimiento de Azure: RGPD](https://aka.ms/gdprCRM) enumera las responsabilidades de controlador y de procesador para todos los artículos del RGPD. Tenga en cuenta que para los servicios de Azure, el cliente suele ser el controlador y Microsoft actúa como procesador.

La [matriz de implementación de análisis de datos del plano técnico de seguridad y cumplimiento de Azure: RGPD](https://aka.ms/gdprAnalytics) proporciona información sobre qué artículos del Reglamento aborda la arquitectura de análisis de datos, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada artículo cubierto.


## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute
Debe configurarse un túnel VPN seguro o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de análisis de datos. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como un mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicado entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Están [disponibles](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

### <a name="extract-transform-load-etl-process"></a>Proceso de extracción, transformación y carga de datos (ETL)
[PolyBase](https://docs.microsoft.com/en-us/sql/relational-databases/polybase/polybase-guide) puede cargar datos en Azure SQL Database sin necesidad de una herramienta ETL o de importación independiente. PolyBase permite el acceso a los datos mediante consultas T-SQL. Con PolyBase se puede usar la inteligencia empresarial y la pila de análisis de Microsoft, así como herramientas de terceros compatibles con SQL Server.

### <a name="azure-active-directory-setup"></a>Configuración de Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) es esencial para la administración de la implementación y la concesión de acceso a las personas que interactúan con el entorno. Se puede integrar una instancia de Windows Server Active Directory con AAD en [cuatro clics](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Los clientes también pueden enlazar la infraestructura de Active Directory implementada (controladores de dominio) con una instancia de AAD existente al convertir la infraestructura de Active Directory implementada en un subdominio de un bosque de AAD.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
