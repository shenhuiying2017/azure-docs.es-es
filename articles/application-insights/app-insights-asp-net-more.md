<properties 
	pageTitle="Uso más completo de Application Insights" 
	description="Aquí se presentan algunas características que puede explorar una vez que sepa utilizar Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2016" 
	ms.author="awills"/>

# Más telemetría desde Application Insights

Este es un resumen de las características que quizá no ha probado de [Application Insights de Visual Studio](app-insights-overview.md). Suponemos que ya [ha empezado a utilizarlo](app-insights-asp-net.md). Application Insights le permite supervisar la disponibilidad, el rendimiento y el uso de su aplicación web. Con los comentarios que obtendrá sobre el rendimiento y la eficacia de la aplicación en su entorno natural, pueda tomar decisiones meditadas sobre la dirección del diseño en cada ciclo de vida de desarrollo.

## Más telemetría

||
|---|---
|[**Pruebas de disponibilidad**](app-insights-monitor-web-app-availability.md)<br/>Envíe las solicitudes HTTP de la aplicación web a intervalos regulares desde todo el mundo. Recibirá una alerta si la respuesta es lenta o poco confiable.| 
|[**Llamadas de dependencia y AJAX**](app-insights-asp-net-dependencies.md)<br/>Supervise consultas SQL y llamadas a REST u otros recursos.|
|[**Excepciones**](app-insights-asp-net-exceptions.md)<br/>Cuente excepciones controladas y no controladas, obtenga seguimientos de pila y haga clic en el código.|
|[**Páginas web**](app-insights-javascript.md)<br/>Supervise el uso y el rendimiento de las páginas mediante la instrumentación de las páginas web.
|**Rendimiento de hosts: [Diagnósticos de Azure](app-insights-azure-diagnostics.md), [contadores de rendimiento de Windows](app-insights-web-monitor-performance.md)**<br/>Vea el uso de CPU y otras métricas del contexto. |![](./media/app-insights-asp-net-more/04.png)
|[**API del SDK: telemetría personalizada**](app-insights-api-custom-events-metrics.md)<br/>Envíe sus propios eventos y métricas para obtener una vista más detallada del rendimiento y el uso de la aplicación, tanto en código de servidor como de cliente.|
|[**Integración de registros**](app-insights-asp-net-trace-logs.md)<br/>Si usa una plataforma de registro como Log4Net, NLog o System.Diagnostics.Trace, puede instalar un adaptador que envíe estos registros a Application Insights junto con otros informes de telemetría.|
|[**Procesadores de telemetría**](app-insights-api-filtering-sampling.md)<br/>Filtre, modifique o amplíe la telemetría enviada desde el SDK en su aplicación. |


## Análisis y presentación eficaces

||
|---|---
|[**Búsqueda de diagnóstico por datos de instancia**](app-insights-visual-studio.md)<br/>Busque y filtre eventos como solicitudes, excepciones, llamadas de dependencia, seguimientos de registro y vistas de página. En Visual Studio, vaya al código desde los seguimientos de la pila.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Explorador de métricas para datos agregados**](app-insights-metrics-explorer.md)<br/>Explore, filtre y segmente datos agregados, como las tasas de solicitudes, errores y excepciones; los tiempos de respuesta y los tiempos de carga de página.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Paneles**](app-insights-dashboards.md#dashboards)<br/>Combine datos de varios recursos y compártalos con otros. Ideal para aplicaciones de varios componentes y para la presentación continua en la sala de reuniones. |![Ejemplo de paneles](./media/app-insights-asp-net/62.png)
|[**Secuencia en directo**](app-insights-metrics-explorer.md#live-stream)<br/>Al implementar una nueva compilación, consulte estos indicadores de rendimiento casi en tiempo real para asegurarse de que todo funciona según lo esperado.|![Ejemplo de análisis](./media/app-insights-asp-net-more/050.png)
|[**Análisis**](app-insights-analytics.md)<br/>Responda preguntas complejas acerca del uso y el rendimiento de su aplicación mediante este eficaz lenguaje de consulta.|![Ejemplo de análisis](./media/app-insights-asp-net-more/010.png)
|[**Alertas automáticas y manuales**](app-insights-alerts.md)<br/>Las alertas automáticas se adaptan a los patrones normales de telemetría de la aplicación y se activan cuando algo no responde al patrón habitual. También puede establecer alertas sobre niveles de métricas estándares o personalizadas.|![Ejemplo de alerta](./media/app-insights-asp-net-more/020.png)

## Administración de datos

|||
|---|---|
|[**Exportación continua**](app-insights-export-telemetry.md)<br/>Copie todos los datos de telemetría en el almacenamiento para poder analizarlos a su ritmo.|
|**API de acceso a datos**<br/>Próximamente.|
|[**Muestreo**](app-insights-sampling.md)<br/>Reduce la velocidad de los datos y le permite permanecer dentro del límite de su plan de tarifa.|![Icono de muestreo](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0608_2016-->