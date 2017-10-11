---
title: Seguridad operativa de Azure | Microsoft Docs
description: "Aprenda sobre Microsoft Operations Management Suite (OMS), sus servicios y cómo funciona."
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
ms.date: 04/27/2017
ms.author: TomSh
ms.openlocfilehash: ec9e0fc7d67537a47d5c0d3bb376b60dc6ccffcd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-operational-security"></a>Seguridad operativa de Azure
## <a name="introduction"></a>Introducción

### <a name="overview"></a>Información general
Sabemos que la seguridad tiene la máxima prioridad en la nube y conocemos la importancia que tiene que buscar información exacta y a tiempo sobre la seguridad de Azure. Una de las principales razones para usar Azure en sus aplicaciones y servicios es aprovechar la amplia gama de herramientas de seguridad y funcionalidades disponibles. Estas herramientas y funcionalidades permiten crear soluciones seguras en la plataforma Azure segura. Microsoft Azure debe proporcionar confidencialidad, integridad y disponibilidad de los datos del cliente, al mismo tiempo que hace posible una responsabilidad transparente.

Para ayudar a los clientes a comprender mejor la gama de controles de seguridad que se implementa en Microsoft Azure desde la perspectiva operativa de Microsoft y la del cliente, se han escrito estas notas del producto, "Seguridad operativa de Azure", para proporcionar información completa sobre la seguridad operativa disponible con Microsoft Azure.

### <a name="azure-platform"></a>Plataforma Azure
Azure es una plataforma de servicios en la nube pública que admite una amplia selección de sistemas operativos, lenguajes de programación, plataformas, herramientas, bases de datos y dispositivos. Puede ejecutar contenedores de Linux con integración de Docker, compilar aplicaciones con JavaScript, Python, .NET, PHP, Java, Node.js y crear back-ends para dispositivos iOS, Android y Windows. El servicio en la nube de Azure admite las mismas tecnologías en las que ya confían millones de desarrolladores y profesionales de TI.

Al crear en un proveedor de servicios en la nube pública o al migrar recursos de TI a uno, confía en las capacidades de la organización para proteger sus aplicaciones y datos con los servicios y los controles que facilitan para administrar la seguridad de sus recursos en la nube.

La infraestructura de Azure está diseñada desde la instalación hasta las aplicaciones para hospedar millones de clientes simultáneamente, y proporciona una base de confianza en la que las empresas pueden satisfacer sus requisitos de seguridad. Además, Azure ofrece una amplia gama de opciones de seguridad configurables, así como la capacidad de controlarlas, por lo que puede personalizar la seguridad para satisfacer los requisitos exclusivos de las implementaciones de su organización. Este documento explica cómo las funcionalidades de seguridad de Azure pueden ayudarle a cumplir estos requisitos.

### <a name="abstract"></a>Descripción breve
La seguridad operativa de Azure hace referencia a los servicios, los controles y las características disponibles para los usuarios para proteger sus datos, aplicaciones y otros recursos en Microsoft Azure. La seguridad operativa de Azure se basa en una plataforma que incorpora el conocimiento adquirido a través de diversas funcionalidades exclusivas de Microsoft, incluido el ciclo de vida de desarrollo de seguridad (SDL) de Microsoft, el programa Microsoft Security Response Center y un conocimiento en profundidad del panorama de amenazas de ciberseguridad.

En estas notas del producto se describe el enfoque de Microsoft hacia la seguridad operativa de Azure dentro de la plataforma de nube de Microsoft Azure y se cubren los siguientes servicios:
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) es la solución de administración de TI para la nube híbrida. Utilizada por sí sola o para extender la implementación existente de System Center, OMS ofrece la máxima flexibilidad y control para la administración basada en la nube de su infraestructura.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

Con OMS, puede administrar cualquier instancia en cualquier nube, incluidas las locales o de Azure, AWS, Windows Server, Linux, VMware y OpenStack, con un costo menor que las soluciones de la competencia. Diseñada para el mundo que prefiere la nube, OMS ofrece un nuevo enfoque a la administración de la empresa que es la manera más rápida y rentable de enfrentarse a los nuevos desafíos de negocio y adaptarse a nuevas cargas de trabajo, aplicaciones y entornos de nube.

### <a name="oms-services"></a>Servicios de OMS

Un conjunto de servicios que se ejecutan en Azure proporciona la funcionalidad principal de OMS. Cada servicio ofrece una función de administración específica, y se pueden combinar los servicios para lograr escenarios de administración diferentes.

| Servicio  | Descripción|
| :------------- | :-------------|
| Log Analytics | Supervisión y análisis de la disponibilidad y el rendimiento de los distintos recursos, incluidas las máquinas físicas y virtuales. |
|Automatización | Automatización de procesos manuales y aplicación de configuraciones a máquinas físicas y virtuales. |
| Copia de seguridad | Realización de copias de seguridad y restauración de datos críticos. |
| Site Recovery | Provisión de una alta disponibilidad para las aplicaciones críticas. |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) proporciona servicios de supervisión para OMS recopilando datos de los recursos administrados en un repositorio central. Estos datos podrían incluir eventos, datos de rendimiento o datos personalizados proporcionados a través de la API. Una vez recopilados, los datos están disponibles para las alertas, el análisis y la exportación.


Este método permite consolidar los datos desde diversos orígenes, de forma que se pueden combinar datos de los servicios de Azure con el entorno local existente. También separa claramente la recopilación de los datos de la acción realizada en los datos, para que todas las acciones estén disponibles para todos los tipos de datos.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

El servicio Log Analytics administra sus datos basados en la nube de forma segura mediante los siguientes métodos:
-   Segregación de datos
-   Retención de datos
-   Seguridad física
-   Administración de incidentes
-   Cumplimiento normativo
-   Certificaciones de estándares de seguridad

### <a name="azure-backup"></a>Copia de seguridad de Azure

[Azure Backup](http://azure.microsoft.com/documentation/services/backup) proporciona servicios de copia de seguridad y restauración de datos y forma parte del conjunto de productos y servicios OMS.
Protege los datos de las aplicaciones y los conserva durante años sin necesidad de realizar ninguna inversión y afrontando unos costes operativos mínimos. Permite realizar la copia de seguridad de datos procedentes de servidores físicos y virtuales de Windows, además de cargas de trabajo de aplicaciones como SQL Server y SharePoint. También se puede usar en [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) para replicar datos protegidos en Azure para obtener redundancia y almacenamiento a largo plazo.


Los datos protegidos en Azure Backup se almacenan en un almacén de copia de seguridad ubicado en una región geográfica determinada. Los datos se replican en la misma región y, según el tipo de almacén, también pueden replicarse en otra región para obtener una resistencia adicional.

### <a name="management-solutions"></a>Soluciones de administración
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura local y en la nube.


Las [soluciones de administración](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) son conjuntos de lógica preempaquetados que implementan un escenario de administración concreto usando uno o más servicios de OMS. Hay disponibles diferentes soluciones de Microsoft y de asociados que se pueden agregar fácilmente a la suscripción de Azure para aumentar el valor de su inversión en OMS. Como asociado, puede crear sus propias soluciones para que sean compatibles con los servicios y las aplicaciones, y proporcionarlas a los usuarios a través de las plantillas de inicio rápido o Azure Marketplace.


![Soluciones de administración](./media/azure-operational-security/azure-operational-security-fig4.png)

Un buen ejemplo de una solución que usa varios servicios para proporcionar funcionalidad adicional es la [solución Administración de actualizaciones](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Esta solución utiliza al agente de [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) de Windows y Linux para recopilar información acerca de las actualizaciones necesarias en cada agente. Escribe estos datos en el repositorio de Log Analytics en donde puede analizarlo con un panel incluido.

Cuando se crea una implementación, se utilizan runbooks en [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) para instalar las actualizaciones necesarias. Todo este proceso se administra en el portal y no tiene que preocuparse sobre los detalles subyacentes.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center ayuda a proteger los recursos de Azure. Proporciona una supervisión de la seguridad y una administración de directivas integradas en suscripciones de Azure. Dentro del servicio, es posible definir directivas no solo para las suscripciones de Azure, sino también para [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), por lo que puede ser más específico.

### <a name="security-policies-and-recommendations"></a>Directivas de seguridad y recomendaciones

Una directiva de seguridad define el conjunto de controles recomendados para los recursos en la suscripción o el grupo de recursos especificados.

En Security Center, se definen directivas de acuerdo con los requisitos de seguridad de la compañía y el tipo de aplicaciones o la confidencialidad de los datos.

![Directivas de seguridad y recomendaciones](./media/azure-operational-security/azure-operational-security-fig5.png)


Las directivas que están habilitadas en el nivel de suscripción se propagan automáticamente a todos los grupos de recursos de la suscripción, como se muestra en el diagrama de la derecha:


### <a name="data-collection"></a>Colección de datos

Security Center recopila datos de las máquinas virtuales (VM) para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y avisarle de las amenazas. La primera vez que se accede a Security Center, la recopilación de datos está habilitada en todas las máquinas virtuales de la suscripción. Aunque se recomienda usar la recopilación de datos, puede desactivarla en la directiva de Security Center.

### <a name="data-sources"></a>Orígenes de datos

- Azure Security Center analiza los datos de los orígenes siguientes para proporcionar visibilidad de su estado de seguridad, identificar vulnerabilidades y recomendar mitigaciones y detectar amenazas activas:

-   Servicios de Azure: utiliza la información acerca de la configuración de los servicios de Azure que se hayan implementado mediante la comunicación con el proveedor de recursos de cada uno de dichos servicios.

- Tráfico de red: usa metadatos muestreados del tráfico de red de la infraestructura de Microsoft, como el IP o el puerto de origen o destino, el tamaño de paquete y el protocolo de red.

-   Soluciones de asociados: usa alertas de seguridad de las soluciones de asociados integradas, como firewalls y soluciones antimalware.

-   Sus Virtual Machines: usa información de configuración e información sobre eventos de seguridad, como los eventos y registros de auditoría de Windows, registros IIS, mensajes de Syslog y archivos de volcado de memoria de sus máquinas virtuales.

### <a name="data-protection"></a>Protección de datos

Para ayudar a los clientes a evitar, detectar y responder a las amenazas, Azure Security Center recopila y procesa datos relacionados con la seguridad, entre los que se incluyen la información de configuración, los metadatos, los registros de eventos y los archivos de volcado de memoria, entre otros. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.

-   **Segregación de datos**: los datos se mantienen separados de forma lógica en cada componente a lo largo de todo el servicio. Todos los datos se etiquetan por organización. Este etiquetado persiste a lo largo del ciclo de vida de los datos y se aplica en cada nivel del servicio.

-   **Acceso a datos**: para proporcionar recomendaciones de seguridad e investigar las posibles amenazas de seguridad, el personal de Microsoft puede acceder a la información recopilada o analizada por los servicios de Azure, incluidos archivos de volcado de memoria, eventos de creación de proceso, artefactos e instantáneas de disco de máquina virtual, que de forma involuntaria pueden incluir datos de cliente o datos personales de sus máquinas virtuales. Cumplimos la [Declaración de privacidad y los Términos de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), en donde se estipula que Microsoft no usa los datos de los clientes ni información derivada de ellos con fines comerciales, publicitarios o similares.

-   **Uso de datos**: Microsoft utiliza los patrones y la información sobre amenazas vistos en varios inquilinos para mejorar las funcionalidades de detección y prevención; esto se realiza según lo dispuesto en los compromisos de privacidad que se describen en nuestra [declaración de privacidad](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

### <a name="data-location"></a>Ubicación de los datos

Azure Security Center recopila copias efímeras de los archivos de volcado de memoria y las analiza para buscar pruebas de intentos de vulnerabilidad y de riesgos ciertos. Azure Security Center realiza este análisis en la misma geoárea en la que se encuentra el área de trabajo y elimina las copias efímeras una vez que el análisis se completa. Los artefactos de la máquina se almacenan de forma centralizada en la misma región que la máquina virtual.

-   **Sus cuentas de almacenamiento**: se especifica una cuenta de almacenamiento para cada región en la que se ejecutan máquinas virtuales. Esto permite almacenar los datos en la misma región en que se encuentra la máquina virtual de la que se recopilan.

-   **Almacenamiento de Azure Security Center**: Además, se guarda información acerca de las alertas de seguridad, incluidas las alertas de asociado, recomendaciones y estado de mantenimiento de seguridad en el almacenamiento centralizado, que actualmente se encuentra en Estados Unidos. Esta información puede incluir información de configuración relacionada y eventos de seguridad recopilados de las máquinas virtuales que son necesarios para proporcionarle la alerta de seguridad, la recomendación o el estado de mantenimiento de seguridad.


## <a name="azure-monitor"></a>Azure Monitor

La solución [Seguridad y auditoría de OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) permite a la TI supervisar activamente todos los recursos, lo que puede ayudar a minimizar el impacto de los incidentes de seguridad. Auditoría y seguridad de OMS tiene dominios de seguridad que pueden utilizarse para la supervisión de recursos. El dominio de seguridad proporciona acceso rápido a opciones para supervisar la seguridad. Se proporciona más información de los siguientes dominios:

-   Evaluación de malware
-   Evaluación de la actualización
-   Identidad y acceso

Azure Monitor proporciona punteros a la información sobre determinados tipos de recursos. Ofrece funciones de visualización, consulta, enrutamiento, alertas, escalado automático y automatización de los datos tanto de la infraestructura de Azure (registro de actividad) como de cada recurso individual de Azure (registros de diagnóstico).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Las aplicaciones de nube son complejas y tienen muchas partes móviles. La supervisión proporciona datos para garantizar que la aplicación permanece en funcionamiento en un estado correcto. También ayuda a evitar posibles problemas o a solucionar los existentes.

Además, puede usar datos de supervisión para obtener un conocimiento más profundo sobre su aplicación. Este conocimiento puede ayudarle a mejorar el rendimiento o mantenimiento de la aplicación, o a automatizar acciones que de lo contrario requerirían intervención manual.

### <a name="azure-activity-log"></a>Azure Activity Log


Es un registro que proporciona información sobre las operaciones que se realizaron en los recursos de su suscripción. El registro de actividad se conocía antes como "Registros de auditoría" o "Registros operativos", ya que notifica eventos del plano de control para las suscripciones.

![Azure Activity Log](./media/azure-operational-security/azure-operational-security-fig7.png)

Con el Registro de actividades, se pueden determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) en los recursos de la suscripción. También puede conocer el estado de la operación y otras propiedades relevantes. El registro de actividad no incluye las operaciones de lectura (GET) ni las operaciones de los recursos que usan el modelo Clásico.

### <a name="azure-diagnostic-logs"></a>Registros de diagnóstico de Azure

Estos registros los emite un recurso y proporcionan información detallada y frecuente sobre la operación de dicho recurso. El contenido de estos registros varía según el tipo de recurso.

Por ejemplo, los registros del sistema de eventos de Windows son una categoría de registro de diagnóstico para máquinas virtuales y los registros de blob, tabla y cola son categorías de los registros de diagnóstico para cuentas de almacenamiento.

Los registros de diagnóstico son distintos del [registro de actividad (anteriormente conocido como registro de auditoría o registro operativo)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). El registro de actividad proporciona información sobre las operaciones que se realizaron en los recursos de su suscripción. Los registros de diagnóstico proporcionan información detallada sobre las operaciones que el mismo recurso realiza.

### <a name="metrics"></a>Métricas

Azure Monitor permite utilizar telemetría para obtener información sobre el rendimiento y el estado de las cargas de trabajo en Azure. El tipo de telemetría de datos de Azure más importante son las métricas (también denominadas contadores de rendimiento) emitidas por la mayoría de los recursos de Azure. Azure Monitor proporciona varias maneras de configurar y consumir estas [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para supervisar y solucionar problemas. Las métricas son una valiosa fuente de telemetría y le permiten realizar las siguientes tareas:

-   **Realizar un seguimiento del rendimiento** del recurso (por ejemplo, una máquina virtual, un sitio web o una aplicación lógica) mediante el trazado de las métricas en un gráfico de portal y anclando ese gráfico a un panel.

-   **Recibir una notificación de un problema** que afecta al rendimiento del recurso cuando una métrica cruza un umbral determinado.

-   **Configurar acciones automatizadas**, como escalar automáticamente un recurso o activar un runbook cuando una métrica cruza un umbral determinado.

-   **Realizar análisis avanzados** o elaborar informes de tendencias de rendimiento o de uso de los recursos.

-   **Archivar** el historial de rendimiento o estado de los recursos para fines de cumplimiento o auditoría.

### <a name="azure-diagnostics"></a>Diagnóstico de Azure

Es la funcionalidad de Azure que habilita la recopilación de datos de diagnóstico en una aplicación implementada. Puede utilizar la extensión de diagnóstico desde diversos orígenes diferentes. Actualmente se admiten [roles web y de trabajo del servicio en la nube de Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) que ejecutan Microsoft Windows, y [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Otros servicios de Azure tienen su propio diagnóstico independiente.

## <a name="azure-network-watcher"></a>Azure Network Watcher

La auditoría de la seguridad de la red es fundamental para detectar vulnerabilidades de red y asegurar el cumplimiento de la seguridad de TI y el modelo de gobierno reglamentario. Con la vista Grupo de seguridad, puede recuperar las reglas de seguridad y de grupo de seguridad de red configuradas, así como las reglas de seguridad efectivas. Con la lista de reglas que se aplican, puede determinar los puertos que están abiertos y evaluar la vulnerabilidad de la red.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de red en Azure. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure. Este servicio incluye captura de paquetes, próximo salto, comprobación de flujo de IP, vista de grupos de seguridad y registros de flujo de NSG. La supervisión en el nivel de escenario, ofrece una visión global de los recursos de la red que contrasta con la supervisión de recursos de red individuales.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

En la actualidad, Network Watcher dispone de las siguientes funcionalidades:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Registros de auditoría</a>**: se registran las operaciones realizadas como parte de la configuración de redes. Estos registros se pueden ver en Azure Portal o se pueden recuperar mediante herramientas de Microsoft como Power BI o herramientas de terceros. Los registros de auditoría están disponibles a través del portal, PowerShell, la CLI y la API de Rest. Para más información sobre los registros de auditoría, consulte Operaciones de auditoría con Resource Manager. Hay registros de auditoría disponibles para las operaciones realizadas en todos los recursos de red.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Comprobaciones de flujo de IP</a>**: comprueba si un paquete está permitido o no en función de los parámetros de paquete de 5-tuplas de información del flujo (IP de destino, IP de origen, puerto de destino, puerto de origen y protocolo). Si un grupo de seguridad de red deniega un paquete, se devuelven el nombre de la regla y del grupo de seguridad que lo deniega.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Próximo salto</a>**: determina el próximo salto para los paquetes que se enrutan en el tejido de red de Azure, lo que le permite diagnosticar cualquier ruta definida por el usuario que se haya configurado incorrectamente.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Vista de grupos de seguridad</a>**: obtiene las reglas de seguridad eficaces que se aplican en una máquina virtual.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Registro de flujo de NSG</a>**: los registros de flujo de los grupos de seguridad de red permiten capturar registros relacionados con el tráfico que están permitidos o no por las reglas de seguridad del grupo. El flujo se define mediante una información de 5-tuplas: IP de origen, IP de destino, puerto de origen, puerto de destino y protocolo.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) puede almacenar métricas que incluyen estadísticas de las transacciones y datos de capacidad agregados sobre las solicitudes realizadas a un servicio de almacenamiento. Las transacciones se notifican tanto en el nivel de operación de API como en el nivel de servicio de almacenamiento, y la capacidad se notifica en el nivel de servicio de almacenamiento. Los datos de las métricas se pueden utilizar para analizar el uso del servicio de almacenamiento, diagnosticar problemas con las solicitudes realizadas en el mismo y mejorar el rendimiento de las aplicaciones que utilizan un servicio.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) realiza el registro y proporciona datos de métricas para una cuenta de almacenamiento. Puede usar estos datos para hacer un seguimiento de solicitudes, analizar tendencias de uso y diagnosticar problemas con la cuenta de almacenamiento. El registro de Storage Analytics está disponible para los [servicios Blob, Queue y Table service](https://docs.microsoft.com/azure/storage/storage-introduction). El análisis de almacenamiento registra información detallada sobre las solicitudes correctas y erróneas realizadas a un servicio de almacenamiento.

Esta información se puede utilizar para supervisar solicitudes concretas y para diagnosticar problemas con un servicio de almacenamiento. Las solicitudes se registran en función de la mejor opción. Las entradas del registro se crean solo si se presentan solicitudes al punto de conexión de servicio. Por ejemplo, si una cuenta de almacenamiento tiene actividad en el punto de conexión de Blob service pero no en los puntos de conexión de Table o Queue service, solo se crean los registros correspondientes a Blob service.

Para utilizar el análisis de almacenamiento, debe habilitarlo para cada servicio que desee supervisar. Puede habilitarlo en [Azure Portal](https://portal.azure.com/); para más información, consulte [Supervisión de una cuenta de almacenamiento en Azure Portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). También puede habilitar el análisis de almacenamiento mediante programación a través de la API de REST o la biblioteca de cliente. Use la operación Set Service Properties para habilitar Storage Analytics de forma individual para cada servicio.

Los datos agregados se almacenan en un blob conocido (para el registro) y en tablas conocidas (para las métricas), a los que se puede tener acceso mediante las API del servicio Blob y del servicio Tabla.

Storage Analytics tiene un límite de 20 TB en cuanto a la cantidad de datos almacenados, que es independiente del límite total de la cuenta de almacenamiento. Todos los registros se almacenan en [blobs en bloques](https://docs.microsoft.com/azure/storage/storage-analytics) en un contenedor denominado $logs, que se crea automáticamente cuando se habilita Storage Analytics para una cuenta de almacenamiento.

Son facturables las acciones siguientes realizadas por el análisis de almacenamiento:

-   Solicitudes para crear blobs para el registro
-   Solicitudes para crear entidades de tabla para las métricas

> [!Note]
> Para más información sobre las directivas de facturación y retención de datos, consulte [Storage Analytics and Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing) (Storage Analytics y facturación).
> Si desea obtener un rendimiento óptimo, conviene que limite el número de discos muy usados que se conectan a la máquina virtual para evitar una posible limitación. Si no todos los discos se usan mucho al mismo tiempo, la cuenta de almacenamiento admite un mayor número de discos.

> [!Note]
> Para más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Se registran los siguientes tipos de solicitudes autenticadas y anónimas.

| Autenticadas  | Anónimas|
| :------------- | :-------------|
| Solicitudes correctas | Solicitudes correctas |
|Solicitudes erróneas, incluidos errores de tiempo de espera, de limitación, de red, de autorización y de otro tipo | Solicitudes que usan una firma de acceso compartido (SAS), incluidas las correctas y las erróneas |
| Solicitudes que usan una firma de acceso compartido (SAS), incluidas las correctas y las erróneas |Errores de tiempo de espera para el cliente y el servidor |
|   Solicitudes de datos de análisis |    Solicitudes GET erróneas con el código de error 304 (No modificado) |
| Las solicitudes realizadas por el propio análisis de almacenamiento, como la creación o eliminación del registro, no se registran. Puede encontrar una lista completa de los datos registrados en los temas [Operaciones y mensajes de estado registrados por el análisis de almacenamiento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) y [Formato del registro del análisis de almacenamiento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | El resto de solicitudes anónimas erróneas no se registran. Puede encontrar una lista completa de los datos registrados en [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Operaciones registradas y mensajes de estado de Storage Analytics) y [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Formato de registro de Storage Analytics). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD también incluye un conjunto completo de funcionalidades de administración de identidades, entre ellas, Multi-Factor Authentication, registro de dispositivos, administración de contraseñas de autoservicio, administración de grupos de autoservicio, administración de cuentas con privilegios, control de acceso basado en rol, supervisión del uso de aplicaciones, auditoría enriquecida y supervisión y alertas de seguridad.

-   Mejore la seguridad de las aplicaciones con la autenticación multifactor y acceso condicional de Azure AD.

-   Supervise el uso de las aplicaciones y proteja su empresa contra amenazas avanzadas con informes y supervisión de seguridad.

Azure Active Directory (Azure AD) incluye informes de seguridad, actividad y auditoría para el directorio. [El informe de auditoría de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ayuda a los clientes a identificar las acciones con privilegios que se produjeron en su entorno de Azure Active Directory. Las acciones con privilegios incluyen cambios de elevación (por ejemplo, creación de roles o restablecimientos de contraseña), cambios de configuraciones de directiva (por ejemplo, directivas de contraseña) o cambios de configuración de directorio (por ejemplo, cambios en la configuración de la federación de dominio).

Los informes proporcionan el registro de auditoría para el nombre del evento, el actor que realizó la acción, el recurso de destino que se ve afectado por el cambio y la fecha y hora (en UTC). Los clientes pueden recuperar la lista de eventos de auditoría de su entorno de Azure Active Directory desde [Azure Portal](https://portal.azure.com/), como se describe en [Visualización de los registros de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Esta es una lista de los informes incluidos:

| Informes de seguridad  | Informes de actividad| Informes de auditoría |
| :------------- | :-------------| :-------------|
|Inicios de sesión desde orígenes desconocidos | Uso de la aplicación: resumen | Informe de auditoría de directorio |
|Inicios de sesión tras varios errores | Uso de la aplicación: detallado |   |
|Inicios de sesión desde varias ubicaciones geográficas | Panel de la aplicación |  |
|Inicios de sesión desde direcciones IP con actividad sospechosa |Errores de aprovisionamiento de cuentas |  |
|Actividad de inicio de sesión irregular |Dispositivos de usuario individuales |  |
|Inicios de sesión desde dispositivos posiblemente infectados |Actividad de usuario individual |   |
|Usuarios con actividad de inicio de sesión anómala |Informe de actividad de grupos |   |
| |Informe de actividad de registro de restablecimiento de contraseña |   |
| |Actividad de restablecimiento de contraseña |   | |



Los datos de estos informes pueden resultar útiles para las aplicaciones, como sistemas SIEM, auditorías y herramientas de inteligencia empresarial. Las [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) de generación de informes de Azure AD proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Se puede llamar a estas API desde diversos lenguajes de programación y herramientas.

Los eventos del informe de auditoría de Azure AD se conservan durante 180 días.

> [!Note]
> Para obtener más información acerca de la retención de los informes, consulte [Directivas de retención de informes de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Para los clientes interesados en almacenar [eventos de auditoría](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) durante períodos de retención más largos, se puede utilizar la API de informes para extraer periódicamente los eventos de auditoría a un almacén de datos independiente.

## <a name="summary"></a>Resumen

Este artículo resume la protección de la privacidad y los datos, así como el software y los servicios que le ayudan a administrar la infraestructura de TI de su organización. Microsoft reconoce que, cuando confía sus datos a otras personas, esa confianza requiere una seguridad rigurosa. Microsoft se adhiere a instrucciones estrictas de seguridad y cumplimiento de normas, desde la codificación hasta la operación de un servicio.  La seguridad y la protección de los datos son prioritarias en Microsoft.

Este artículo explica cómo

-   Recopilar datos, procesarlos y protegerlos en Operations Management Suite (OMS).

-   Analizar rápidamente eventos en distintos orígenes de datos. Identificar riesgos de seguridad y comprender el ámbito y el impacto de las amenazas y los ataques para reducir el daño de una infracción de seguridad.

-   Identificar patrones de ataque visualizando tipos de amenazas malintencionadas y tráfico de IP malintencionado saliente. Comprender la posición de seguridad de todo el entorno independientemente de la plataforma.

-   Capturar todos los datos de eventos y registros requeridos para una auditoría de cumplimiento o seguridad. Reducir el tiempo y los recursos necesarios para suministrar una auditoría de seguridad con un completo conjunto de datos de registro y eventos que admite búsquedas y se puede exportar.

<ul>
<li>Recopilar eventos relacionados con la seguridad, auditorías y análisis de infracciones para mantener vigilados sus recursos:</li>
<ul>
<li>Posición de seguridad</li>
<li>Problema importante</li>
<li>Resúmenes de amenazas</li>
</ul>
</ul>

## <a name="next-steps"></a>Pasos siguientes

- [Diseño y seguridad operativa](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft diseña su software y sus servicios teniendo en cuenta la seguridad para ayudar a garantizar que su infraestructura de nube sea resistente y esté defendida ante los ataques.

- [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance)

Use el análisis y los datos de seguridad de Microsoft para realizar una detección de amenazas más inteligente y eficaz.

- [Planeamiento y operaciones de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide): una serie de pasos y tareas que puede seguir para optimizar el uso de Security Center en función de los requisitos de seguridad y el modelo de administración en la nube de su organización.

