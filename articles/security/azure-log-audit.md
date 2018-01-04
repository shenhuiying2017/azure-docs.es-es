---
title: "Registro y auditoría de Azure | Microsoft Docs"
description: "Aprenda a usar datos de registro para obtener un conocimiento más profundo sobre su aplicación."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 032aa4a6cedd49ff9c3b4803561b8b187e8f9af5
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="azure-logging-and-auditing"></a>Registro y auditoría de Azure
## <a name="introduction"></a>Introducción
### <a name="overview"></a>Información general
Para ayudar a los clientes de Azure actuales y potenciales a entender y utilizar las diferentes funcionalidades de seguridad disponibles en la plataforma Azure y relacionadas con ella, Microsoft ha desarrollado una serie de notas del producto, información general de seguridad, procedimientos recomendados y listas de comprobación. Los temas varían en extensión y detalle, y se actualizan periódicamente. Este documento forma parte de esa serie, como se resume en la breve descripción de la siguiente sección.
### <a name="azure-platform"></a>Plataforma Azure
Azure es una plataforma de servicios en la nube abierta y flexible que admite la más amplia selección de sistemas operativos, lenguajes de programación, marcos, herramientas, bases de datos y dispositivos.

Por ejemplo, puede:
-   Ejecutar contenedores de Linux con integración de Docker.

-   Compilar aplicaciones con JavaScript, Python, .NET, PHP, Java y Node.js.

-   Crear back-end para dispositivos iOS, Android y Windows.

Los servicios en la nube pública de Azure admiten las mismas tecnologías en las que ya confían millones de desarrolladores y profesionales de TI.

Al crear o migrar recursos de TI a un proveedor de nube, confía en las capacidades de la organización para proteger las aplicaciones y los datos con los servicios y los controles que facilitan para administrar la seguridad de sus recursos en la nube.

La infraestructura de Azure está diseñada desde la instalación hasta las aplicaciones para hospedar millones de clientes simultáneamente, y proporciona una base de confianza en la que las empresas pueden satisfacer sus necesidades de seguridad. Además, Azure ofrece una amplia gama de opciones de seguridad configurables, así como la posibilidad de controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos únicos de sus implementaciones. Este documento le ayuda a cumplir estos requisitos.

### <a name="abstract"></a>Descripción breve
La auditoría y el registro de eventos y alertas relacionados con la seguridad son componentes importantes en una estrategia de protección de datos eficaz. Los registros e informes de seguridad proporcionan un registro electrónico de actividades sospechosas y le ayudan a detectar patrones que pueden indicar una penetración externa de la red, intentada o lograda, así como ataques internos. Puede utilizar la auditoría para supervisar la actividad de usuario y el cumplimiento normativo de documentos, realizar análisis forenses y mucho más. Las alertas proporcionan notificación inmediata cuando se producen eventos de seguridad.

Los productos y servicios de Microsoft Azure proporcionan opciones de seguridad de registro y auditoría de seguridad que le ayudan a identificar carencias en sus directivas y mecanismos de seguridad y a resolver esas carencias para evitar infracciones. Los servicios de Microsoft ofrecen algunas de las siguientes opciones (y en algunos casos, todas): sistemas centralizados de supervisión, registro y análisis para proporcionar una visibilidad continua; alertas oportunas e informes para ayudarle a administrar la gran cantidad de información generada por dispositivos y servicios.

Los datos de registro de Microsoft Azure se pueden exportar a sistemas de incidentes de seguridad y administración de eventos (SIEM) para su análisis y se integran en soluciones de auditoría de terceros.

Estas notas del producto proporcionan una introducción a la generación, recopilación y análisis de los registros de seguridad de los servicios hospedados en Azure, y pueden ayudarle a conocer más detalladamente la seguridad de sus implementaciones de Azure. El ámbito de estas notas del producto se limita a las aplicaciones y servicios compilados e implementados en Azure.

> [!Note]
> Algunas de las recomendaciones de este artículo pueden provocar un aumento del uso de datos, de la red o de recursos de proceso, lo que incrementará los costes de las licencias o suscripciones.

## <a name="types-of-logs-in-azure"></a>Tipos de registros de Azure
Las aplicaciones de nube son complejas y tienen muchas partes móviles. Los registros proporcionan datos para garantizar que la aplicación permanece en funcionamiento en un estado correcto. También ayuda a evitar posibles problemas o a solucionar los existentes. Además, puede usar datos de registro para obtener un conocimiento más profundo sobre su aplicación. Este conocimiento puede ayudarle a mejorar el rendimiento o mantenimiento de la aplicación, o a automatizar acciones que de lo contrario requerirían intervención manual.

Azure genera gran cantidad de registros para cada servicio. Estos registros se clasifican en estos tipos principales:
-   **Registros de control/administración**, que ofrecen visibilidad sobre las operaciones CREATE, UPDATE y DELETE de Azure Resource Manager. Los [registros de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) son un ejemplo de este tipo de registro.

-   **Registros de plano de datos**, que ofrecen visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Ejemplos de este tipo de registro son los registros de sistema, seguridad y aplicaciones de eventos de Windows en una máquina virtual, así como los [registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) que se han configurado mediante Azure Monitor.


-   **Eventos procesados**, que aportan información sobre eventos y alertas analizados que se han procesado en su nombre. Ejemplos de este tipo son las [alertas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), donde [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) ha procesado y analizado su suscripción, y proporciona unas alertas de seguridad concisas.

En la tabla siguiente se enumeran el tipo más importante de registros disponibles en Azure.

| Categoría del registro | Tipo de registro | Usos | Integración |
| ------------ | -------- | ------ | ----------- |
|[Registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Eventos de plano de control de los recursos de Azure Resource Manager|   Proporcionan información detallada sobre las operaciones que se realizaron en los recursos de su suscripción.| API de REST y [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Registros de diagnóstico de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Datos frecuentes acerca del funcionamiento de los recursos de Azure Resource Manager de la suscripción| Proporcionan información detallada sobre las operaciones que el mismo recurso realiza.| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Informes de AAD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Registros e informes|Actividades de inicio de sesión de usuario e información de actividades del sistema acerca de la administración de grupos y usuarios|[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Máquina virtual y Cloud Services](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Registro de eventos de Windows y Syslog de Linux|    Captura los datos del sistema y los datos de registro en las máquinas virtuales, y transfiere estos datos a la cuenta de almacenamiento que elija.|   Windows con [WAD](https://docs.microsoft.com/azure/azure-diagnostics) (almacenamiento de Diagnósticos de Microsoft Azure) y Linux en Azure Monitor|
|[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Registro de almacenamiento y proporciona datos de métricas para una cuenta de almacenamiento.|Proporciona información detallada sobre seguimiento de solicitudes, análisis de tendencias de uso y diagnóstico de problemas con la cuenta de almacenamiento.|    API de REST o [biblioteca de cliente](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Registros de flujos de NSG (grupo de seguridad de red)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON y muestra flujos entrantes y salientes por cada regla|Consulte información sobre el tráfico IP de entrada y salida a través de un grupo de seguridad de red|[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Registros, excepciones y diagnósticos personalizados|    Servicio de Application Performance Management (APM) para desarrolladores web en varias plataformas.| API de REST, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Datos de proceso/alerta de seguridad| Alerta de Azure Security Center, alerta de OMS| Información y alertas de seguridad|   API de REST, JSON|

### <a name="activity-log"></a>Registro de actividad
El [registro de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporciona información sobre las operaciones que se realizaron en los recursos de su suscripción. El registro de actividad se conocía antes como "Registros de auditoría" o "Registros operativos", ya que notifica [eventos del plano de control](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) para las suscripciones. Con el registro de actividad, se pueden determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) realizadas en los recursos de la suscripción. También puede conocer el estado de la operación y otras propiedades relevantes. El registro de actividad no incluye las operaciones de lectura (GET).

Aquí PUT, POST, DELETE hacen referencia a todas las operaciones de escritura que el registro de actividad contiene en los recursos. Por ejemplo, puede usar los registros de actividad para encontrar errores al solucionar problemas o para supervisar cómo ha modificado un recurso un usuario de su organización.

![Registro de actividad](./media/azure-log-audit/azure-log-audit-fig1.png)


Puede recuperar los eventos del registro de actividad mediante Azure Portal, la [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), los cmdlets de PowerShell y la [API de REST de Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Los registros de actividad tienen un período de retención de datos de 19 días.

Escenarios de integración
-   [Crear una alerta de correo electrónico o webhook que se desencadene con un evento de registro de actividad.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Transmitirlo a un centro de eventos ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) para ingestión en un servicio de terceros o una solución de análisis personalizada como PowerBI.

-   Analizarlo en PowerBI con el [paquete de contenido de PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

-   [Guardarlo en una cuenta de almacenamiento para archivarlo o inspeccionarlo manualmente](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Puede especificar el tiempo de retención (en días) mediante perfiles de registro.

-   Verlo y realizar consultas en él en Azure Portal.

-   Consultarlo mediante un cmdlet de PowerShell, la CLI o la API de REST.

-   Exportar el registro de actividad con perfiles de registro a [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Puede usar una cuenta de almacenamiento o un [espacio de nombres de centro de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) que no esté en la misma suscripción que el que emite los registros. El usuario que configura los ajustes debe tener el acceso de [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) adecuado a ambas suscripciones.
### <a name="azure-diagnostic-logs"></a>Registros de diagnóstico de Azure
Un recurso emite registros de diagnóstico de Azure que proporcionan datos exhaustivos y frecuentes acerca del funcionamiento de ese recurso. El contenido de estos registros varía según el tipo de recurso (por ejemplo, los [registros del sistema de eventos de Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) son una categoría de registro de diagnóstico para máquinas virtuales, mientras que los [registros de blob, tabla y cola](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) son categorías para cuentas de almacenamiento). Se diferencian del registro de actividad en que proporcionan información sobre las operaciones realizadas en recursos de su suscripción.

![Registros de diagnóstico de Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Los registros de diagnóstico de Azure ofrecen varias opciones de configuración: Azure Portal, con PowerShell, la interfaz de línea de comandos (CLI) y API de REST.

**Escenarios de integración**
-   Guardarlos en una [cuenta de almacenamiento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) para archivarlos o inspeccionarlos manualmente. Puede especificar el tiempo de retención (en días) mediante la configuración de diagnóstico.

-   [Transmitirlos Event Hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) para la ingestión en un servicio de terceros o una solución de análisis personalizado como [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

-   Analizarlos con [Log Analytics de OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Servicios admitidos, esquema para registros de diagnóstico y categorías de registro admitidas por tipo de recurso**


| Servicio | Esquema y documentos | Tipo de recurso | Categoría |
| ------- | ------------- | ------------- | -------- |
|Load Balancer| [Análisis del registros para el Equilibrador de carga de Azure (vista previa)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Grupos de seguridad de red|[Análisis del registro para grupos de seguridad de red (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Puertas de enlace de aplicaciones|[Registro de diagnóstico para la Puerta de enlace de aplicaciones](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Registro de Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Habilitación y uso de Análisis de tráfico de búsqueda](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Almacén de Data Lake|[Acceso a los registros de diagnóstico de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Auditoría|
|Data Lake Analytics|[Acceso a los registros de diagnóstico de Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Auditoría|
|||Microsoft.DataLakeAnalytics/accounts|Requests|
|||Microsoft.DataLakeStore/accounts|Requests|
|Logic Apps|[Esquema de seguimiento personalizado de Logic Apps B2B](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Registros de diagnósticos de Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Log Analytics para Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Registros de diagnóstico de Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Registros de diagnósticos de trabajos](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Ejecución|
|||Microsoft.StreamAnalytics/streamingjobs|Creación|
|Azure Service Bus|[Registros de diagnóstico de Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Informes de Azure Active Directory
Azure Active Directory (Azure AD) incluye informes de seguridad, actividad y auditoría para el directorio. El [informe de auditoría de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ayuda a los clientes a identificar las acciones con privilegios que se produjeron en su entorno de Azure Active Directory. Las acciones con privilegios incluyen cambios de elevación (por ejemplo, creación de roles o restablecimientos de contraseña), cambios de configuraciones de directiva (por ejemplo, directivas de contraseña) o cambios de configuración de directorio (por ejemplo, cambios en la configuración de la federación de dominio).

Los informes proporcionan el registro de auditoría para el nombre del evento, el actor que realizó la acción, el recurso de destino que se ve afectado por el cambio y la fecha y hora (en UTC). Los clientes pueden recuperar la lista de eventos de auditoría de su entorno de Azure Active Directory desde [Azure Portal](https://portal.azure.com/), como se describe en [Visualización de los registros de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Esta es una lista de los informes incluidos:

| Informes de seguridad | Informes de actividad | Informes de auditoría |
| :--------------- | :--------------- | :------------ |
|Inicios de sesión desde orígenes desconocidos| Uso de la aplicación: resumen| Informe de auditoría de directorio|
|Inicios de sesión tras varios errores|  Uso de la aplicación: detallado||
|Inicios de sesión desde varias ubicaciones geográficas|    Panel de la aplicación||
|Inicios de sesión desde direcciones IP con actividad sospechosa|   Errores de aprovisionamiento de cuentas||
|Actividad de inicio de sesión irregular|    Dispositivos de usuario individuales||
|Inicios de sesión desde dispositivos posiblemente infectados|   Actividad de usuario individual||
|Usuarios con actividad de inicio de sesión anómala| Informe de actividad de grupos||
||Informe de actividad de registro de restablecimiento de contraseña||
||Actividad de restablecimiento de contraseña|||

Los datos de estos informes pueden resultar útiles para las aplicaciones, como sistemas SIEM, auditorías y herramientas de inteligencia empresarial. Las API de generación de informes de Azure AD proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Se puede llamar a estas [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) desde diversos lenguajes de programación y herramientas.

Los eventos del informe de auditoría de Azure AD se conservan durante 180 días.

> [!Note]
> Para más información sobre la retención de los informes, consulte [Directivas de retención de informes de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Para los clientes interesados en almacenar eventos de auditoría durante períodos de retención más largos, se puede utilizar Reporting API para extraer periódicamente los [eventos de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) en un almacén de datos independiente.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Registros de máquinas virtuales que usan Diagnósticos de Azure
[Diagnósticos de Azure](https://docs.microsoft.com/azure/azure-diagnostics) es la funcionalidad de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede utilizar la extensión de diagnóstico desde varios orígenes distintos. Actualmente se admiten [roles web y de trabajo del servicio en la nube de Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Registros de máquinas virtuales que usan Diagnósticos de Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

[Azure Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) con Microsoft Windows y [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Puede habilitar el Diagnóstico de Azure en una máquina virtual mediante las opciones siguientes:

-   Si utiliza Visual Studio, consulte el artículo sobre el [uso de Visual Studio para rastrear Azure Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines).

-   [Configuración de Diagnósticos de Azure en Azure Virtual Machine de forma remota](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Uso de PowerShell para configurar los diagnósticos en Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante una plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento. El registro de Storage Analytics está disponible para los [servicios Blob, Queue y Table service](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics registra información detallada sobre las solicitudes correctas y erróneas realizadas a un servicio de almacenamiento.

Esta información se puede utilizar para supervisar solicitudes concretas y para diagnosticar problemas con un servicio de almacenamiento. Las solicitudes se registran en función de la mejor opción. Las entradas del registro se crean solo si se presentan solicitudes al punto de conexión de servicio. Por ejemplo, si una cuenta de almacenamiento tiene actividad en el punto de conexión de Blob service pero no en los puntos de conexión de Table o Queue service, solo se crean los registros correspondientes a Blob service.

Para utilizar Storage Analytics, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo en [Azure Portal](https://portal.azure.com/); para más información, consulte [Supervisión de una cuenta de almacenamiento en Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). También puede habilitar Storage Analytics mediante programación a través de la API de REST o la biblioteca de cliente. Use la operación Set Service Properties para habilitar Storage Analytics de forma individual para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API de Blob service y de Table service.

Storage Analytics tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Todos los registros se almacenan en [blobs en bloques](https://docs.microsoft.com/azure/storage/storage-analytics) en un contenedor denominado $logs, que se crea automáticamente cuando se habilita Storage Analytics para una cuenta de almacenamiento.

> [!Note]
> Para más información sobre las directivas de facturación y retención de datos, consulte [Storage Analytics and Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing) (Storage Analytics y facturación).
>
> [!Note]
> Para más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Se registran los siguientes tipos de solicitudes autenticadas y anónimas.



| Autenticadas  | Anónimas|
| :------------- | :-------------|
| Solicitudes correctas | Solicitudes correctas |
|Solicitudes erróneas, incluidos errores de tiempo de espera, de limitación, de red, de autorización y de otro tipo | Solicitudes que usan una firma de acceso compartido (SAS), incluidas las correctas y las erróneas |
| Solicitudes que usan una firma de acceso compartido (SAS), incluidas las correctas y las erróneas |Errores de tiempo de espera para el cliente y el servidor |
|   Solicitudes de datos de análisis |    Solicitudes GET erróneas con el código de error 304 (No modificado) |
| Las solicitudes realizadas por el propio Storage Analytics, como la creación o eliminación del registro, no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) y [Formato del registro de Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | El resto de solicitudes anónimas erróneas no se registran. Puede encontrar una lista completa de los datos registrados en [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Operaciones registradas y mensajes de estado de Storage Analytics) y [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Formato de registro de Storage Analytics). |

### <a name="azure-networking-logs"></a>Registros de redes de Azure
El registro y supervisión de redes en Azure es completa y cubre dos amplias categorías:

-   [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): este servicio ofrece las características de supervisión de redes basada en el escenario. Este servicio incluye captura de paquetes, próximo salto, comprobación de flujo de IP, vista de grupos de seguridad y registros de flujo de NSG. La supervisión en el nivel de escenario, ofrece una visión global de los recursos de la red que contrasta con la supervisión de recursos de red individuales.

-   [Supervisión de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) - la supervisión en el nivel de recurso consta de cuatro funciones: registros de diagnóstico, métricas, solución de problemas y mantenimiento de recursos. Todas estas funciones se integran en el nivel de recursos de red.

![Registros de redes de Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red mediante Azure. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure.

**Registros de flujos de NSG**: los registros de flujos de los grupos de seguridad de red permiten capturar registros relacionados con el tráfico que están permitidos o no por las reglas de seguridad del grupo. Estos registros de flujos se escriben en formato JSON y muestran los flujos de entrada y de salida en función de cada regla, la NIC a la que se aplica el flujo, información de 5-tupla sobre el flujo (IP de origen/destino, puerto de origen/destino, protocolo), y si se permitió o denegó el tráfico.

### <a name="network-security-group-flow-logging"></a>Registro de flujos de grupos de seguridad de red

Los [registros de flujos de grupos de seguridad de red](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) son una característica de Network Watcher que permite consultar información acerca del tráfico IP de entrada y de salida en un grupo de seguridad de red. Estos registros de flujos se escriben en formato JSON y muestran los flujos de entrada y de salida en función de cada regla, la NIC a la que se aplica el flujo, información de 5-tupla sobre el flujo (IP de origen/destino, puerto de origen/destino, protocolo), y si se permitió o denegó el tráfico.

Aunque los registros de flujo tienen como objetivo los grupos de seguridad de red, no se muestran como los demás registros. Los registros de flujos solo se almacenan en una cuenta de almacenamiento.

Las mismas directivas de retención vistas en otros registros se aplican a los registros de flujo. Los registros tienen una directiva de retención que se puede establecer entre 1 y 365 días. Si no se establece una directiva de retención, los registros se mantendrán indefinidamente.

**Registros de diagnóstico**

Los recursos de red crean eventos periódicos y espontáneos y estos se registran en las cuentas de almacenamiento, se envían a un centro de eventos o a Log Analytics. Estos registros proporcionan información acerca del estado de un recurso. Estos registros se pueden ver en herramientas como Power BI y Log Analytics. Para aprender a ver registros de diagnóstico, visite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Registros de diagnóstico](./media/azure-log-audit/azure-log-audit-fig5.png)

Los registros de diagnóstico están disponibles para el [equilibrador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), las rutas y [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher proporciona una vista de los registros de diagnóstico. Esta vista contiene todos los recursos de las redes que admiten el registro de diagnóstico. Desde esta vista, puede habilitar y deshabilitar los recursos de red de forma cómoda y rápida.


Además de las funcionalidades de registro anteriores, Network Watcher tiene actualmente las siguientes funcionalidades:
- [Topología](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): proporciona una vista de nivel de red que muestra las diversas interconexiones y asociaciones entre los recursos de red de un grupo de recursos.

- [Captura de paquetes variable](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): captura datos de paquetes dentro y fuera de una máquina virtual. Opciones de filtrado avanzadas y controles optimizados con los que se pueden establecer límites de tiempo y de tamaño, lo cual proporciona una gran versatilidad. Los datos de paquetes se pueden almacenar en un almacén de blobs o en el disco local en formato .cap.

-   [Comprobaciones de flujo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): comprueba si un paquete está permitido o no en función de los parámetros de paquete de información de 5-tupla sobre el flujo (IP de destino, IP de origen, puerto de destino, puerto de origen y protocolo). Si un grupo de seguridad deniega un paquete, se devuelve el nombre de la regla y del grupo que lo deniega.

-   [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): determina el próximo salto para los paquetes que se enrutan en el tejido de red de Azure, lo que le permite diagnosticar cualquier ruta definida por el usuario que se haya configurado incorrectamente.

-   [Vista de grupos de seguridad](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): obtiene las reglas de seguridad eficaces que se aplican en una máquina virtual.

-   [Solución de problemas de la puerta de enlace de Virtual Network y de las conexiones](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): ofrece la capacidad para solucionar problemas con las puertas de enlace de Virtual Network y las conexiones.

-   [Límites de suscripción de red](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): le permite ver el uso de los recursos de la red en comparación con los límites.

### <a name="application-insight"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) es un servicio de Application Performance Management (APM) extensible para desarrolladores web en varias plataformas. Úselo para supervisar la aplicación web en directo. Se detectan automáticamente las anomalías de rendimiento. Incluye herramientas de análisis eficaces que le ayudan a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación.

 Está diseñado para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso.

 Funciona con diversas aplicaciones y en una amplia variedad de plataformas, como .NET, Node.js o J2EE, tanto hospedadas localmente como en la nube. Se integra con el proceso de devOps y tiene puntos de conexión para diversas herramientas de desarrollo.

![Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Application Insights está dirigido al equipo de desarrollo y sirve ayudarle a conocer el rendimiento de una aplicación y cómo se utiliza. Supervisa:

-   **Tasas de solicitud, tiempos de respuesta y tasas de error** - Averigüe qué páginas que son las más conocidas, en qué momento del día y dónde están los usuarios. Vea qué páginas presentan mejor rendimiento. Si los tiempos de respuesta y las tasas de error aumentan cuando hay más solicitudes, quizás tiene un problema de recursos.

-   **Tasas de dependencia, tiempos de respuesta y tasa de error** - Averigüe si los servicios externos le ralentizan.

-   **Excepciones**: - Analice las estadísticas agregadas o seleccione instancias concretas y profundice en el seguimiento de la pila y las solicitudes relacionadas. Se notifican tanto las excepciones de servidor como las de explorador.

-   **Vistas de página y rendimiento de carga** - Notificados por los exploradores de los usuarios.

-   **Llamadas AJAX** desde páginas web - Tasas, tiempos de respuesta y tasas de error.

-   **Número de usuarios y sesiones**.

-   **Contadores de rendimiento** de las máquinas de servidor de Windows o Linux, como CPU, memoria y uso de la red.

-   **Diagnóstico de host** de Docker o Azure.

-   **Registros de seguimiento de diagnóstico** de la aplicación - De esta forma puede correlacionar eventos de seguimiento con las solicitudes.

-   **Métricas y eventos personalizados** que usted mismo escribe en el código de cliente o servidor para realizar un seguimiento de eventos empresariales, como artículos vendidos o partidas ganadas.

**Lista de escenarios de integración y descripción:**

| Escenarios de integración | DESCRIPCIÓN |
| --------------------- | :---------- |
|[Mapa de aplicación](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Los componentes de la aplicación, con alertas y métricas clave.||
|[Búsqueda de diagnóstico para datos de instancia](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Busque y filtre eventos como solicitudes, excepciones, llamadas de dependencia, seguimientos de registro y vistas de páginas.||
|[Explorador de métricas para datos agregados](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Explore, filtre y segmente datos agregados, como los índices de solicitudes, errores y excepciones; los tiempos de respuesta y los tiempos de carga de página.||
|[Paneles](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Combine datos de varios recursos y compártalos con otros. Ideal para aplicaciones de varios componentes y para la presentación continua en la sala de reuniones.||
|[Secuencia de métricas en directo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Al implementar una nueva compilación, fíjese en estos indicadores de rendimiento casi en tiempo real para asegurarse de que todo funciona según lo esperado.||
|[Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Responda preguntas complejas acerca del uso y el rendimiento de su aplicación mediante este eficaz lenguaje de consulta.||
|[Alertas automáticas y manuales](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Las alertas automáticas se adaptan a los patrones normales de telemetría de la aplicación y se desencadenan cuando algo no responde al patrón habitual. También puede establecer alertas sobre niveles de métricas estándares o personalizadas.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Vea los datos de rendimiento en el código. Vaya al código desde los seguimientos de la pila.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integre métricas de uso con otra inteligencia empresarial.||
|[API de REST](https://dev.applicationinsights.io/)|Escriba código para ejecutar consultas sobre las métricas y los datos sin procesar.||
|[Exportación continua](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Exportación masiva de datos sin procesar al almacenamiento cuando llegan.||

### <a name="azure-security-center-alerts"></a>Alertas de Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque.

La detección de amenazas de Security Center recopila automáticamente información de seguridad de sus recursos de Azure, de la red y de soluciones de asociados relacionadas. Después, analiza estos datos (a menudo, relacionando la información de diferentes orígenes) para identificar las amenazas. En Security Center, las alertas de seguridad están clasificadas por prioridad y se incluyen recomendaciones para solucionar la amenaza.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

Security Center utiliza análisis avanzados que superan con creces los enfoques basados en firmas. Los grandes avances registrados en las tecnologías de grandes datos y [aprendizaje automático](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) se aplican para evaluar eventos en todo el tejido de la nube, lo que permite detectar amenazas que no se podrían identificar mediante enfoques manuales, así como predecir la evolución de los ataques. Estas técnicas de análisis son:

-   **Información integrada sobre amenazas**: busca actores malintencionados conocidos aplicando la información global sobre amenazas de los productos y servicios de Microsoft, la Unidad de crímenes digitales de Microsoft (DCU), Microsoft Security Response Center (MSRC) y otras fuentes externas.

-   **Análisis del comportamiento**: aplica patrones conocidos para detectar comportamientos malintencionados.

-   **Detección de anomalías**: utiliza la generación de perfiles estadísticos para crear una base histórica de referencia. Alerta de las desviaciones de las referencias que se ajustan a un posible vector de ataque.


Muchos equipos de respuesta a incidentes y operaciones de seguridad confían en una solución de administración de eventos (SIEM) e información de seguridad como punto de partida para clasificar e investigar alertas de seguridad. Gracias a la integración de registro de Azure, los clientes pueden sincronizar alertas de Azure Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM casi en tiempo real.


## <a name="log-analytics"></a>Log Analytics

Log Analytics es un servicio de [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) que le ayuda a recopilar y analizar los datos generados por los recursos en los entornos locales o en la nube. Proporciona información en tiempo real mediante el uso de paneles personalizados y de búsqueda integrados para analizar fácilmente millones de registros en todas las cargas de trabajo y los servidores, independientemente de su ubicación física.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

En el centro de Log Analytics se encuentra el repositorio de OMS, que está hospedado en la nube de Azure. Los datos se recopilan en el repositorio desde los orígenes conectados mediante la configuración de orígenes de datos y la incorporación de soluciones a la suscripción. Cada uno de los orígenes de datos y soluciones creará tipos de registros distintos que tendrán su propio conjunto de propiedades pero, de todos modos, podrían seguirse analizando en conjunto en consultas al repositorio. Esto le permite usar las mismas herramientas y los mismos métodos para trabajar con distintas variantes de datos recopilados por distintos orígenes.

Los orígenes conectados son los equipos y otros recursos que generan los datos que recopila Log Analytics. Esto puede incluir agentes instalados en equipos [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) y [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) que se conectan directamente o agentes en un [grupo de administración de System Center Operations Manager conectado](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics también puede recopilar datos desde [Azure Storage](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[orígenes de datos](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) son las distintas variantes de datos que se recopilan desde cada origen conectado. Esto incluye eventos y [datos de rendimiento](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) de agentes de [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) y Linux, además de orígenes como [registros de IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) y [registros de texto personalizado](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Usted configura cada origen de datos que desea recopilar y la configuración se entrega automáticamente a cada origen conectado.

Hay cuatro maneras diferentes de [recopilar registros y métricas para servicios de Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
1.  Diagnósticos de Azure directos a Log Analytics (Diagnósticos en la tabla siguiente)

2.  Diagnósticos de Azure a Azure Storage y luego a Log Analytics (Almacenamiento en la tabla siguiente)

3.  Conectores para servicios de Azure (Conectores en la tabla siguiente)

4.  Scripts para recopilar y después publicar datos en Log Analytics (espacios en blanco en la tabla siguiente y para servicios que no aparecen)

| Servicio | Tipo de recurso | Registros | Métricas | Solución |
| :------ | :------------ | :--- | :------ | :------- |
|Puertas de enlace de aplicaciones|  Microsoft.Network/<br>applicationGateways|  Diagnóstico|Diagnóstico|    [Azure Application](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)[Gateway Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Conector|  Conector|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)[Connector (versión preliminar)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Cuentas de Automation|   Microsoft.Automation/<br>AutomationAccounts|    Diagnóstico||       [Más información](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Cuentas de Batch|    Microsoft.Batch/<br>batchAccounts|  Diagnóstico|    Diagnóstico||
|Servicios en la nube clásica||       Storage||       [Más información](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Diagnóstico|||
|Data Lake Analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   Diagnóstico|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>accounts|   Diagnóstico|||
|Espacio de nombres del Centro de eventos|   Microsoft.EventHub/<br>namespaces|  Diagnóstico|    Diagnóstico||
|IoT Hubs|  Microsoft.Devices/<br>IotHubs||     Diagnóstico||
|Key Vault| Microsoft.KeyVault/<br>vaults|  Diagnóstico  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Equilibradores de carga|    Microsoft.Network/<br>loadBalancers|    Diagnóstico|||
|Logic Apps|    Microsoft.Logic/<br>workflows|  Diagnóstico|    Diagnóstico||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupos de seguridad de red|   Microsoft.Network/<br>networksecuritygroups|Diagnóstico||   [Azure Network Security Group Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Almacenes de recuperación|   Microsoft.RecoveryServices/<br>vaults|||[Azure Recovery Services Analytics (versión preliminar)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Servicios de búsqueda|   Microsoft.Search/<br>searchServices|    Diagnóstico|    Diagnóstico||
|Espacio de nombres de Service Bus| Microsoft.ServiceBus/<br>namespaces|    Diagnóstico|Diagnóstico|    [Service Bus Analytics (versión preliminar)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric Analytics (versión preliminar)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>bases de datos||       Diagnóstico||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Storage|||         Script| [Azure Storage Analytics (versión preliminar)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtual Machines|  Microsoft.Compute/<br>virtualMachines|  Extensión|  Extensión||
||||Diagnóstico||
|Conjuntos de escalado de máquinas virtuales|   Microsoft.Compute/<br>virtualMachines    ||Diagnóstico||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Granjas de servidores web|Microsoft.Web/<br>serverfarms||   Diagnóstico
|Sitios web| Microsoft.Web/<br>sites ||      Diagnóstico|    [Más información](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integración de registros con sistemas locales de SIEM
La [integración de registros de Azure](https://www.microsoft.com/download/details.aspx?id=53324) le permite integrar los registros sin procesar de los recursos de Azure en los **sistemas locales de administración de eventos e información de seguridad (SIEM)**.

![Integración de registros](./media/azure-log-audit/azure-log-audit-fig9.png)

La integración de registros de Azure recopila Diagnósticos de Azure de las máquinas virtuales de Windows (WAD), los registros de actividad de Azure, las alertas de Azure Security Center y los registros del proveedor de recursos de Azure. Esta integración proporciona un panel unificado para todos los recursos, tanto locales como en la nube, de forma que puede agregar, correlacionar, analizar y alertar sobre los eventos de seguridad.



La integración de registros de Azure admite actualmente la integración de registros de actividad de Azure, registros de eventos de Windows de las máquinas virtuales Windows en su suscripción de Azure, alertas de Azure Security Center, registros de Diagnóstico de Azure y registros de auditoría de Azure Active Directory.

| Tipo de registro | Análisis de registros que admiten JSON (Splunk, ArcSight, Qradar) |
| :------- | :-------------------------------------------------------- |
|Registros de auditoría de AAD|    Sí|
|Registros de actividad| Sí|
|Alertas de ASC |Sí|
|Registros de diagnóstico (registros de recursos)|  Sí|
|Registros de VM|   Sí, mediante eventos reenviados y no mediante JSON|


En la siguiente tabla se explica en detalle la categoría Registro y la integración de SIEM.

[Introducción a la integración de registros de Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): este tutorial lo guía a través de la instalación de la integración de registros de Azure y de almacenamiento de Azure WAD, de registros de actividad de Azure, de alertas de Azure Security Center, y de registros de auditoría de Azure Active Directory.

Escenarios de integración

-   [Partner configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Pasos de configuración de soluciones de asociados): esta entrada de blog muestra cómo configurar la integración de registro de Azure para trabajar con soluciones de asociados como Splunk, HP ArcSight e IBM QRadar.

-   [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.

-   [Integración de las alertas de Security Center con la integración de registro de Azure (versión preliminar)](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration). Este documento le explica cómo sincronizar las alertas de Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM.

## <a name="next-steps"></a>Pasos siguientes

- [Auditoría y registro](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Proteger datos mediante el mantenimiento de la visibilidad y la rápida respuesta a las alertas de seguridad puntuales

- [Security Logging and Audit Log Collection within Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/) (Recopilación de registros de seguridad y de registros de auditoría dentro de Azure)

Qué configuración se debe aplicar para asegurarse de que las instancias de Azure estén recopilando los registros de seguridad y de auditoría correctos.

- [Definir la configuración de auditoría de una colección de sitios](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Como administrador de colecciones de sitios, se puede recuperar el historial de acciones realizadas por un usuario determinado y también el historial de acciones efectuadas durante un intervalo de fechas determinado. 

- [Buscar en el registro de auditoría del Centro de seguridad y cumplimiento de Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Se puede usar el Centro de seguridad y cumplimiento de Office 365 para buscar en el registro de auditoría unificado, y ver la actividad de usuario y de administrador en la organización de Office 365.


