---
title: 'Azure Security and Compliance Blueprint: Data Warehouse para la automatización de FedRAMP'
description: 'Azure Security and Compliance Blueprint: Data Warehouse para la automatización de FedRAMP'
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 110ce131286f437e051dd859eb4d2baa29f106f6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206718"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure Security and Compliance Blueprint: Data Warehouse para la automatización de FedRAMP

## <a name="overview"></a>Información general

[Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) es un programa a nivel del gobierno de los Estados Unidos que proporciona un enfoque estandarizado para la evaluación de la seguridad, la concesión de autorizaciones, y la supervisión continua de servicios y productos en la nube. Azure Security and Compliance Blueprint proporciona orientación sobre cómo entregar una arquitectura de almacenamiento de datos de Microsoft Azure que ayude a implementar un subconjunto de controles de FedRAMP High. Esta solución ofrece una guía sobre la implementación y la configuración de recursos de Azure para una arquitectura de referencia común, la cual muestra las formas en que los clientes pueden cumplir los requisitos específicos de seguridad y cumplimiento, y sirve como base para que los clientes compilen y configuren sus propias soluciones de almacén de datos en Azure.

Esta arquitectura de referencia, las guías de implementación de los controles asociados y los modelos de amenazas están diseñados como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción. Implementar una aplicación sin modificar en este entorno no es suficiente para satisfacer por completo los requisitos de la base de referencia de FedRAMP High. Tenga en cuenta lo siguiente:
- Esta arquitectura proporciona una base de referencia para ayudar a los clientes a implementar cargas de trabajo en Azure de manera compatible con FedRAMP.
- Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución integrada mediante esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura

Esta solución proporciona una arquitectura de referencia de almacén de datos que implementa un almacén de datos de alto rendimiento y seguro en la nube. Hay dos niveles de datos independientes en esta arquitectura: uno donde se importan los datos y se almacenan provisional y definitivamente en un entorno de SQL en clúster y otro para Azure SQL Data Warehouse donde se cargan los datos mediante una herramienta de extracción, transformación y carga de datos (por ejemplo, consultas T-SQL de [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) para su procesamiento. Una vez que los datos están almacenados en Azure SQL Data Warehouse, se pueden realizar análisis a gran escala.

Microsoft Azure ofrece una variedad de servicios de informes y análisis para el cliente. Esta solución incluye SQL Server Reporting Services (SSRS) para la rápida creación de informes desde Azure SQL Data Warehouse. Todo el tráfico SQL se cifra con SSL mediante la inclusión de certificados autofirmados. Como procedimiento recomendado, Azure recomienda el uso de una entidad de certificación de confianza para mejorar la seguridad.

En Azure SQL Data Warehouse, los datos se almacenan en tablas relacionales con almacenamiento en columnas, un formato que reduce significativamente los costos de almacenamiento de datos al tiempo que mejora el rendimiento de las consultas.  Según los requisitos de uso, los recursos de proceso de Azure SQL Data Warehouse se pueden escalar y reducir verticalmente o apagarse completamente si no hay procesos activos que necesiten recursos de proceso.

Un equilibrador de carga de SQL administra el tráfico SQL, lo cual garantiza un rendimiento alto. Todas las máquinas virtuales de esta arquitectura de referencia se implementan en un conjunto de disponibilidad con instancias de SQL Server configuradas en un grupo de disponibilidad AlwaysOn para las funcionalidades de alta disponibilidad y recuperación ante desastres.

Esta arquitectura de referencia de almacén de datos también incluye un nivel de Active Directory (AD) para la administración de recursos dentro de la arquitectura. La subred de Active Directory permite la fácil adopción en una estructura de bosque de AD mayor, lo que permite un funcionamiento continuo del entorno, aunque no esté disponible el acceso al bosque mayor. Todas las máquinas virtuales implementadas están unidas mediante dominio al nivel de Active Directory y usan directivas de grupo de Active Directory para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo.

Una máquina virtual sirve de host de tipo bastión de administración, lo cual proporciona una conexión segura para que los administradores accedan a los recursos implementados. Los datos se cargan en el área de ensayo a través de este host de tipo bastión de administración. **Azure recomienda configurar una conexión VPN o de Azure ExpressRoute para la administración y la importación de datos de en la subred de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de Data Warehouse para FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Diagrama de arquitectura de referencia de Data Warehouse para FedRAMP")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

Azure Virtual Machines
-   (1) Host de tipo bastión
-   (2) Controlador de dominio de Active Directory
-   (2) Nodo de clúster de SQL Server
-   (1) Testigo de SQL Server

Conjuntos de disponibilidad
-   (1) Controladores de dominio de Active Directory
-   (1) Testigo y nodos del clúster de SQL

Virtual Network
-   (4) Subredes
-   (4) Grupos de seguridad de red

SQL Data Warehouse

SQL Server Reporting Services

Equilibrador de carga de Azure SQL

Azure Active Directory

Almacén de Recovery Services

Azure Key Vault

Operations Management Suite (OMS)

## <a name="deployment-architecture"></a>Arquitectura de implementación

En la siguiente sección se detallan los elementos de desarrollo e implementación.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) es un almacenamiento de datos empresarial (EDW) en la nube empresarial que aprovecha el procesamiento paralelo masivo (MPP) para ejecutar rápidamente consultas complejas en petabytes de datos. Importe macrodatos en SQL Data Warehouse con consultas T-SQL de PolyBase simples y use la potencia de MPP para realizar análisis de alto rendimiento.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) permite la rápida creación de informes con tablas, gráficos, mapas, medidores, matrices y más para Azure SQL Data Warehouse.

**Host de tipo bastión**: el host de tipo bastión es el único punto de entrada que permite a los usuarios acceder a los recursos implementados en este entorno. El host de tipo bastión proporciona una conexión segura a los recursos implementados al permitir solo el tráfico remoto desde las direcciones IP públicas de una lista segura. Para permitir el tráfico de escritorio remoto (RDP), el origen del tráfico debe definirse en el grupo de seguridad de red (NSG).

Se ha creado una máquina virtual unidas mediante dominio como host de tipo bastión con las siguientes configuraciones:
-   [Extensión antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extensión de OMS](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensión de Diagnósticos de Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mediante Azure Key Vault (cumple los requisitos de Azure Government, PCI DSS, HIPAA y otros).
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

**Azure Storage** Para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos y sistema operativo. La solución se integra con Azure Key Vault para ayudar a controlar y administrar las claves de cifrado del disco.

**Azure SQL Database** La instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   La [auditoría de bases de datos SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
-   SQL Database está configurado para usar el [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado de archivos de datos y de registro en tiempo real para proteger la información en reposo. TDE garantiza que los datos almacenados no hayan estado sujetos a un acceso no autorizado.
-   Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   La [detección de amenazas de SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar y responder ante posibles amenazas a medida que se producen, mediante el envío de alertas de seguridad para actividades sospechosas en la base de datos, posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas de Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos confidenciales nunca aparecen como texto no cifrado dentro del sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
-   El [enmascaramiento de los datos dinámicos de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) puede realizarse después de la implementación de la arquitectura de referencia. Los clientes deberán ajustar la configuración del enmascaramiento de los datos dinámicos para ajustarse al esquema de la base de datos.

### <a name="business-continuity"></a>Continuidad del negocio
**Alta disponibilidad**: las cargas de trabajo del servidor se agrupan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ayudar a garantizar la alta disponibilidad de las máquinas virtuales en Azure. Durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual está disponible para cumplir el 99,95 % del Acuerdo de Nivel de Servicio de Azure.

**Almacén de Recovery Services**: el [almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) aloja datos de copia de seguridad y protege todas las configuraciones de Azure Virtual machines en esta arquitectura. Con los almacenes de Recovery Services, los clientes pueden restaurar archivos y carpetas desde una máquina virtual de IaaS sin tener que restaurar toda la máquina virtual, lo que permite unos tiempos de restauración más rápidos.

### <a name="logging-and-audit"></a>Registro y auditoría
[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) proporciona un registro completo de la actividad de usuario y del sistema, así como mantenimiento del sistema. La solución [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de OMS recopila y analiza los datos generados por los recursos en los entornos locales o en Azure.
- **Registros de actividad:** los [registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción.
- **Registros de diagnóstico:** los [registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos incluyen los registros del sistema de eventos de Windows y los registros de Azure Blob Storage, tablas y cola.
- **Registros de firewall:** Application Gateway proporciona registros de diagnóstico y acceso completos. Los registros de firewall están disponibles para los recursos de Application Gateway con WAF habilitado.
- **Archivado de registros**: todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para que queden archivados durante un período de retención definido de dos días. Esos registros se conectan a Azure Log Analytics para el procesamiento, el almacenamiento y la creación de informes de panel.

Además, como parte de esta arquitectura, se incluyen las siguientes soluciones de OMS:
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): la solución de antimalware notifica sobre el estado de malware, las amenazas y la protección.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): la solución Azure Automation almacena, ejecuta y administra runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): el panel Security and Audit proporciona una visión de alto nivel del estado de seguridad de los recursos al proporcionar información sobre métricas de los dominios de seguridad, problemas importantes, detecciones, inteligencia sobre amenazas y consultas comunes de seguridad.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): la solución Update Management permite la administración de cliente de las actualizaciones de seguridad del sistema operativo, que incluye el estado de las actualizaciones disponibles y el proceso de instalación de las actualizaciones necesarias.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Registros de actividad de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Change Tracking permite a los clientes identificar fácilmente los cambios en el entorno.

### <a name="identity-management"></a>Administración de identidades
Las siguientes tecnologías proporcionan funcionalidades de administración de identidad en el entorno de Azure:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) puede ser el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios de la solución se crearon en Azure Active Directory, incluidos los usuarios que acceden a SQL Database.
-   La autenticación para acceder a la aplicación se realiza con Azure AD. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de la columna de base de datos usa Azure AD para autenticar la aplicación en Azure SQL Database. Para más información, consulte cómo [proteger los datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.
-   El [control de acceso basado en rol (RBAC) de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite administrar el control de acceso a Azure. El acceso a la suscripción está limitado al administrador de la suscripción.

Para obtener más información sobre el uso de las características de seguridad de Azure SQL Database, consulte el ejemplo de la [aplicación de demostración de Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Seguridad
**Administración de secretos**: la solución utiliza [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube.

**Protección contra malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para Virtual Machines proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.

**Administración de revisiones**: las máquinas virtuales Windows implementadas como parte de esta arquitectura de referencia se configuran de forma predeterminada para recibir actualizaciones automáticas desde Windows Update Service. Esta solución también incluye el servicio [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) de OMS, mediante el cual se pueden crear implementaciones actualizadas de las máquinas virtuales cuando sea necesario.


## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="expressroute-and-vpn"></a>ExpressRoute y VPN
Debe configurarse [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o un túnel VPN seguro para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de almacén de datos. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Al configurar ExpressRoute o una VPN adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

### <a name="extract-transform-load-etl-process"></a>Proceso de extracción, transformación y carga de datos (ETL)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) puede cargar datos Azure SQL Data Warehouse sin necesidad de una herramienta ETL o de importación independiente. PolyBase permite el acceso a los datos mediante consultas T-SQL. Con PolyBase se puede usar la inteligencia empresarial y la pila de análisis de Microsoft, así como herramientas de terceros compatibles con SQL Server.

### <a name="azure-active-directory-setup"></a>Configuración de Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es esencial para la administración de la implementación y la concesión de acceso a las personas que interactúan con el entorno. Se puede integrar una instancia de Windows Server Active Directory con AAD en [cuatro clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Los clientes también pueden enlazar la infraestructura de Active Directory implementada (controladores de dominio) con una instancia de AAD existente al convertir la infraestructura de Active Directory implementada en un subdominio de un bosque de AAD.

### <a name="additional-services"></a>Servicios adicionales
Aunque esta arquitectura de almacenamiento de datos no está diseñada para su implementación en el entorno [comercial de Azure](https://azure.microsoft.com/overview/what-is-azure/), se pueden lograr objetivos similares a través de los servicios que se describen en esta arquitectura de referencia, así como de otros servicios disponibles solo en el entorno comercial de Azure. Tenga en cuenta que el entorno comercial de Azure mantiene FedRAMP JAB P-ATO en el nivel de impacto moderado, lo cual permite a los organismos gubernamentales y los asociados implementar información moderadamente confidencial en la nube aprovechando el entorno comercial de Azure.

El entorno comercial de Azure ofrece una amplia variedad de servicios que usan almacenamiento provisional y definitivo de datos con y sin formato, por ejemplo:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) es un servicio en la nube administrado creado para complejos proyectos híbridos de extracción, transformación y carga (ETL), extracción, carga y transformación (ELT) e integración de datos. Con Azure Data Factory, los clientes pueden crear y programar flujos de trabajo basados en datos (llamados canalizaciones) que ingieren datos de distintos almacenes de datos. Después, pueden procesar y transformar los datos para la salida a almacenes de datos como Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos. Azure Data Lake Store es compatible con la mayoría de componentes de código abierto del ecosistema Hadoop y se integra perfectamente con otros servicios de Azure, como Azure SQL Data Warehouse.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos (DFD) de esta arquitectura de referencia está disponible para [descarga](https://aka.ms/blueprintdwthreatmodel) y se encuentra a continuación:

![Data Warehouse para el modelo de amenazas de FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Data Warehouse para el modelo de amenazas de FedRAMP")

## <a name="compliance-documentation"></a>Documentación de cumplimiento
En la [matriz de responsabilidades de los clientes de FedRAMP High de Azure Security and Compliance Blueprint](https://aka.ms/blueprinthighcrm) se enumeran todos los controles de seguridad necesarios para la base de referencia de FedRAMP High. Análogamente, en la [matriz de responsabilidades de los clientes de FedRAMP Moderate de Azure Security and Compliance Blueprint](https://aka.ms/blueprintcrmmod) se enumeran todos los controles de seguridad necesarios para la base de referencia de FedRAMP Moderate. Ambos documentos incluyen información detallada sobre si la implementación de cada control es la responsabilidad de Microsoft,d el cliente o de ambos.

La [matriz de responsabilidades de los clientes de FedRAMP High de Azure Security and Compliance Blueprint](https://aka.ms/blueprintdwcimhigh) y la [matriz de responsabilidades de los clientes de FedRAMP Moderate de Azure Security and Compliance Blueprint](https://aka.ms/blueprintdwcimmod) proporcionan información sobre los controles cubiertos por la arquitectura de almacenamiento de datos para cada base de referencia de FedRAMP, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada control cubierto.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
