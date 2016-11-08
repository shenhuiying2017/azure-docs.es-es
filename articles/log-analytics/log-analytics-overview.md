---
title: ¿Qué es Log Analytics? | Microsoft Docs
description: Log Analytics es un servicio de Operations Management Suite (OMS) que le ayuda a recopilar y analizar los datos operativos generados por los recursos en los entornos locales o de nube. En este artículo se proporciona una breve información general sobre los distintos componentes de Log Analytics, además de vínculos a contenido detallado.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2016
ms.author: bwren

---
# ¿Qué es Log Analytics?
Log Analytics es un servicio de Operations Management Suite (OMS) que le ayuda a recopilar y analizar los datos generados por los recursos en los entornos locales o de nube. Proporciona información en tiempo real mediante el uso de paneles personalizados y de búsqueda para analizar fácilmente millones de registros en todas las cargas de trabajo y los servidores, independientemente de su ubicación física.

## Componentes de Log Analytics
En el centro de Log Analytics se encuentra el repositorio de OMS, el que está hospedado en la nube de Azure. Los datos se recopilan en el repositorio desde los orígenes conectados mediante la configuración de orígenes de datos y la incorporación de soluciones a la suscripción. Cada uno de los orígenes de datos y soluciones creará tipos de registros distintos que tendrán su propio conjunto de propiedades pero, de todos modos, podrían seguirse analizando en conjunto en consultas al repositorio. Esto le permite usar las mismas herramientas y los mismos métodos para trabajar con distintas variantes de datos recopilados por distintos orígenes.

![Repositorio de OMS](media/log-analytics-overview/overview.png)

Los orígenes conectados son los equipos y otros recursos que generan los datos que recopila Log Analytics. Esto puede incluir los agentes instalados en equipos [Windows](log-analytics-windows-agents.md) y [Linux](log-analytics-linux-agents.md) que se conectan directamente o agentes en un [grupo de administración de System Center Operations Manager conectado](log-analytics-om-agents.md). Log Analytics también puede recopilar datos desde el [almacenamiento de Azure](log-analytics-azure-storage.md).

Los [orígenes de datos](log-analytics-data-sources.md) son las distintas variantes de datos que se recopilan desde cada origen conectado. Esto incluye [datos de rendimiento](log-analytics-data-sources-performance-counters.md) y eventos desde agentes de [Windows](log-analytics-data-sources-windows-events.md) y Linux, además de orígenes como [registros de IIS](log-analytics-data-sources-iis-logs.md) y [registros de textos personalizados](log-analytics-data-sources-custom-logs.md). Usted configura cada origen de datos que desea recopilar y la configuración se entrega automáticamente a cada origen conectado.

## Análisis de datos de Log Analytics
La mayor parte de la interacción con Log Analytics se realizará a través del portal de OMS que se ejecuta en cualquier explorador y le proporciona acceso a las opciones de configuración y a varias herramientas para analizar y realizar acciones sobre los datos recopilados. En el portal, usará [búsquedas de registros](log-analytics-log-searches.md), en las que construye consultas para analizar los datos recopilados, [paneles](log-analytics-dashboards.md), los que puede personalizar vistas gráficas de las búsquedas más valiosas, y [soluciones](log-analytics-add-solutions.md), las que proporcionan herramientas de análisis y funcionalidad adicionales.

![Portal de OMS](media/log-analytics-overview/portal.png)

Log Analytics brinda una sintaxis de consulta para recuperar y consolidar rápidamente los datos en el repositorio. Puede crear y guardar las [búsquedas de registros](log-analytics-log-searches.md) para analizar directamente los datos en el portal de OMS o puede hacer que las búsquedas de registros se ejecuten automáticamente con el fin de crear una alerta, en caso de que los resultados de la consulta indiquen una condición importante.

![Búsqueda de registros](media/log-analytics-overview/log-search.png)

Para generar una vista gráfica rápida del estado del entorno general, puede agregar al [panel](log-analytics-dashboards.md) visualizaciones de las búsquedas de registros guardadas.

![Panel](media/log-analytics-overview/dashboard.png)

Con el fin de analizar datos que se encuentran fuera de Log Analytics, puede exportar los datos desde el repositorio de OMS a herramientas como [Power BI](log-analytics-powerbi.md) o Excel. También puede usar la [API de búsqueda de registros](log-analytics-log-search-api.md) para crear soluciones personalizadas que aprovechen los datos de Log Analytics o para integrarla con otros sistemas.

## Soluciones
Las soluciones agregan funcionalidad a Log Analytics. Se ejecutan principalmente en la nube y brindan un análisis de los datos recopilados en el repositorio de OMS. También pueden definir tipos de registros nuevos para recopilar y que se pueden analizar con las búsquedas de registros o mediante una interfaz de usuario adicional que brinda la solución en el panel de OMS.

![Solución de seguimiento de cambios](media/log-analytics-overview/change-tracking.png)

Hay disponibles soluciones para diversas funciones y puede examinar fácilmente estas soluciones disponibles y [agregarlas al área de trabajo de OMS](log-analytics-add-solutions.md) desde la galería de soluciones. Muchas funciones se implementarán automáticamente y comenzarán a funcionar de inmediato, mientras que otras pueden requerir algo de configuración.

![Galería de soluciones](media/log-analytics-overview/solution-gallery.png)

## Arquitectura de Log Analytics
Los requisitos de implementación de Log Analytics son mínimos, debido a que los componentes centrales están hospedados en la nube de Azure. Esto incluye el repositorio en adición de los servicios que le permiten correlacionar y analizar los datos recopilados. Se puede obtener acceso al portal desde cualquier explorador, por lo que no hay requisito de software cliente.

Debe instalar agentes en equipos [Windows](log-analytics-windows-agents.md) y [Linux](log-analytics-linux-agents.md), pero no se requiere un agente adicional para los equipos que ya son miembros de un [grupo de administración de SCOM conectado](log-analytics-om-agents.md). Los agentes de SCOM continuarán comunicándose con los servidores de administración, los que reenviarán los datos a Log Analytics. Sin embargo, algunas soluciones necesitarán agentes para comunicarse directamente con Log Analytics. La documentación de cada solución especificará sus requisitos de comunicación.

Cuando se [suscriba a Log Analytics](log-analytics-get-started.md), creará un área de trabajo de OMS. Considere el área de trabajo como un entorno de OMS único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Puede crear varias áreas de trabajo en su suscripción para admitir varios entornos, como los entornos de producción y prueba.

![Arquitectura de Log Analytics](media/log-analytics-overview/architecture.png)

## Pasos siguientes
* [Suscríbase a una cuenta gratis de Log Analytics](log-analytics-get-started.md) para probar su propio entorno.
* Vea los distintos [orígenes de datos](log-analytics-data-sources.md) disponibles para recopilar datos en el repositorio de OMS.
* [Examine las soluciones disponibles en la galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad a Log Analytics.

<!---HONumber=AcomDC_0525_2016-->