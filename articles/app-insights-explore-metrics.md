<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Exploración de las métricas

Si todavía no [ha configurado su proyecto web para Application Insights][ha configurado su proyecto web para Application Insights], puede hacerlo ahora.

A continuación se muestran los informes que puede esperar consultar en el cuadro de aplicaciones en Application Insights. Para llegar a la página desde Visual Studio, haga clic con el botón secundario en el proyecto web y elija Open Application Insights. Para llegar a él desde el panel de inicio en vista previa de Microsoft Azure, elija Examinar, Application Insights y, a continuación, seleccione el proyecto.

-   [Estado de la aplicación][Estado de la aplicación]
-   [Análisis de uso][Análisis de uso]
-   [Pasos siguientes][Pasos siguientes]

## <a name="health"></a>Estado de la aplicación

El estado de la aplicación está supervisado por la instrumentación del código de la aplicación.

![][]

### Tiempo medio de respuesta

Mide el tiempo que transcurre entre la entrada de una solicitud web en la aplicación y la devolución de la respuesta.

Los puntos muestran una media móvil. Si hay muchas solicitudes, puede haber algunas que se desvíen de la media sin un máximo o mínimo obvio en el gráfico.

Busque máximos inusuales. En general, espere que el tiempo de respuesta se incremente con un aumento en las solicitudes. Si el aumento es desproporcionado, la aplicación puede estar alcanzando un límite de recursos como la CPU o la capacidad de un servicio que utiliza.

### Solicitudes

El número de solicitudes recibidas en un período especificado. Compárelo con los resultados de otros gráficos para ver cómo la aplicación se comporta cuando la carga varía.

### Pruebas web

![][1]

Las [pruebas web][pruebas web] muestran los resultados de las solicitudes web enviadas al servidor a intervalos regulares desde los servidores de Application Insights de todo el mundo.

Compruebe si los resultados varían junto con el recuento de solicitudes.

[Cómo configurar pruebas web][pruebas web].

### Solicitudes más lentas

![][2]

Muestra qué solicitudes pueden necesitar una optimización del rendimiento.

### Búsqueda de diagnóstico

![][3]

Si ha [configurado el registro de diagnóstico][configurado el registro de diagnóstico], haga clic para ver los últimos eventos.

### Solicitudes con error

![][4]

Un recuento de solicitudes que inician excepciones no detectadas.

## <a name="usage"></a>Análisis de uso

![][5]

Los datos de uso proceden parcialmente del servidor y parcialmente de los [scripts de las páginas web][ha configurado su proyecto web para Application Insights].

### Sesiones por explorador

Una *sesión* es un período que se inicia cuando el usuario abre cualquier página en el sitio web y que finaliza cuando el usuario ya no envía ninguna solicitud web durante un período de tiempo de espera de 30 minutos.

Haga clic para ampliar el gráfico.

### Vistas de la página superior

Muestra el recuento total en las últimas 24 horas.

Haga clic para ver los gráficos de las vistas de página en la semana anterior.

### Reorganización de los mosaicos

![Elección de ajustes, personalizar][Elección de ajustes, personalizar]

## <a name="next"></a>Pasos siguientes

[Configuración de pruebas web][pruebas web]

[Captura y búsqueda de registros de diagnóstico][configurado el registro de diagnóstico]

[Solución de problemas][Solución de problemas]

## Más información

-   [Application Insights][Application Insights]
-   [Incorporación de Application Insights al proyecto][ha configurado su proyecto web para Application Insights]
-   [Supervisión inmediata de un servidor web activo][Supervisión inmediata de un servidor web activo]
-   [Exploración de métricas en Application Insights][Exploración de métricas en Application Insights]
-   [Búsqueda del registro de diagnóstico][configurado el registro de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][pruebas web]
-   [Seguimiento de uso con eventos y métricas][Seguimiento de uso con eventos y métricas]
-   [Preguntas y repuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [ha configurado su proyecto web para Application Insights]: ../app-insights-monitor-application-health-usage/
  [Estado de la aplicación]: #health
  [Análisis de uso]: #usage
  [Pasos siguientes]: #next
  []: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [pruebas web]: ../app-insights-monitor-web-app-availability/
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [configurado el registro de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Elección de ajustes, personalizar]: ./media/appinsights/appinsights-21-customizeblade.png
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Supervisión inmediata de un servidor web activo]: ../app-insights-monitor-performance-live-website-now/
  [Exploración de métricas en Application Insights]: ../app-insights-explore-metrics/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-track-usage-custom-events-metrics/
