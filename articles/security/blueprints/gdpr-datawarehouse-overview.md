---
title: 'Azure Security and Compliance Blueprint: Data Warehouse para el RGPD'
description: 'Azure Security and Compliance Blueprint: Data Warehouse para el RGPD'
services: security
author: jomolesk
ms.assetid: 7a33fb3b-cfb6-49dd-ab4d-c53cf0d5da41
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: a091f4c941b1f4a338ad23956dbba3a7b89b87e8
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161868"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-gdpr"></a>Azure Security and Compliance Blueprint: Data Warehouse para el RGPD

## <a name="overview"></a>Información general
El Reglamento general de protección de datos (RGPD) contiene muchos requisitos sobre la recopilación, almacenamiento y uso de información personal, incluida la forma en que las organizaciones identifican y protegen los datos personales, se adaptan a los requisitos de transparencia, detectan e informan de las infracciones de datos personales, y ofrecen cursos sobre la privacidad al personal y a otros empleados. El RGPD ofrece un mayor control sobre los datos personales e impone muchas nuevas obligaciones a las organizaciones que recopilan, controlan o analizan datos personales. El RGPD impone nuevas reglas sobre las organizaciones que ofrecen bienes y servicios a personas de la Unión Europea (UE) o que recopilan y analizan datos vinculados a residentes de la UE. El RGPD se aplica con independencia de la ubicación de la organización.

Microsoft ha diseñado Azure con medidas de seguridad y directivas de privacidad líderes del sector para proteger los datos en la nube, incluidas las categorías de datos personales identificadas por el RGPD. Los [términos contractuales](http://aka.ms/Online-Services-Terms) de Microsoft comprometen a Microsoft con los requisitos de los procesadores.

Esta instancia de Azure Security and Compliance Blueprint proporciona una guía para implementar una arquitectura de almacenamiento de datos en Azure que ayuda con los requisitos del RGPD. Esta solución muestra las formas en que los clientes pueden cumplir con los requisitos específicos de seguridad y cumplimiento y sirve como base para que los clientes compilen y configuren sus propias soluciones de almacenamiento de datos en Azure. Los clientes pueden utilizar esta arquitectura de referencia y seguir el [proceso de cuatro pasos](https://aka.ms/gdprebook) de Microsoft en su recorrido hacia el cumplimiento del RGPD:
1. Descubrir: identificar qué datos personales existen y dónde residen.
2. Administrar: controlar cómo se utilizan los datos personales y cómo se accede a ellos.
3. Proteger: establecer controles de seguridad para prevenir, detectar y responder a vulnerabilidades e infracciones de datos.
4. Informar: guardar la documentación necesaria y administrar las solicitudes de datos y las notificaciones de infracciones.

Esta arquitectura de referencia, las guías de implementación asociadas y el modelo de amenazas están diseñados como base para que los clientes puedan adaptarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Tenga en cuenta lo siguiente:
- Esta arquitectura proporciona una línea de base para ayudar a los clientes a implementar cargas de trabajo en Azure de manera compatible con el RGPD.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución compilada con esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura
Esta solución proporciona una arquitectura de referencia que implementa un almacenamiento de datos de alto rendimiento y seguro en la nube. Hay dos niveles de datos independientes en esta arquitectura: uno donde se importan los datos y se almacenan provisional y definitivamente en un entorno de SQL en clúster y otro para Azure SQL Data Warehouse donde se cargan los datos mediante una herramienta de extracción, transformación y carga de datos (por ejemplo, consultas T-SQL de [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) para su procesamiento. Una vez que los datos están almacenados en Azure SQL Data Warehouse, se pueden realizar análisis a gran escala.

Azure ofrece una variedad de servicios de informes y análisis para el cliente. Esta solución incluye SQL Server Reporting Services (SSRS) para la rápida creación de informes desde Azure SQL Data Warehouse. Todo el tráfico SQL se cifra con SSL mediante la inclusión de certificados autofirmados. Como procedimiento recomendado, Azure recomienda el uso de una entidad de certificación de confianza para mejorar la seguridad.

En Azure SQL Data Warehouse, los datos se almacenan en tablas relacionales con almacenamiento en columnas, un formato que reduce significativamente los costos de almacenamiento de datos al tiempo que mejora el rendimiento de las consultas.  Según los requisitos de uso, los recursos de proceso de Azure SQL Data Warehouse se pueden escalar y reducir verticalmente o apagarse completamente si no hay procesos activos que necesiten recursos de proceso.

Un equilibrador de carga de SQL administra el tráfico SQL, lo cual garantiza un rendimiento alto. Todas las máquinas virtuales de esta arquitectura de referencia se implementan en un conjunto de disponibilidad con instancias de SQL Server configuradas en un grupo de disponibilidad AlwaysOn para las funcionalidades de alta disponibilidad y recuperación ante desastres.

Esta arquitectura de referencia de almacenamiento de datos también incluye un nivel de Active Directory (AD) para la administración de recursos dentro de la arquitectura. La subred de Active Directory permite la fácil adopción en una estructura de bosque de AD mayor, lo que permite un funcionamiento continuo del entorno, aunque no esté disponible el acceso al bosque mayor. Todas las máquinas virtuales implementadas están unidas mediante dominio al nivel de Active Directory y usan directivas de grupo de Active Directory para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo.

Una máquina virtual sirve de host de tipo bastión de administración, lo cual proporciona una conexión segura para que los administradores accedan a los recursos implementados. Los datos se cargan en el área de ensayo a través de este host de tipo bastión de administración. **Azure recomienda configurar una conexión VPN o de Azure ExpressRoute para la administración y la importación de datos de en la subred de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de Data Warehouse para el RGPD](images/gdpr-datawarehouse-architecture.png?raw=true "Diagrama de arquitectura de referencia de Data Warehouse para el RGPD")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

-   Azure Virtual Machines
    - (1) Host de tipo bastión
    -   (2) Controlador de dominio de Active Directory
    -   (2) Nodo de clúster de SQL Server
    -   (1) Testigo de SQL Server
-   Conjuntos de disponibilidad
    - Controladores de dominio de Active Directory
    - Testigo y nodos de clúster de SQL
-   Virtual Network
    - (4) Subredes
    -   (4) Grupos de seguridad de red
- SQL Data Warehouse
- SQL Server Reporting Services
- Equilibrador de carga de Azure SQL
- Azure Active Directory
- Almacén de Recovery Services
- Azure Key Vault
- Operations Management Suite (OMS)
- Azure Data Catalog
- Azure Security Center

## <a name="deployment-architecture"></a>Arquitectura de implementación
En la siguiente sección se detallan los elementos de desarrollo e implementación.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) es un almacenamiento de datos empresarial (EDW) en la nube empresarial que aprovecha el procesamiento paralelo masivo (MPP) para ejecutar rápidamente consultas complejas en petabytes de datos, lo que permite que los usuarios identifiquen de manera eficaz los datos personales. Los usuarios pueden usar consultas T-SQL de PolyBase simples para importar macrodatos a SQL Data Warehouse y usar la potencia de MPP para ejecutar análisis de alto rendimiento.

**SQL Server Reporting Services (SSRS)**: [SQL Server Reporting Services](https://docs.microsoft.com/en-us/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) proporciona la rápida creación de informes con tablas, gráficos, mapas, medidores, matrices y más para Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) facilita que los usuarios que administran los datos puedan detectar y comprender los orígenes de datos. En los orígenes de datos comunes se pueden registrar, etiquetar y buscar datos personales. Los datos permanecen en la ubicación existente, pero se agrega una copia de sus metadatos a Data Catalog, junto con una referencia a la ubicación del origen de datos. Los metadatos también se indexan no solo para que todos los orígenes de datos se puedan detectar fácilmente a través de la búsqueda, sino también para que los usuarios que los detecten puedan comprenderlos.

**Host de tipo bastión**: el host de tipo bastión es el único punto de entrada que permite a los usuarios acceder a los recursos implementados en este entorno. El host de tipo bastión proporciona una conexión segura a los recursos implementados al permitir solo el tráfico remoto desde las direcciones IP públicas de una lista segura. Para permitir el tráfico de escritorio remoto (RDP), el origen del tráfico debe definirse en el grupo de seguridad de red (NSG).

Esta solución crea una máquina virtual como host de tipo bastión unido mediante dominio con las siguientes configuraciones:
-   [Extensión antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensión de OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensión de Diagnósticos de Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) con Azure Key Vault
-   Una [directiva de apagado automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reducir el consumo de recursos de la máquina virtual cuando no esté en uso.
-   [Credential Guard de Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que las credenciales y otros secretos se ejecuten en un entorno protegido aislado del sistema operativo en ejecución

### <a name="virtual-network"></a>Red virtual
La arquitectura de referencia define una red virtual privada con un espacio de direcciones de 10.0.0.0/16.

**Grupos de seguridad de red**: los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contienen listas de control de acceso (ACL) que permiten o deniegan el tráfico en una red virtual. Los NSG pueden usarse para proteger el tráfico en una subred o a nivel de máquina virtual individual. Los grupos de seguridad de red siguientes ya existen:
  - Un grupo de seguridad de red para la capa de datos (clústeres de SQL Server, testigo de SQL Server y equilibrador de carga de SQL)
  - Un grupo de seguridad de red para el host de tipo bastión
  - Un grupo de seguridad de red para Active Directory
  - Un grupo de seguridad de red para SQL Server Reporting Services

Cada uno de los grupos de seguridad de red tiene puertos y protocolos específicos abiertos para que la solución pueda funcionar de forma segura y correcta. Además, las siguientes opciones de configuración están habilitadas para cada NSG:
  - Los [eventos y registros de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) están habilitados y se almacenan en la cuenta de almacenamiento.
  - Log Analytics de OMS está conectado a los [diagnósticos del grupo de seguridad de red](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subredes**: asegúrese de que cada subred esté asociada a su grupo de seguridad de red correspondiente.

### <a name="data-at-rest"></a>Datos en reposo
La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Azure Storage**: para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Esto ayuda a proteger los datos personales en cumplimiento de los compromisos de seguridad de la organización y los requisitos de cumplimiento definidos por el RGPD.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado de volúmenes de los discos de datos y del sistema operativo. La solución se integra con Azure Key Vault para ayudar a controlar y administrar las claves de cifrado del disco.

**Azure SQL Database**: la instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   La [auditoría de bases de datos SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
-   SQL Database está configurado para usar el [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado de la base de datos, de las copias de seguridad asociadas y de archivos de registro de transacciones para proteger la información en reposo. TDE garantiza que los datos personales almacenados no hayan estado sujetos a un acceso no autorizado.
-   Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   La [detección de amenazas de SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar y responder ante posibles amenazas a medida que se producen, mediante el envío de alertas de seguridad para actividades sospechosas en la base de datos, posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas de Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos personales confidenciales nunca van a aparecer como texto no cifrado dentro del sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
- La característica [Propiedades extendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) puede utilizarse para interrumpir el procesamiento de los titulares de los datos, ya que permite a los usuarios agregar propiedades personalizadas a los objetos de la base de datos y etiquetar los datos como "interrumpidos" para admitir la lógica de la aplicación y evitar el procesamiento de los datos personales asociados.
- La [seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite a los usuarios definir directivas para restringir el acceso a los datos a fin de interrumpir su procesamiento.
- [Enmascaramiento dinámico de datos (DDM) de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de los datos personales al enmascarar los datos para usuarios o aplicaciones sin privilegios. DDM puede detectar datos potencialmente confidenciales y sugerir las máscaras adecuadas que se pueden aplicar. Esto ayuda a identificar los datos personales que cumplen los requisitos para la protección del RGPD y a reducir el acceso de tal manera que no salga de la base de datos mediante un acceso no autorizado. **Nota: Los clientes deberán ajustar la configuración de DDM para ajustarse al esquema de la base de datos.**

### <a name="identity-management"></a>Administración de identidades
Las siguientes tecnologías proporcionan funcionalidades de administración del acceso a datos personales en el entorno de Azure:
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios para esta solución se crean en AAD, incluidos los usuarios que acceden a SQL Database.
-   La autenticación para acceder a la aplicación se realiza con AAD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos usa AAD para autenticar la aplicación Azure SQL Database. Para más información, consulte cómo [proteger los datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [El control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite a los administradores definir permisos de acceso bien definidos para conceder solo el nivel de acceso que los usuarios necesitan para realizar su trabajo. En lugar de dar a cada usuario permisos ilimitados para los recursos de Azure, los administradores pueden permitir solo ciertas acciones para acceder a los datos personales. El acceso a la suscripción está limitado al administrador de la suscripción.
- [AAD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite a los clientes minimizar el número de usuarios con acceso a determinada información como, por ejemplo, datos personales.  Los administradores pueden usar AAD Privileged Identity Management para detectar, restringir y supervisar las identidades con privilegios y su acceso a los recursos. Esta funcionalidad también puede utilizarse para aplicar un acceso administrativo a petición, Just-in-Time, cuando sea necesario.
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

**Administración de revisiones**: las máquinas virtuales Windows implementadas como parte de esta arquitectura de referencia se configuran de forma predeterminada para recibir actualizaciones automáticas desde Windows Update Service. Esta solución también incluye el servicio [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) de OMS, a través del cual se pueden crear implementaciones actualizadas para aplicar revisiones a las máquinas virtuales cuando sea necesario.

**Protección contra malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Virtual Machines proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.

**Alertas de seguridad**: [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) permite a los clientes supervisar el tráfico, recopilar registros y analizar orígenes de datos en busca de amenazas. Además, Azure Security Center accede a la configuración existente de los servicios de Azure para proporcionar recomendaciones de configuración y servicio que ayuden a mejorar la postura de seguridad y a proteger los datos personales. Azure Security Center incluye un [informe de inteligencia de amenazas](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) por cada amenaza detectada para ayudar a los equipos de respuesta a incidentes a investigar y corregir las amenazas.

### <a name="business-continuity"></a>Continuidad del negocio
**Alta disponibilidad**: las cargas de trabajo del servidor se agrupan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ayudar a garantizar la alta disponibilidad de las máquinas virtuales en Azure. Durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual está disponible para cumplir el 99,95 % del Acuerdo de Nivel de Servicio de Azure.

**Almacén de Recovery Services**: el [almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) aloja datos de copia de seguridad y protege todas las configuraciones de Azure Virtual machines en esta arquitectura. Con los almacenes de Recovery Services, los clientes pueden restaurar archivos y carpetas desde una VM de IaaS sin tener que restaurar toda la VM, lo que permite unos tiempos de restauración más rápidos.

### <a name="logging-and-auditing"></a>Registro y auditoría
[Operations Management Suite (OMS)](https://docs.microsoft.com/en-us/azure/operations-management-suite/operations-management-suite-overview) proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.
- **Registros de actividad:** los [registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico:** los [registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos incluyen los registros del sistema de eventos de Windows y los registros de Azure Blob Storage, tablas y cola.
- **Archivado de registros**: todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para su archivado. El usuario puede configurar la retención hasta 730 días para cumplir los requisitos de retención específicos de una organización. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se incluyen las siguientes soluciones de OMS:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución de antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la solución Azure Automation almacena, ejecuta y administra runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, incluido el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure para un cliente.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos de esta arquitectura de referencia está disponible para su [descarga](https://aka.ms/gdprDWdfd) y se encuentra a continuación. El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Modelo de amenazas de Data Warehouse para el RGPD](images/gdpr-datawarehouse-threat-model.png?raw=true "Modelo de amenazas de Data Warehouse para el RGPD")

## <a name="compliance-documentation"></a>Documentación de cumplimiento
La [matriz de responsabilidades de clientes del plano técnico de seguridad y cumplimiento de Azure: RGPD](https://aka.ms/gdprCRM) enumera las responsabilidades de controlador y de procesador para todos los artículos del RGPD. Tenga en cuenta que, para los servicios de Azure, el cliente suele ser el controlador y Microsoft actúa como procesador.

La [matriz de implementación de Data Warehouse para el RGPD de Azure Security and Compliance Blueprint](https://aka.ms/gdprDW) proporciona información sobre qué artículos del Reglamento aborda la arquitectura de almacenamiento de datos, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada artículo cubierto.

## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute
Debe configurarse [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o un túnel VPN seguro para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de almacenamiento de datos. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicado entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Hay [disponibles](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

### <a name="extract-transform-load-etl-process"></a>Proceso de extracción, transformación y carga de datos (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) puede cargar datos Azure SQL Data Warehouse sin necesidad de una herramienta ETL o de importación independiente. PolyBase permite el acceso a los datos mediante consultas T-SQL. Con PolyBase se puede usar la inteligencia empresarial y la pila de análisis de Microsoft, así como herramientas de terceros compatibles con SQL Server.

### <a name="azure-active-directory-setup"></a>Configuración de Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es esencial para la administración de la implementación y la concesión de acceso a las personas que interactúan con el entorno. Se puede integrar una instancia de Windows Server Active Directory con AAD en [cuatro clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Los clientes también pueden enlazar la infraestructura de Active Directory implementada (controladores de dominio) con una instancia de AAD existente al convertir la infraestructura de Active Directory implementada en un subdominio de un bosque de AAD.

### <a name="optional-services"></a>Servicios opcionales
Azure ofrece una variedad de servicios para ayudar con el almacenamiento y el proceso de almacenamiento provisional de datos con formato y sin formato. Los servicios siguientes se pueden agregar a esta arquitectura de referencia en función de los requisitos del cliente:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) es un servicio en la nube administrado creado para complejos proyectos híbridos de extracción, transformación y carga (ETL), extracción, carga y transformación (ELT) e integración de datos. Azure Data Factory tiene funcionalidades para ayudar a hacer seguimiento de los datos personales y ubicarlos, incluidas herramientas de visualización y supervisión para identificar cuándo llegaron los datos y de dónde provienen. Con Azure Data Factory, los clientes pueden crear y programar flujos de trabajo basados en datos (llamados canalizaciones) que ingieren datos de distintos almacenes de datos. Estas canalizaciones permiten que los clientes ingieran datos desde orígenes internos y externos. Después, pueden procesar y transformar los datos para la salida a almacenes de datos como Azure SQL Data Warehouse.
- Los clientes pueden almacenar de manera provisional los datos no estructurados en [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), lo que permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos.  Azure Data Lake tiene funcionalidades que permiten la extracción y conversión de los datos. Azure Data Lake Store es compatible con la mayoría de componentes de código abierto del ecosistema Hadoop y se integra perfectamente con otros servicios de Azure, como Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
