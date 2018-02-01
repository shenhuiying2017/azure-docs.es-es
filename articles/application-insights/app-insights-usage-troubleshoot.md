---
title: "Solución de problemas de análisis de uso en Azure Application Insights"
description: "Guía de solución de problemas: análisis de uso del sitio y las aplicaciones con Application Insights"
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin
ms.openlocfilehash: cb5f3052301b23eb10cd6b84ab6fae98bcc7ea18
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Solución de problemas de análisis de uso en Application Insights
¿Tiene alguna pregunta sobre las [herramientas de análisis de uso en Application Insights](app-insights-usage-overview.md): [usuarios, sesiones, eventos](app-insights-usage-segmentation.md), [embudos](usage-funnels.md), [flujos de usuarios](app-insights-usage-flows.md), [retención](app-insights-usage-retention.md) o cohortes? Estas son algunas respuestas.

## <a name="counting-users"></a>Recuento de usuarios
**Las herramientas de análisis de uso muestran que mi aplicación tiene una sesión y un usuario, pero sé que tiene varios usuarios y sesiones. ¿Cómo se solucionan estos recuentos incorrectos?**

Todos los eventos de telemetría de Application Insights tienen un [identificador de usuario anónimo](application-insights-data-model-context.md) y un [identificador de sesión](application-insights-data-model-context.md) como parte de las propiedades estándares. De forma predeterminada, todas las herramientas de análisis de uso de recuento de usuarios y sesiones se basan en estos identificadores. Si estas propiedades estándares no se rellenan con identificadores únicos para cada usuario y sesión de la aplicación, verá un recuento incorrecto de usuarios y sesiones en las herramientas de análisis de uso.

Si supervisa una aplicación web, la solución más sencilla consiste en agregar el [SDK de JavaScript de Application Insights](app-insights-javascript.md) a la aplicación y asegurarse de que el fragmento de código de script se carga en todas las páginas que desee supervisar. El SDK de JavaScript genera identificadores de usuario y sesión automáticamente y rellena los eventos de telemetría con ellos según se van enviando desde la aplicación.

Si supervisa un servicio web (sin interfaz de usuario), [cree un inicializador de telemetría que rellene las propiedades de los identificadores de usuario anónimo y sesión](app-insights-usage-send-user-context.md) según las nociones de su servicio sobre los usuarios únicos y las sesiones.

Si la aplicación envía [identificadores de usuarios autenticados](app-insights-api-custom-events-metrics.md#authenticated-users), con la herramienta Usuarios puede realizar el recuento en función de estos. En la lista desplegable "Mostrar", seleccione "Usuarios autenticados".

Actualmente, las herramientas de análisis de uso no admiten el recuento de usuarios o sesiones en función de propiedades distintas del identificador de usuario anónimo, el identificador de usuario autenticado o el identificador de sesión.

## <a name="naming-events"></a>Nomenclatura de los eventos
**Mi aplicación tiene miles de vistas de página y nombres de eventos personalizados distintos. Resulta difícil distinguirlos y las herramientas de análisis de uso dejan de responder con frecuencia. ¿Cómo se solucionan estos problemas de nomenclatura?**

Las herramientas de análisis de uso utilizan las vistas de página y los nombres de los eventos personalizados. La correcta nomenclatura de los eventos es fundamental para sacarles partido a estas herramientas. El objetivo es un equilibrio entre un número reducido de nombres demasiado genéricos ("Se hizo clic en el botón") y un número excesivo de nombres demasiado específicos ("Se hizo clic en el botón Editar de http://www.contoso.com/index").

Para realizar cambios en las vistas de página y los nombres de evento personalizados que envía la aplicación, debe cambiar el código fuente de la aplicación y volver a realizar la implementación. **Todos los datos de telemetría de Application Insights se almacenan durante 90 días y no se pueden eliminar**, por lo que los cambios realizados en los nombres de evento tardarán 90 días en manifestarse totalmente. Durante los 90 días después de los cambios en el nombre, en la telemetría aparecen tanto los nuevos nombres como los antiguos, por lo que es necesario ajustar las consultas y comunicarse con los equipos en consecuencia.

Si la aplicación envía demasiados nombres de vista de página, compruebe si estos se especifican manualmente en el código o si los envía automáticamente el SDK de JavaScript de Application Insights:

* Si se especifican manualmente en el código mediante [`trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), cambie el nombre a uno menos específico. Evite los errores comunes, como colocar la dirección URL en el nombre de la vista de página. En su lugar, use el parámetro de dirección URL de `trackPageView` API. Mueva los demás detalles del nombre de la vista de página a propiedades personalizadas.

* Si el SDK de JavaScript de Application Insights envía los nombres de vista de página automáticamente, puede cambiar los títulos de las páginas o enviar los nombres de vista de página manualmente. De forma predeterminada, el SDK envía el [título](https://developer.mozilla.org/docs/Web/HTML/Element/title) de cada página como nombre de la vista de página. Puede cambiar los títulos a otros más generales, pero tenga en cuenta la optimización del motor de búsqueda y otros efectos que este cambio podría tener. Especificar manualmente los nombres de vista de página con `trackPageView` API invalida los nombres recopilados automáticamente, por lo que podría enviar nombres más generales a la telemetría sin cambiar los títulos de página.   

Si la aplicación envía demasiados nombres de evento personalizados, cambie los nombres en el código a otros menos específicos. Una vez más, evite colocar direcciones URL y otros datos de página o dinámicos directamente en los nombres de evento personalizados. En su lugar, mueva estos detalles a las propiedades personalizadas del evento personalizado con `trackEvent` API. Por ejemplo, en lugar de `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, sugerimos algo parecido a `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>pasos siguientes

* [Introducción al análisis de uso](app-insights-usage-overview.md)

## <a name="get-help"></a>Obtención de ayuda
* [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/ms-application-insights)

