<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Seguir el uso

## <a name="webclient"></a>Configurar el análisis de uso de web

Si todavía no lo ha hecho, [agregue Application Insights al proyecto web][start].

## <a name="usage"></a>Análisis de uso

En la hoja de información general de la aplicación, aparecen estos mosaicos de uso:

![](./media/appinsights/appinsights-47usage.png)

### Sesiones por explorador

Una *sesión* es un período que se inicia cuando un usuario abre una página del sitio web y termina si el usuario no envía ninguna solicitud web durante un tiempo de espera de 30 minutos.

Haga clic en las distintas opciones para ver el gráfico con mayor detalle.

### Vistas de página principales

Muestra los recuentos totales de las últimas 24 horas.

Haga clic en el mosaico de vistas de página para obtener un historial más detallado.

![](./media/appinsights/appinsights-49usage.png)

Haga clic en Intervalo de tiempo para ver un historial más extenso de hasta siete días.

Haga clic en un gráfico para ver otras métricas que puede mostrar.

![](./media/appinsights/appinsights-63usermetrics.png)

## Recuentos de página personalizados

De forma predeterminada, se realiza un recuento de página cada vez que se carga una página nueva en el explorador del cliente. Sin embargo, tal vez quiera realizar un recuento de otras vistas de página. Por ejemplo, una página puede mostrar su contenido en pestañas y a usted puede interesarle realizar el recuento de una página cuando el usuario cambia de pestaña. O el código JavaScript de la página puede cargar contenido nuevo sin cambiar la URL del explorador.

Inserte una llamada de JavaScript como esta en el lugar adecuado del código de cliente:

    appInsights.trackPageView(myPageName);

El nombre de página puede contener los mismos caracteres que una URL, aunque se ignorarán los elementos situados tras "\#" o "?".

## Inspeccionar eventos de vista de página individuales

Por lo general, Application Insights analiza la telemetría de vista de página y usted solo verá informes acumulativos, cuya media se ha calculado en función de todos los usuarios. Pero a efectos de depuración, también puede ver eventos de vista de página individuales.

En la hoja Búsqueda de diagnóstico, establezca Filtros en Vista de página.

![](./media/appinsights/appinsights-51searchpageviews.png)

Seleccione el evento que desea ver con mayor detalle.

> [WACOM.NOTE] Si desea usar [Buscar][diagnostic], tenga en cuenta que deben coincidir las palabras completas. "Acerc" y "cerca" no coinciden con "Acerca", aunque "Acerc\* " sí. Tampoco puede iniciar un término de búsqueda con un carácter comodín. Por ejemplo, "\*cerc" no coincide con "Acerca".

> [Más información sobre la búsqueda de diagnóstico][diagnostic]]

## Seguir el uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## Más información

-   [Application Insights: primeros pasos][start]
-   [Supervisar un servidor web activo en este momento][redfield]
-   [Supervisar el rendimiento de aplicaciones web][perf]
-   [Buscar registros de diagnóstico][diagnostic]
-   [Seguimiento de disponibilidad con pruebas web][availability]
-   [Seguir el uso][usage]
-   [Preguntas y respuestas y solución de problemas][qna]

<!--Link references-->

  [start]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [1]: ./media/appinsights/appinsights-49usage.png
  [2]: ./media/appinsights/appinsights-63usermetrics.png
  [3]: ./media/appinsights/appinsights-51searchpageviews.png
  [diagnostic]: ../app-insights-search-diagnostic-logs/
  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
