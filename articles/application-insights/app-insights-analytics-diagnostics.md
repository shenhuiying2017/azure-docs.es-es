---
title: "Diagnóstico inteligente de los cambios en el rendimiento de las aplicaciones web en Azure Application Insights| Microsoft Docs"
description: "Diagnóstico automático de subidas o pasos en la telemetría de rendimiento desde la aplicación web."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: mbullwin
ms.openlocfilehash: e0c8d712f03da0c5e47ea422b051c0b8734c6b21
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="diagnose-sudden-changes-in-your-app-telemetry"></a>Diagnóstico de los cambios repentinos en la telemetría de aplicación

*Esta característica se encuentra en versión preliminar.*

Diagnostique los cambios repentinos en el rendimiento o el uso de la aplicación web con un simple clic. La característica Smart Diagnostics está disponible siempre que crea un gráfico de tiempo en [Analytics](app-insights-analytics.md) en [Application Insights](app-insights-overview.md). Cada vez que se produce un cambio inusual en la tendencia de sus resultados, como una subida o una bajada, Smart Diagnostics identifica un patrón de dimensiones y valores relacionados que podrían explicar el cambio. Esto ayuda a diagnosticar el problema rápidamente. 

En este ejemplo, Smart Diagnostics ha identificado un patrón de valores de propiedad asociados con el cambio y resalta la diferencia entre los resultados con y sin ese patrón:

![resultados de diagnóstico de análisis de ejemplo](./media/app-insights-analytics-diagnostics/analytics-result.png)
 

## <a name="diagnose-data-changes"></a>Diagnóstico de cambios en los datos

1.  Ejecute una consulta en Analytics y represéntela como un gráfico de tiempo. 
2.  Haga clic en cualquier punto de subida resaltado, si lo hay.
 
    ![punto de subida](./media/app-insights-analytics-diagnostics/peak.png)

    Diagnostics tarda unos segundos en detectar un patrón.

3. La pestaña de resultados de diagnóstico muestra un patrón que puede explicar la discontinuidad de los datos.

    ![result](./media/app-insights-analytics-diagnostics/result.png)
 
    El texto muestra los valores de dimensión que parecen guardar una correlación con el cambio. En este ejemplo, está asociado a una solicitud determinada y una versión de explorador determinado.

    Observe también los dos componentes del gráfico, con el filtro true y false. El componente false muestra una tendencia sin cambios. En otras palabras, no hay ningún cambio en los resultados de telemetría, si excluimos la combinación problemática de dimensiones que ha identificado Diagnostics. Por el contrario, los resultados de esa combinación muestran un cambio significativo en el área resaltada de investigación. Esto muestra que Diagnostics ha encontrado una combinación de propiedades que explica el cambio.

4.  Si el patrón es complejo, debe mantener el mouse sobre **Mostrar todo** para ver las dimensiones.

    ![mostrar todo](./media/app-insights-analytics-diagnostics/show-all.png)
 
5.  En caso de que Diagnostics no encuentre ningún patrón significativo sobre el que informar, se presenta la página "sin resultados". En este momento, puede cambiar la consulta. Por ejemplo, podría restringir el intervalo de tiempo y la discretización de las consultas de Analytics para obtener un análisis más profundo y posiblemente mejores resultados.

Una vez que sabe que una determinada página de su sitio web tiene un problema en un explorador determinado, puede ir directamente a la página del problema e investigar los cambios recientes.

## <a name="try-the-demo"></a>Prueba de la demostración

[Haga clic aquí para ver una demostración](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA3VSTY%2FTQAy991dYPXWlLf0QIO2KIiGWA3duiMPsxEnMzhe2p6WIH48nVUsuGylRNPOe3%2FOzN5vFZgPfRhL4VZHPIGM%2BCdgHdESgpMjOKx0RnsgNKYuSF%2BjRaWUE7xKMGIoBgTpMSv2Z0jBxOWc1QBWEPjM4EMUCP2uc0A3x8E5HKMi%2BEQNC7oHRbIgKdJWdUk5vmr9PvdkArildit%2Fcrk0lBDjnyhBzk%2FKVxdTy0QhNY6RhDPYqdlCy9XMV96NjBZc68IH8y6Tzuf01iZxeIZ%2FI5DqMOYmaQQRXNUdz6qGb5WOdSKEXnOozHtEFK%2Bh0qnq5YQzGF9DcoinoqbcigkO0NOZRNGOZaaBkMuat5xznFOtULKhG%2BdrGlVDhy%2B8SMlsETV8dD6gTd0YrbsBrFq6U1v%2Filv4C%2FsJpRJuwUrQTZ0P7eIDOHLeD1X67e7%2Fe7dbbB9htH%2Ffbu4vQDfvhFez%2B8a1h%2F1f3VSy%2BJ4Ol1oN8X4qN0qMZWv44HJanzKFLeJIltKcRpcbomP7gbHNkdV2Xe1uqO3g%2BwzOl1c3PvbmMlC7KjKlry2GX0w4s%2FgFoo5%2BhBAMAAA%3D%3D&timespan=PT24H) en los datos de ejemplo.

## <a name="how-it-works"></a>Cómo funciona

Smart Diagnostics usa un algoritmo de aprendizaje automático avanzado no supervisado en la operación [DiffPatterns](app-insights-analytics-reference.md). Lo que hace es buscar patrones candidatos que puedan explicar los cambios en los datos. Luego, analiza el impacto de cada candidato sobre la métrica y muestra el patrón que guarda una mejor correlación con el cambio.

## <a name="no-diagnostic-points"></a>¿No hay puntos de diagnóstico?

Smart Diagnostics solo funciona cuando se satisfacen los siguientes criterios:

 * La opción Smart Diagnostics está activada. Mire debajo del icono Configuración de Analytics.
 * La opción Smart Diagnostics de la configuración de Analytics está seleccionada. 
 * Eje de tiempo: el eje x del gráfico debe ser de tipo `datetime`.
 * Gráfico de líneas o de áreas: Diagnostics solo funciona con estos tipos de gráfico. Use `| render timechart` o `| render areachart` al final de la consulta; o seleccione el gráfico de líneas o de áreas en el selector de lista desplegable.
 * Discontinuidad: debe haber una discontinuidad significativa en los datos.
 * Suficientes puntos para analizar.
 * No más de una cláusula de resumen en la consulta.
 * Ninguna cláusula del proyecto que contenga una definición de nombre delante de la cláusula de resumen.

 
 ## <a name="related-articles"></a>Artículos relacionados

 * [Tutorial de Analytics](app-insights-analytics-tour.md)
 * La [detección inteligente](app-insights-proactive-diagnostics.md) le avisa automáticamente de problemas en el rendimiento.