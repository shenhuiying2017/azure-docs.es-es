---
title: "Supervisión de recursos y aplicaciones de Azure | Microsoft Docs"
description: "Información general de los distintos servicios y funcionalidades de Microsoft que contribuyen a una estrategia de supervisión completa de los servicios y las aplicaciones de Azure."
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
ms.openlocfilehash: 3ab7d2d5c3b95d215f3ee9eb9346e8a7895e734c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Supervisión de aplicaciones y recursos de Azure

La supervisión es el acto de recopilar y analizar datos para determinar el rendimiento, el mantenimiento y la disponibilidad de su aplicación empresarial y de los recursos de los que esta depende. Una estrategia de supervisión eficaz facilitará la comprensión de la operación detallada de los distintos componentes de la aplicación y aumentará el tiempo de actividad con la notificación anticipada de errores críticos, para poder solucionarlos antes de que se conviertan en problemas.

Azure incluye varios servicios que individualmente realizan una tarea o un rol específicos en el espacio de supervisión, y juntos proporcionan una solución completa para recopilar y analizar la telemetría de la aplicación y los recursos de Azure subyacentes que los respaldan y para actuar en consecuencia.  También pueden servir para supervisar recursos locales críticos, a fin de proporcionar un entorno de supervisión híbrido.   Conocer las herramientas y los datos que están disponibles es el primer paso para desarrollar una estrategia de supervisión completa para la aplicación. 

En el siguiente diagrama se muestra una vista conceptual de los distintos componentes que funcionan conjuntamente para proporcionar la supervisión de los recursos de Azure.  Cada uno de ellos se describe en las siguientes secciones con vínculos a información técnica detallada.

![Información general de la supervisión](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Supervisión básica
La supervisión básica proporciona el control fundamental requerido de los recursos de Azure.  Estos servicios requieren una configuración mínima y recopilan la telemetría principal que usan los servicios de supervisión premium.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) habilita la supervisión básica del servicio de Azure al permitir la recopilación de [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) y [registros de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).  Por ejemplo, el registro de actividad indicará cuándo se crean o modifican los recursos.  Se encuentran disponibles métricas que ofrecen estadísticas de rendimiento de diferentes recursos e incluso del sistema operativo de una máquina virtual.  Puede ver estos datos con alguno de los exploradores en Azure Portal, enviarlos a Log Analytics para tendencias y análisis detallados o crear reglas de alertas para notificar los problemas críticos con antelación.

### <a name="service-health"></a>Service Health
El estado de la aplicación se basa en los servicios de Azure de los que depende.  [Azure Service Health](../service-health/service-health-overview.md) identifica los problemas con los Servicios de Azure que podrían afectar a la aplicación y también ayuda a planear cualquier programa de mantenimiento.

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) supervisa constantemente la telemetría de uso y configuración de los recursos para proporcionar recomendaciones personalizadas basadas en procedimientos recomendados.  El seguimiento de estas recomendaciones ayuda a mejorar el rendimiento, la seguridad y la disponibilidad de los recursos que respaldan las aplicaciones.


## <a name="premium-monitoring-services"></a>Servicios de supervisión premium
Los siguientes servicios de Azure proporcionan funcionalidades enriquecidas para recopilar y analizar datos de supervisión.  Se basan en la supervisión básica y usan la funcionalidad común de Azure y proporcionan análisis eficaces con los datos recopilados para ofrecer información exclusiva de las aplicaciones y la infraestructura.  Presentan los datos en el contexto de escenarios concretos destinados a diferentes públicos.

### <a name="application-insights"></a>Application Insights
[Application Insights](http://azure.microsoft.com/documentation/services/application-insights) permite supervisar la disponibilidad, el rendimiento y el uso de la aplicación, tanto si se hospeda en la nube como en un entorno local.  Mediante la instrumentación de la aplicación para que funcione con Application Insights, puede obtener información detallada, lo que le permite identificar rápidamente y diagnosticar errores sin tener que esperar a que un usuario informe de ellos. Con la información que recopile, puede tomar decisiones informadas sobre el mantenimiento y las mejoras de la aplicación.  Además de las herramientas extensas para interactuar con los datos que recopila, Application Insights almacena sus datos en un repositorio común para aprovechar la funcionalidad compartida, como las alertas, los paneles y un análisis profundo con el lenguaje de consulta de Log Analytics.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) desempeña un rol fundamental en la supervisión de Azure mediante la recopilación de datos de una variedad de recursos en un solo repositorio donde se pueden analizar con un lenguaje de consulta eficaz.  Application Insights y Azure Security Center almacenan sus datos en el almacén de datos de Log Analytics y utilizan su motor de análisis.  Todo esto combinado con los datos recopilados de Azure Monitor, de las soluciones de administración y de los agentes instalados en máquinas virtuales locales en la nube o en el entorno local le permite crear una imagen completa de todo el entorno. 


### <a name="service-map"></a>Mapa de servicio
[Service Map](../operations-management-suite/operations-management-suite-service-map.md) proporciona información de su entorno de IaaS mediante el análisis de las máquinas virtuales con sus distintos procesos y dependencias de otros equipos y procesos externos.  Integra eventos, datos de rendimiento y soluciones de administración en Log Analytics, para que pueda ver estos datos en el contexto de cada equipo y su relación con el resto de su entorno.  Service Map es similar al [mapa de aplicación de Application Insights](../application-insights/app-insights-app-map.md), pero se centra en los componentes de la infraestructura que respaldan las aplicaciones.

### <a name="network-watcher"></a>Network Watcher
[Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) proporciona supervisión y diagnóstico basados en escenarios para distintos escenarios de red de Azure.  Almacena datos de métricas y diagnósticos de Azure para realizar análisis y trabajos adicionales con [soluciones de administración de Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), para realizar la supervisión completa de los recursos de red.


### <a name="management-solutions"></a>Soluciones de administración
Las [soluciones de administración](../log-analytics/log-analytics-add-solutions.md) son conjuntos de lógica empaquetados que ofrecen información para una aplicación o servicio concretos.  Se basan en Log Analytics para almacenar y analizar los datos de supervisión que recopilan.  Las soluciones de administración las pone a disposición Microsoft y asociados que ofrecen supervisión de distintos servicios de Azure y de terceros. Como ejemplo de soluciones de supervisión se incluyen la [supervisión de contenedores](../log-analytics/log-analytics-containers.md), que ayuda a ver y administrar los hosts de contenedores, y [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md), que recopila y visualiza métricas de rendimiento de instancias de Azure SQL Database.


## <a name="shared-functionality"></a>Funcionalidad compartida
Las siguientes herramientas de Azure proporcionan una funcionalidad crítica para los servicios de supervisión premium.  Varios servicios las comparten, lo que permite usar configuraciones y funcionalidades comunes en varios servicios.

### <a name="alerts"></a>Alertas
Las [alertas de Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) informan de forma anticipada de estados críticos y potencialmente aplica acciones correctivas.  Las reglas de alertas pueden usar datos de varios orígenes, como las métricas y los registros. Usan [grupos de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md) que contienen conjuntos únicos de destinatarios y acciones en respuesta a una alerta.  Según sus requisitos, puede tener acciones externas de lanzamiento de alertas que usen webhooks e integrarlas con las herramientas de ITSM.

### <a name="dashboards"></a>Paneles
Los [paneles de Azure](../azure-portal/azure-portal-dashboards.md) permiten combinar distintos tipos de datos en un único panel en Azure Portal y compartirlos con otros usuarios de Azure.  Por ejemplo, puede crear un panel que combina iconos que muestran un gráfico de métricas, una tabla de registros de actividad, un gráfico de uso de Application Insights y el resultado de una búsqueda de registros en Log Analytics.

También puede exportar datos de Log Analytics a [Power BI](https://docs.microsoft.com/power-bi/) para aprovechar las ventajas de visualizaciones adicionales y también poner los datos a disposición de otros dentro y fuera de la organización.

### <a name="metrics-explorer"></a>Explorador de métricas
Las [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) son valores numéricos generados por los recursos de Azure que le ayudarán a comprender el funcionamiento y el rendimiento del recurso. Puede enviar métricas a Log Analytics para realizar análisis con datos de otros orígenes.



### <a name="activity-logs"></a>Registros de actividad
Los [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) proporcionan datos sobre el funcionamiento de los recursos de Azure.  Esto incluye dicha información como cambios de configuración de los recursos, los incidentes de mantenimiento del servicio, las recomendaciones sobre la mejor utilización del recurso e información relacionada con las operaciones de escalado automático.  Puede ver registros de un recurso determinado en su página en Azure Portal o ver registros de varios recursos en el Explorador de registros de actividad.  También puede enviar registros de actividad a Log Analytics, para poder analizarlos con los datos recopilados por las soluciones de administración, los agentes de las máquinas virtuales y otros orígenes.


## <a name="example-scenarios"></a>Escenarios de ejemplo
A continuación se indican ejemplos de alto nivel que ilustran cómo se utilizarían las diferentes herramientas de supervisión en Azure para distintos escenarios.

### <a name="monitoring-a-web-application"></a>Supervisión de una aplicación web
Considere la posibilidad de implementar una aplicación web en Azure con App Services, Azure Storage y SQL Database.  Puede empezar por acceder a las [métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md) y los [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) de cada uno de estos recursos individuales en sus páginas de Azure Portal.  Esto podría incluir información crítica, como el número de solicitudes realizadas a la aplicación y el tiempo medio de respuesta, además de la identificación de cualquier cambio de configuración.

Después, vaya a Monitor en Azure Portal para ver las métricas y los registros de los distintos recursos de forma conjunta.  Cuando determine los parámetros estándar de las métricas, [cree reglas de alertas](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para que se le notifiquen de forma anticipada casos como, por ejemplo, aumentos del tiempo medio de respuesta más allá de un umbral.  Para obtener una vista rápida del rendimiento diario de su aplicación, cree un panel de Azure para mostrar gráficos de métricas que representen KPI críticos.

Para realizar una supervisión más detallada de la aplicación, [configúrela para Application Insights](../application-insights/quick-monitor-portal.md).  Ahora puede recopilar datos adicionales que proporcionan más información sobre el funcionamiento y el rendimiento de la aplicación.  Application Insights detecta las relaciones subyacentes entre los componentes de la aplicación para permitir la representación visual a través del [mapa de aplicación](../application-insights/app-insights-app-map.md) junto con una [traza de un extremo a otro](../application-insights/app-insights-transaction-diagnostics.md), a fin de diagnosticar el componente, la dependencia o la excepción exactos cuando se produce un problema.  Cree [conjuntos de disponibilidad](../application-insights/app-insights-monitor-web-app-availability.md) para probar de forma proactiva la aplicación desde distintas regiones.  Para ayudar a los desarrolladores, [habilite el generador de perfiles](../application-insights/enable-profiler-compute.md), para que pueda realizar un seguimiento de las solicitudes y excepciones hasta una línea de código específica.  

Para aumentar aún más la visibilidad de los servicios utilizados en la aplicación, agregue la [solución SQL Analytics](../log-analytics/log-analytics-azure-sql.md) para recopilar datos adicionales en Log Analytics. Más adelante, decide investigar la causa principal para los períodos de tiempo en que el rendimiento del sitio ha caído por debajo del umbral.  Escriba una consulta con Log Analytics para poner en correlación los datos de uso y rendimiento recopilados por Application Insights con los datos de configuración y rendimiento de los recursos de Azure que respaldan la aplicación.


### <a name="monitoring-virtual-machines"></a>Supervisión de máquinas virtuales
Tiene una combinación de máquinas virtuales Windows y Linux que se ejecutan en Azure.  Puede usar Azure Monitor para ver [registros de actividad](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) y [métricas a nivel de host](../monitoring-and-diagnostics/monitoring-overview-metrics.md) y después agregar la [extensión de Azure Diagnostics](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) a las máquinas virtuales, para recopilar métricas del sistema operativo invitado.  Después, cree [reglas de alertas](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para recibir una notificación con antelación cuando las métricas básicas, como el uso del procesador y la memoria, cruzan los umbrales.

Para recopilar más información sobre las máquinas virtuales que ejecutan una aplicación empresarial, [cree un área de trabajo de Log Analytics y habilite la extensión de VM](../log-analytics/log-analytics-quick-collect-azurevm.md) en cada equipo.  Configure la [recopilación de orígenes de datos diferentes](../log-analytics/log-analytics-data-sources.md) para la aplicación y [cree vistas](../log-analytics/log-analytics-view-designer.md) para informar sobre su funcionamiento y rendimiento diarios.  Después, [cree reglas de alertas](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para recibir una notificación cuando se reciban eventos de error particulares.  Para supervisar de forma continua el estado del agente instalado, agregue la [solución de administración Agent Health](../operations-management-suite/oms-solution-agenthealth.md).

Para obtener más información sobre la aplicación, [agregue el agente de dependencias](../operations-management-suite/operations-management-suite-service-map-configure.md) a las máquinas virtuales para agregarlas a [Service Map](../operations-management-suite/operations-management-suite-service-map.md).  Detecta los procesos críticos e identifica las conexiones entre equipos con otros servicios.  Después de una notificación de interrupción, use Service Map para realizar análisis forenses con el fin de identificar las máquinas concretas que experimentaron el problema.  Después, cree una [consulta de los datos de Log Analytics](../log-analytics/log-analytics-log-search-new.md) para identificar el problema en el futuro y cree una regla de alertas para recibir una notificación anticipada cuando se detecte una condición.



## <a name="next-steps"></a>pasos siguientes
Más información acerca de

* [Azure Monitor en un vídeo de Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introducción a Azure Monitor](monitoring-get-started.md)
* [Diagnósticos de Azure](../azure-diagnostics.md): si intenta diagnosticar problemas en su aplicación de Cloud Services, Virtual Machines, Conjuntos de escalado de máquinas virtuales o Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) : si está intentando diagnosticar problemas en su aplicación web de App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/): para analizar los datos de supervisión recopilados.
