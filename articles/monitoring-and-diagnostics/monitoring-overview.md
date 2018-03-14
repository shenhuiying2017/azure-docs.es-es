---
title: "Supervisión de recursos y aplicaciones de Azure | Microsoft Docs"
description: "Introducción a los servicios y funcionalidades de Microsoft que contribuyen a una estrategia de supervisión completa de los servicios y las aplicaciones de Azure."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: d8da175a551f7c589c313b2289b2a0209dbd2b56
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Supervisión de aplicaciones y recursos de Azure

La supervisión es el acto de recopilar y analizar datos para determinar el rendimiento, el mantenimiento y la disponibilidad de su aplicación empresarial y de los recursos de los que esta depende. Una estrategia de supervisión eficaz le ayuda a comprender el funcionamiento detallado de los componentes de la aplicación. También le ayuda a aumentar el tiempo de actividad al notificarle de forma proactiva cuestiones críticas para que pueda resolverlas antes de que se conviertan en problemas.

Azure incluye varios servicios que realizan individualmente una tarea o un rol específico en el espacio de supervisión. Juntos, estos servicios ofrecen una solución completa para recopilar, analizar y actuar en la telemetría de la aplicación y los recursos de Azure que las admiten. También pueden servir para supervisar recursos locales críticos, a fin de proporcionar un entorno de supervisión híbrido. Conocer las herramientas y los datos que están disponibles es el primer paso para desarrollar una estrategia de supervisión completa para la aplicación. 

En el siguiente diagrama se muestra una vista conceptual de los componentes que funcionan conjuntamente para proporcionar la supervisión de los recursos de Azure. En las siguientes secciones se describen estos componentes y se proporcionan vínculos a información técnica detallada.

![Información general de la supervisión](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Supervisión básica
La supervisión básica proporciona el control fundamental requerido de los recursos de Azure. Estos servicios requieren una configuración mínima y recopilan la telemetría principal que usan los servicios de supervisión premium.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) habilita la supervisión básica del servicio de Azure al permitir la recopilación de [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) y [registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Por ejemplo, el registro de actividad le indica cuándo se crean o modifican los recursos. 

Se encuentran disponibles métricas que ofrecen estadísticas de rendimiento de diferentes recursos e incluso del sistema operativo de una máquina virtual. Puede ver estos datos con alguno de los exploradores en Azure Portal, enviarlos a Azure Log Analytics para tendencias y análisis detallados o crear reglas de alertas para notificar los problemas críticos de forma proactiva.

### <a name="service-health"></a>Service Health
El estado de la aplicación se basa en los servicios de Azure de los que depende. [Azure Service Health](../service-health/service-health-overview.md) identifica los problemas con los servicios de Azure que podrían afectar a la aplicación. Service Health también le ayuda a planear un mantenimiento programado.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) supervisa constantemente la telemetría de uso y la configuración de recursos. Ofrece recomendaciones personalizadas basadas en procedimientos recomendados. El seguimiento de estas recomendaciones ayuda a mejorar el rendimiento, la seguridad y la disponibilidad de los recursos que respaldan las aplicaciones.


## <a name="premium-monitoring-services"></a>Servicios de supervisión premium
Los siguientes servicios de Azure proporcionan funcionalidades enriquecidas para recopilar y analizar datos de supervisión. Estos servicios se basan en la supervisión básica y aprovechan las funcionalidades comunes de Azure. Proporcionan análisis eficaces con los datos recopilados y le ofrecen información única sobre sus aplicaciones e infraestructura. Presentan los datos en el contexto de escenarios destinados a diferentes públicos.

### <a name="application-insights"></a>Application Insights
Puede utilizar [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) para supervisar la disponibilidad, el rendimiento y el uso de la aplicación, tanto si se hospeda en la nube como en un entorno local. 

Mediante la instrumentación de la aplicación para que funcione con Application Insights, puede obtener información detallada. Puede identificar y diagnosticar errores rápidamente sin tener que esperar a que un usuario informe de ellos. Con la información que recopile, puede tomar decisiones informadas sobre el mantenimiento y las mejoras de la aplicación. 

Application Insights tiene numerosas herramientas para interactuar con los datos que recopila. Application Insights almacena sus datos en un repositorio común. Puede sacar partido de las funcionalidades compartidas, como alertas, paneles y análisis detallados con el lenguaje de consulta de Log Analytics.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) desempeña un rol fundamental en la supervisión de Azure mediante la recopilación de datos de una gran variedad de recursos en un solo repositorio. Allí, puede analizar los datos mediante un lenguaje de consulta eficaz. 

Application Insights y Azure Security Center almacenan sus datos en el almacén de datos de Log Analytics y utilizan su motor de análisis. Los datos son datos recopilados de Azure Monitor, de las soluciones de administración y de los agentes instalados en máquinas virtuales locales en la nube o en el entorno local. Esta funcionalidad compartida le ayuda a formarse una imagen completa de su entorno. 


### <a name="service-map"></a>Mapa de servicio
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) proporciona información de su entorno de IaaS mediante el análisis de las máquinas virtuales con sus distintos procesos y dependencias de otros equipos y procesos externos. Integra eventos, datos de rendimiento y soluciones de administración en Log Analytics. Después, puede ver estos datos en el contexto de cada equipo y su relación con el resto del entorno. 

Service Map es similar a [Mapa de aplicación en Application Insights](../application-insights/app-insights-app-map.md). Se centra en los componentes de infraestructura que admiten las aplicaciones.

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) proporciona supervisión y diagnóstico basados en escenarios para distintos escenarios de red de Azure. Almacena datos en las métricas y diagnósticos de Azure para su posterior análisis. Funciona con las siguientes soluciones para supervisar diversos aspectos de la red:
* [Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/): una solución de supervisión basada en la nube, que controla la conectividad entre las nubes públicas, los centros de datos y los entornos locales.
* [ExpressRoute Monitor](https://azure.microsoft.com/en-in/blog/monitoring-of-azure-expressroute-in-preview/): funcionalidad de NPM que supervisa la conectividad de un extremo a otro y el rendimiento a través de circuitos Azure ExpressRoute.
* Análisis de tráfico: solución basada en la nube, que proporciona visibilidad de la actividad de usuarios y aplicaciones en la red en la nube.
* [DNS Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-dns): solución que proporciona información detallada relacionada con la seguridad, el rendimiento y las operaciones, en función de los servidores DNS.

### <a name="management-solutions"></a>Soluciones de administración
Las [soluciones de administración](../log-analytics/log-analytics-add-solutions.md) son conjuntos de lógica empaquetados que ofrecen información para una aplicación o servicio concretos. Se basan en Log Analytics para almacenar y analizar los datos de supervisión que recopilan. 

Las soluciones de administración las ponen a disposición Microsoft y asociados que ofrecen supervisión para distintos servicios de Azure y de terceros. Entre los ejemplos de soluciones de supervisión se incluyen los siguientes:
* [Supervisión de contenedor](../log-analytics/log-analytics-containers.md), que ayuda a ver y administrar los hosts de contenedores.
* [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md), que recopila y muestra métricas de rendimiento para bases de datos Azure SQL.


## <a name="shared-functionality"></a>Funcionalidad compartida
Las siguientes herramientas de Azure proporcionan una funcionalidad crítica para los servicios de supervisión premium. Varios servicios las comparten, lo que permite aprovechar configuraciones y funcionalidades comunes en varios servicios.

### <a name="alerts"></a>Alertas
Las [alertas de Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) informan de forma proactiva de estados críticos y aplican potencialmente acciones correctivas. Las reglas de alertas pueden usar datos de varios orígenes, como las métricas y los registros. Usan [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md) que contienen conjuntos únicos de destinatarios y acciones en respuesta a una alerta. Según sus requisitos, puede tener acciones externas de inicio de alertas mediante webhooks e integrarlas con las herramientas de ITSM.

### <a name="dashboards"></a>Paneles
Puede utilizar [paneles de Azure](../azure-portal/azure-portal-dashboards.md) para combinar distintos tipos de datos en un único panel en Azure Portal. Puede compartir luego el panel con otros usuarios de Azure. 

Por ejemplo, puede crear un panel que combine:
- Iconos que muestran un grafo de métricas
- Una tabla de registro de actividad
- Un gráfico de uso de Application Insights
- La salida de una búsqueda de registros de Log Analytics

También puede exportar datos de Log Analytics a [Power BI](https://docs.microsoft.com/power-bi/). Allí, puede sacar partido de visualizaciones adicionales. Puede hacer asimismo que los datos estén disponibles para otras personas dentro y fuera de su organización.

### <a name="metrics-explorer"></a>Explorador de métricas
Las [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) son valores numéricos generados por un recurso de Azure que le ayudan a comprender el funcionamiento y el rendimiento del recurso. Mediante el Explorador de métricas, puede enviar métricas a Log Analytics para realizar análisis con datos de otros orígenes.



### <a name="activity-log"></a>Registro de actividad
[Activity Log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) proporciona datos sobre el funcionamiento de un recurso de Azure. Esta información incluye:
- Cambios de configuración para el recurso.
- Incidentes del mantenimiento del servicio.
- Recomendaciones sobre la mejor utilización del recurso.
- Información relativa a operaciones de escalado automático. 

Puede ver registros para un recurso determinado en su página de Azure Portal. O bien, puede ver registros de varios recursos en el Explorador de registros de actividad. 

También puede enviar registros de actividad a Log Analytics. Allí, puede analizar los registros mediante los datos recopilados por las soluciones de administración, los agentes en máquinas virtuales y otros orígenes.


## <a name="example-scenarios"></a>Escenarios de ejemplo
A continuación se indican ejemplos de alto nivel que ilustran cómo puede utilizar las diferentes herramientas de supervisión en Azure para distintos escenarios.

### <a name="monitoring-a-web-application"></a>Supervisión de una aplicación web
Considere la posibilidad de implementar una aplicación web en Azure mediante Azure App Service, Azure Storage y SQL Database. Empiece por acceder a las [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) y los [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) para estos recursos en sus páginas de Azure Portal. Busque información crítica, como el número de solicitudes a la aplicación y el tiempo medio de respuesta. Identifique también cualquier cambio de configuración.

Después, vaya a Monitor en el portal para ver las métricas y los registros de los distintos recursos de forma conjunta. Al ir determinando parámetros estándar para las métricas, [crea reglas de alerta](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Estas reglas informan de forma proactiva, por ejemplo, cuando aumenta el tiempo medio de respuesta más allá de un umbral. Para obtener una vista rápida del rendimiento diario de su aplicación, cree un panel de Azure para mostrar grafos de métricas que representen KPI críticos.

Para realizar una supervisión más detallada de la aplicación, [configúrela para Application Insights](../application-insights/quick-monitor-portal.md). Ahora puede recopilar datos adicionales que proporcionan más información sobre el funcionamiento y el rendimiento de la aplicación. Application Insights detecta las relaciones subyacentes entre los componentes de la aplicación. Permite la representación visual a través del [mapa de aplicación](../application-insights/app-insights-app-map.md) junto con una [traza de un extremo a otro](../application-insights/app-insights-transaction-diagnostics.md), a fin de diagnosticar el componente, la dependencia o la excepción exactos cuando se produce un problema. 

Cree [conjuntos de disponibilidad](../application-insights/app-insights-monitor-web-app-availability.md) para probar de forma proactiva la aplicación desde distintas regiones. Para ayudar a los desarrolladores, [habilite el generador de perfiles](../application-insights/enable-profiler-compute.md), para que pueda realizar un seguimiento de las solicitudes y excepciones hasta una línea de código específica. Para aumentar aún más la visibilidad de los servicios utilizados en la aplicación, agregue la [solución SQL Analytics](../log-analytics/log-analytics-azure-sql.md) para recopilar datos adicionales en Log Analytics. 

Más adelante, decide investigar la causa principal para los períodos en los que el rendimiento del sitio ha caído por debajo del umbral. Escriba una consulta con Log Analytics. Le ayuda a poner en correlación los datos de uso y rendimiento recopilados por Application Insights con los datos de configuración y rendimiento de los recursos de Azure que respaldan la aplicación.


### <a name="monitoring-virtual-machines"></a>Supervisión de máquinas virtuales
Tiene una combinación de máquinas virtuales Windows y Linux que se ejecutan en Azure. Use Azure Monitor para ver [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) y [métricas a nivel de host](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Agregue la [extensión de Azure Diagnostics](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) a las máquinas virtuales, para recopilar métricas del sistema operativo invitado. Después, cree [reglas de alertas](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para recibir una notificación de forma proactiva cuando las métricas básicas, como el uso del procesador y la memoria, cruzan los umbrales.

Para recopilar más información sobre las máquinas virtuales que ejecutan una aplicación empresarial, [cree un área de trabajo de Log Analytics y habilite la extensión de VM](../log-analytics/log-analytics-quick-collect-azurevm.md) en cada equipo. Configure la [recopilación de orígenes de datos diferentes](../log-analytics/log-analytics-data-sources.md) para la aplicación y [cree vistas](../log-analytics/log-analytics-view-designer.md) para informar sobre su funcionamiento y rendimiento diarios. Después, [cree reglas de alertas](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para recibir una notificación cuando se reciban eventos de error particulares. 

Para supervisar de forma continua el mantenimiento del agente instalado, agregue la [solución de administración Agent Health](../operations-management-suite/oms-solution-agenthealth.md). Para más información sobre la aplicación, [agregue el agente de dependencias](../operations-management-suite/operations-management-suite-service-map-configure.md) a las máquinas virtuales para agregarlas a [Service Map](../operations-management-suite/operations-management-suite-service-map.md). Service Map detecta los procesos críticos e identifica las conexiones entre máquinas con otros servicios. 

Después de una notificación de interrupción, use Service Map para realizar análisis forenses con el fin de identificar las máquinas concretas que experimentaron el problema. A continuación, cree una [consulta sobre los datos de Log Analytics](../log-analytics/log-analytics-log-search-new.md) para identificar el problema en el futuro. Y cree una regla de alerta para informar de forma proactiva cuando se detecta la condición.



## <a name="next-steps"></a>Pasos siguientes
Más información sobre:

* [Azure Monitor en un vídeo de Ignite 2016](https://myignite.microsoft.com/videos/4977).
* [Introducción a Azure Monitor](monitoring-get-started.md).
* [Azure Diagnostics](../azure-diagnostics.md) si intenta diagnosticar problemas en su aplicación de Cloud Services, Virtual Machines, conjunto de escalado de máquinas virtuales o Azure Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si está intentando diagnosticar problemas en su aplicación web de App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/): para analizar los datos de supervisión recopilados.
