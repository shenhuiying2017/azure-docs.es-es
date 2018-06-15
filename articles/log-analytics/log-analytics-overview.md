---
title: ¿Qué es Azure Log Analytics? | Microsoft Docs
description: Log Analytics es un servicio de Azure que le ayuda a recopilar y analizar los datos operativos generados por los recursos en los entornos locales o de nube.  En este artículo se proporciona una breve información general sobre los distintos componentes de Log Analytics, además de vínculos a contenido detallado.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: bwren
ms.openlocfilehash: b951d41dab4d349a8d648e7eaa7e23b73ced2ced
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
ms.locfileid: "29939049"
---
# <a name="what-is-azure-log-analytics"></a>¿Qué es Azure Log Analytics?
Log Analytics desempeña un papel fundamental en la administración de Azure al recopilar datos de telemetría y otros desde diversos orígenes y proporcionar un motor de análisis y un lenguaje de consultas que ofrece información sobre el funcionamiento de las aplicaciones y los recursos.  También puede interactuar directamente con los datos de Log Analytics a través de vistas y búsquedas de registros, o puede usar herramientas de análisis en otros servicios de Azure que almacenen sus datos en Log Analytics como Application Insights o Azure Security Center.  

Log Analytics requiere una configuración mínima y ya está integrada con otros servicios de Azure.  Solo tiene que crear un área de trabajo para habilitar la recopilación.  A continuación, puede instalar agentes en las máquinas virtuales para incluirlos en el área de trabajo y habilitar soluciones de administración que incluyan una lógica para proporcionar información adicional en aplicaciones diferentes.  En segundo plano, los tipos de datos están predefinidos o se crean automáticamente a medida que los datos se recopilan.


## <a name="role-in-monitoring"></a>Rol en la supervisión

Los diferentes servicios de supervisión de Azure se describen en [Supervisión de recursos y aplicaciones de Azure](../monitoring-and-diagnostics/monitoring-overview.md).  Log Analytics desempeña un papel central al consolidar los datos de supervisión de orígenes diferentes y ofrecer un eficaz lenguaje de consulta para la consolidación y el análisis.  

Sin embargo, Log Analytics no se limita a la supervisión de los recursos de Azure.  Puede recopilar datos de recursos que sean locales o estén en otras nubes para crear un entorno híbrido de supervisión y puede conectar directamente a System Center Operations Manager para recopilar la telemetría de los agentes existentes.  Las herramientas de análisis de Log Analytics como las búsquedas de registros, las vistas y las soluciones de administración funcionan con todos los datos recopilados, lo que permite centralizar el análisis de todo el entorno.



## <a name="data-collection"></a>Colección de datos
Log Analytics recopila datos de diversos orígenes.  Una vez recopilados, se organizan en tablas independientes para cada tipo, lo que permite que todos los datos se puedan analizar conjuntamente con independencia de su origen.

A continuación se indican algunos métodos para recopilar datos en Log Analytics:

- Configurar Azure Monitor para copiar la métrica y los registros que recopila de los recursos de Azure.
- Los agentes de las máquinas virtuales [Windows](log-analytics-windows-agent.md) y [Linux](log-analytics-linux-agents.md) envían datos de telemetría desde el sistema operativo invitado y las aplicaciones a Log Analytics de acuerdo con los [orígenes de datos](log-analytics-data-sources.md) que se configuran.  
- Conectar un [grupo de administración de System Center Operations Manager](log-analytics-om-agents.md) a Log Analytics para recopilar datos de los agentes.
- Los servicios de Azure como [Application Insights](https://docs.microsoft.com/azure/application-insights/) y [Azure Security Center](https://docs.microsoft.com/azure/security-center/) almacenan sus datos directamente en Log Analytics sin necesidad de ninguna configuración.
- Escribir los datos desde la línea de comandos de PowerShell o el [runbook de Azure Automation](../automation/automation-runbook-types.md) mediante cmdlets de Log Analytics.
- Si tiene requisitos personalizados, puede usar la [API HTTP Data Collector](log-analytics-data-collector-api.md) para escribir datos en Log Analytics desde un cliente de la API REST.


![Componentes de Log Analytics](media/log-analytics-overview/collecting-data.png)

## <a name="add-functionality-with-management-solutions"></a>Adición de funcionalidad con soluciones de administración
[Las soluciones de administración](log-analytics-add-solutions.md) proporcionan una lógica preconfigurada para un escenario o un producto determinado.  Pueden recopilar datos adicionales en Log Analytics o datos de proceso que ya se han recopilado.  Por lo general, incluirán una vista para ayudar a analizar estos datos adicionales.  Existen soluciones disponibles para diversas funciones y continuamente se agregan otras nuevas.  Puede examinar fácilmente las soluciones disponibles y [agregarlas al área de trabajo](log-analytics-add-solutions.md) desde Azure Marketplace.  

![Marketplace](media/log-analytics-overview/solutions.png)


## <a name="query-language"></a>Lenguaje de consulta

Log Analytics incluye un [lenguaje de consulta enriquecido](http://docs.loganalytics.io) para recuperar, consolidar y analizar rápidamente los datos.  Puede crear y probar las consultas que utilizan los [portales de búsqueda de registros o Advanced Analytics](log-analytics-log-search-portals.md) y, a continuación, o bien analizar los datos directamente mediante estas herramientas o guardar las consultas para usarlas con las visualizaciones o las alertas, o exportarlas a otras herramientas como Power BI o Excel.

El lenguaje de consultas de Log Analytics es adecuado para búsquedas de registros simples, pero también incluye funciones avanzadas, como agregaciones, combinaciones y análisis inteligente. Puede aprender rápidamente el lenguaje de consulta usando los [diversos tutoriales](https://docs.loganalytics.io/docs/Learn/Tutorials) disponibles.  Se proporciona orientación concreta a los usuarios que ya están familiarizados con [SQL](https://docs.loganalytics.io/docs/Learn/References/SQL-to-Azure-Log-Analytics) y [Splunk](https://docs.loganalytics.io/docs/Learn/References/Splunk-to-Azure-Log-Analytics).

![Búsqueda de registros](media/log-analytics-overview/analytics-query.png)


## <a name="visualize-log-analytics-data"></a>Visualización de datos de Log Analytics

Las [vistas de Log Analytics](log-analytics-view-designer.md) presentan visualmente los datos de las búsquedas de registro.  Cada vista incluye una combinación de visualizaciones, como barras y líneas gráficas, además de listas que resumen los datos críticos.  Las [soluciones de administración ](#add-functionality-with-management-solutions) incluyen vistas que resumen los datos para una aplicación concreta; puede crear sus propias vistas para presentar los datos de una búsqueda de registros de Log Analytics.

![Vídeos de Log Analytics](media/log-analytics-overview/view.png)

También puede anclar los resultados de una consulta de Log Analytics a un [panel de Azure](../azure-portal/azure-portal-dashboards.md), lo que le permite combinar iconos de distintos servicios de Azure.  Incluso puede anclar una vista de Log Analytics a un panel.

![Paneles de Azure](media/log-analytics-overview/dashboard.png)

## <a name="creating-alerts-from-log-analytics-data"></a>Creación de alertas con datos de Log Analytics

Use [Azure Alerts](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) para que se le notifiquen proactivamente las condiciones en los datos de Log Analytics que son importantes para usted.  Una consulta se ejecuta automáticamente en intervalos programados y se crea una alerta si los resultados coinciden con criterios específicos.  Así se permite combinar las alertas de Log Analytics con otros orígenes, como las alertas casi en tiempo real de [Azure Monitor](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md) y las excepciones de aplicación de [Application Insights](../application-insights/app-insights-alerts.md), compartiendo [grupos de acción](../monitoring-and-diagnostics/monitoring-action-groups.md) para responder a las condiciones de alerta.

![Alerta](media/log-analytics-overview/alerts.png)


## <a name="using-log-analytics-data-in-other-services"></a>Uso de datos de Log Analytics en otros servicios
Los servicios como Application Insights y Azure Security Center almacenan sus datos directamente en Log Analytics.  Normalmente, podrá interactuar con las herramientas de análisis enriquecido que estos servicios proporcionan, pero también puede utilizar las consultas de Log Analytics para acceder a sus datos y, si lo desea, combinarlos con datos de otros servicios.  

Por ejemplo, la siguiente vista es de Application Insights.  Si hace clic en el icono en la esquina superior derecha, se inicia la consola de análisis de Log Analytics con las consultas utilizadas por el gráfico.

![Application Insights](media/log-analytics-overview/application-insights.png)


## <a name="exporting-log-analytics-data"></a>Exportación de datos de Log Analytics

Log Analytics también hace que sus datos estén disponibles fuera de Azure.  Puede configurar [Power BI](log-analytics-powerbi.md) para importar los resultados de una consulta a intervalos programados, lo que le permite aprovechar sus características, como la combinación de datos de diferentes orígenes y el uso compartido de informes en los dispositivos web y móviles.  También puede usar la [API de búsqueda de registros](log-analytics-log-search-api.md) para crear soluciones personalizadas que aprovechen los datos de Log Analytics o para integrarla con otros sistemas.

Puede usar [Logic Apps](../logic-apps/logic-apps-overview.md) en Azure para crear flujos de trabajo personalizados basados en datos de Log Analytics.  En el caso de una lógica más compleja basada en PowerShell, puede usar [runbooks de Azure Automation](../automation/automation-runbook-types.md).

![Power BI](media/log-analytics-overview/export.png)



## <a name="next-steps"></a>Pasos siguientes
- Empiece por [recopilar datos de máquinas virtuales de Azure](log-analytics-quick-collect-azurevm.md).
- Siga un [tutorial acerca de cómo analizar datos de Log Analytics con una consulta simple](log-analytics-tutorial-viewdata.md).
* [Examine las soluciones disponibles](log-analytics-add-solutions.md) para agregar funcionalidad a Log Analytics.

