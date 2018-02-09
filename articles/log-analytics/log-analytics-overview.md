---
title: "¿Qué es Log Analytics en Azure? | Microsoft Docs"
description: "Log Analytics es un servicio de Azure que le ayuda a recopilar y analizar los datos operativos generados por los recursos en los entornos locales o de nube.  En este artículo se proporciona una breve información general sobre los distintos componentes de Log Analytics, además de vínculos a contenido detallado."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: bd90b460-bacf-4345-ae31-26e155beac0e
ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: bwren
ms.openlocfilehash: a95528f5bd259a36ea96c7bc0660ca082c09d6e6
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="what-is-log-analytics"></a>¿Qué es Log Analytics?
Log Analytics es un servicio de Azure que supervisa los entornos local y en la nube para mantener su disponibilidad y rendimiento.  Recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión, para proporcionar análisis entre varios orígenes.  En este artículo se proporciona una breve explicación del valor que ofrece Log Analytics, información general sobre cómo funciona y vínculos a contenido más detallado para que pueda profundizar más.

## <a name="is-log-analytics-for-you"></a>¿Es Log Analytics adecuado en su caso?
Si no dispone actualmente de ningún tipo de supervisión en su entorno de Azure, debe comenzar con [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md), que recopila y analiza los datos de supervisión de los recursos de Azure.  Log Analytics puede [recopilar datos de Azure Monitor](log-analytics-azure-storage.md) para correlacionarlos con otros datos y proporcionar análisis adicional.

Si quiere supervisar el entorno local o dispone de un proceso de supervisión en el que se usan servicios como Azure Monitor o System Center Operations Manager, Log Analytics puede aportar un valor significativo.  Esta solución recopila datos directamente de los agentes y también de estas otras herramientas en un único repositorio.  Las herramientas de análisis de Log Analytics, como búsquedas de registros, vistas y soluciones funcionan con todos los datos recopilados que proceden del análisis centralizado de todo el entorno.


## <a name="using-log-analytics"></a>Uso de Log Analytics
Puede acceder a Log Analytics a través de Azure Portal, que se ejecuta en cualquier explorador, y le proporciona acceso a las opciones de configuración y a varias herramientas para analizar y realizar acciones sobre los datos recopilados.  En el portal, puede aprovechar las [búsquedas de registros](log-analytics-log-searches.md), en las que construye consultas para analizar los datos y [paneles](log-analytics-dashboards.md) recopilados, que puede personalizar con vistas gráficas de las búsquedas más valiosas, y [soluciones](log-analytics-add-solutions.md), que son las que proporcionan herramientas de análisis y funcionalidad adicionales.

La imagen siguiente muestra la pantalla Información general que muestra la información de resumen de las [soluciones](#add-functionality-with-management-solutions) instaladas en el área de trabajo.  Puede hacer clic en cualquier icono para profundizar en los datos de esa solución.

![Portal de OMS](media/log-analytics-overview/portal.png)

Log Analytics incluye un lenguaje de consulta para recuperar y consolidar rápidamente los datos en el repositorio.  Puede crear y guardar las [búsquedas de registros](log-analytics-log-searches.md) para analizar directamente los datos en el portal o puede hacer que las búsquedas de registros se ejecuten automáticamente con el fin de crear una alerta, en caso de que los resultados de la consulta indiquen una condición importante.

![Búsqueda de registros](media/log-analytics-overview/log-search.png)

Con el fin de analizar datos que se encuentran fuera de Log Analytics, puede exportar los datos a herramientas como [Power BI](log-analytics-powerbi.md) o Excel.  También puede usar la [API de búsqueda de registros](log-analytics-log-search-api.md) para crear soluciones personalizadas que aprovechen los datos de Log Analytics o para integrarla con otros sistemas.

## <a name="add-functionality-with-management-solutions"></a>Adición de funcionalidad con soluciones de administración
Las [soluciones de administración](log-analytics-add-solutions.md) agregan funcionalidad a Log Analytics, lo cual le proporciona datos y herramientas de análisis adicionales.  También pueden definir tipos de registros nuevos para recopilar, que se pueden analizar con las búsquedas de registros o mediante una interfaz de usuario adicional que brinda la solución en el panel de OMS.  La imagen del ejemplo siguiente muestra la [solución de seguimiento de cambios](log-analytics-change-tracking.md).

![Solución de seguimiento de cambios](media/log-analytics-overview/change-tracking.png)

Existen soluciones disponibles para diversas funciones y continuamente se agregan otras nuevas.  Puede examinar fácilmente las soluciones disponibles y [agregarlas al área de trabajo](log-analytics-add-solutions.md) desde Azure Marketplace.  Muchas soluciones se implementan automáticamente y comienzan a funcionar de inmediato, mientras que otras pueden requerir algo de configuración.

![Galería de soluciones](media/log-analytics-overview/solution-gallery.png)

## <a name="log-analytics-components"></a>Componentes de Log Analytics
En el centro de Log Analytics se encuentra un repositorio de datos recopilados que está hospedado en la nube de Azure.  Los datos se recopilan desde los orígenes conectados mediante la configuración de orígenes de datos y la incorporación de soluciones a la suscripción.  Cada uno de los orígenes de datos y soluciones creará tipos de registros distintos que tendrán su propio conjunto de propiedades pero, de todos modos, podrían seguirse analizando en conjunto en consultas al repositorio.  Esto le permite usar las mismas herramientas y los mismos métodos para trabajar con distintas variantes de datos recopilados por distintos orígenes.

![Componentes de Log Analytics](media/log-analytics-overview/overview.png)

Los orígenes conectados son los equipos y otros recursos que generan los datos que recopila Log Analytics.  Esto puede incluir los agentes instalados en equipos [Windows](log-analytics-windows-agent.md) y [Linux](log-analytics-linux-agents.md) que se conectan directamente o agentes en un [grupo de administración de System Center Operations Manager conectado](log-analytics-om-agents.md).  Para los recursos de Azure, Log Analytics recopila datos de [Azure Monitor y Diagnósticos de Azure](log-analytics-azure-storage.md).

[orígenes de datos](log-analytics-data-sources.md) son las distintas variantes de datos que se recopilan desde cada origen conectado.  Esto incluye [eventos](log-analytics-data-sources-windows-events.md) y [datos de rendimiento](log-analytics-data-sources-performance-counters.md) de agentes de [Windows](log-analytics-data-sources-windows-events.md) y Linux, además de orígenes como [registros de IIS](log-analytics-data-sources-iis-logs.md) y [registros de texto personalizado](log-analytics-data-sources-custom-logs.md).  Usted configura cada origen de datos que desea recopilar y la configuración se entrega automáticamente a cada origen conectado.

Si tiene requisitos personalizados, puede usar la [API HTTP Data Collector](log-analytics-data-collector-api.md) para escribir datos en el repositorio desde un cliente de API de REST.

## <a name="log-analytics-architecture"></a>Arquitectura de Log Analytics
Los requisitos de implementación de Log Analytics son mínimos, debido a que los componentes centrales están hospedados en la nube de Azure.  Esto incluye el repositorio en adición de los servicios que le permiten correlacionar y analizar los datos recopilados.  Se puede obtener acceso al portal desde cualquier explorador, por lo que no hay requisito de software cliente.

Debe instalar agentes en equipos [Windows](log-analytics-windows-agent.md) y [Linux](log-analytics-linux-agents.md), pero no se requiere ningún agente adicional para los equipos que ya son miembros de un [grupo de administración de System Center Operations Manager conectado](log-analytics-om-agents.md).  Los agentes de Operations Manager continuarán comunicándose con los servidores de administración, los cuales reenviarán los datos a Log Analytics.  Sin embargo, algunas soluciones necesitarán agentes para comunicarse directamente con Log Analytics.  La documentación de cada solución especificará sus requisitos de comunicación.

Cuando se [suscriba a Log Analytics](log-analytics-get-started.md), creará un área de trabajo.  Considere el área de trabajo como un entorno de Log Analytics único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Puede crear varias áreas de trabajo en su suscripción para admitir varios entornos, como los entornos de producción y prueba.

![Arquitectura de Log Analytics](media/log-analytics-overview/architecture.png)

## <a name="next-steps"></a>pasos siguientes
* [Suscríbase a una cuenta gratis de Log Analytics](log-analytics-get-started.md) para probar su propio entorno.
* Vea los distintos [orígenes de datos](log-analytics-data-sources.md) disponibles para recopilar datos en el repositorio de Log Analytics.
* [Examine las soluciones disponibles en la galería de soluciones](log-analytics-add-solutions.md) para agregar funcionalidad a Log Analytics.

