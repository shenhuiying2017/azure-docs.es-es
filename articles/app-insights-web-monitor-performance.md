<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Supervisar el rendimiento de aplicaciones web

*Application Insights se encuentra en su versión de vista previa.*

Asegúrese de que la aplicación tendrá un rendimiento correcto y descubra rápidamente los posibles errores. [Application Insights][Application Insights] le comunicará todas las excepciones y los problemas de rendimiento y le ayudará a diagnosticar las causas principales.

Application Insights puede supervisar aplicaciones web ASP.NET hospedadas en máquinas virtuales o locales, o en sitios web de Microsoft Azure.

-   [Configurar la supervisión de rendimiento][Configurar la supervisión de rendimiento]
-   [Consultar los datos][Consultar los datos]
-   [¿Qué significa todo esto?][¿Qué significa todo esto?]
-   [Problemas de diagnóstico][Problemas de diagnóstico]
-   [Pasos siguientes][Pasos siguientes]

## <a name="setup"></a>Configurar la supervisión de rendimiento

Si todavía no ha agregado Application Insights a un proyecto (es decir, si no dispone de ApplicationInsights.config), puede comenzar con uno de estos procedimientos:

-   [Agregar Application Insights a un proyecto de aplicación en Visual Studio][Application Insights]: opción recomendada. Puede realizar una supervisión pasiva del rendimiento, insertar el registro de diagnósticos y seguir el uso.
-   [Supervisar el rendimiento de un sitio web activo ahora][Supervisar el rendimiento de un sitio web activo ahora]: este procedimiento no precisa actualizar el proyecto de aplicación ni volver a implementar el sitio web.

## <a name="view"></a>Ver informes

Ejecute la aplicación con F5 y pruébela. Abra varias páginas.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![][]

Abra Application Insights desde un proyecto.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure][Haga clic con el botón secundario en el proyecto y abra el Portal de Azure]

Busque los datos en los mosaicos de **Estado de la aplicación**. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos][Haga clic en las distintas opciones para obtener más datos]

Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Cuando se implementa una aplicación, los datos se acumulan a menor velocidad.

Si no puede ver los datos en un primer momento, espere un minuto y haga clic en Actualizar.

### Explorar las métricas

Haga clic en un mosaico para ver su contenido con mayor detalle y los resultados durante más tiempo. Por ejemplo, haga clic en el mosaico Solicitudes y seleccione un intervalo de tiempo:

![Haga clic en las distintas opciones para obtener más datos y seleccionar un intervalo de tiempo][Haga clic en las distintas opciones para obtener más datos y seleccionar un intervalo de tiempo]

Haga clic en un gráfico para elegir las métricas que deben mostrarse:

![Haga clic en un gráfico para elegir las métricas][Haga clic en un gráfico para elegir las métricas]

> [AZURE.NOTE] **Desactive todas las métricas** para ver una selección completa de las opciones disponibles. Las métricas se organizan en grupos. Cuando se selecciona un miembro de un grupo, solo aparecen los demás miembros de dicho grupo.

## <a name="metrics"></a>¿Qué significa todo esto? Informes y mosaicos de rendimiento

Puede obtener una gran variedad de métricas de rendimiento. Comencemos por las que aparecen de forma predeterminada en la hoja de la aplicación.

### Solicitudes

El número de solicitudes HTTP que se reciben en un período específico. Compare este dato con los resultados de otros informes para ver cómo se comporta la aplicación cuando la carga varía.

Las solicitudes HTTP incluyen todas las solicitudes GET o POST de páginas, datos e imágenes.

Haga clic en el mosaico para obtener las cuentas de URL específicas.

### Tiempo de respuesta promedio

Mide el tiempo entre la entrada de una solicitud web y la devolución de la respuesta correspondiente.

Los puntos muestran una media móvil. Si se producen muchas solicitudes, es posible que algunas de ellas se desvíen de la media sin que en el gráfico se muestre ningún pico o descenso evidente.

Busque picos poco habituales. Por lo general, el tiempo de respuesta aumenta cuando aumentan las solicitudes. Si este aumento es desproporcionado, la aplicación puede alcanzar el límite de recursos (por ejemplo, de CPU o de un servicio que esté usando).

Haga clic en el mosaico para obtener los tiempos de URL específicas.

![][1]

### Solicitudes más lentas

![][2]

Muestra las solicitudes que pueden precisar un ajuste de rendimiento.

### Error en las solicitudes

![][3]

Un recuento de las solicitudes que inician excepciones no detectadas.

Haga clic en el mosaico para ver los detalles de errores específicos y seleccione una solicitud individual para revisarla de forma más exhaustiva.

Solo se conserva una muestra representativa de los errores para su inspección individual.

### Otras métricas

Si desea ver las demás métricas que puede mostrar, haga clic en un gráfico y desactive todas las métricas para ver el conjunto completo de opciones disponibles. Haga clic en (i) para ver la definición de cada métrica.

![Cancele todas las métricas para ver el conjunto completo][Cancele todas las métricas para ver el conjunto completo]

Cuando se selecciona una métrica, se ocultan las otras métricas que no pueden aparecer en su mismo gráfico.

## <a name="diagnosis"></a>Problemas de diagnóstico

Para buscar y diagnosticar problemas de rendimiento, lea estas sugerencias:

-   Configure [pruebas web][pruebas web] para recibir una alerta si el sitio web deja de funcionar, o si responde incorrecta o lentamente.
-   Compare el recuento de Solicitudes con otras métricas para ver si la lentitud de respuesta o los errores se encuentran relacionados con la carga.
-   [Inserte y busque instrucciones de seguimiento][Inserte y busque instrucciones de seguimiento] en el código para facilitar la identificación de los problemas.

## <a name="next"></a>Pasos siguientes

[Pruebas web][pruebas web]: reciba solicitudes web en su aplicación de forma periódica desde distintos lugares del mundo.

[Capture y busque seguimientos de diagnóstico][Inserte y busque instrucciones de seguimiento]: inserte llamadas de seguimiento y cribe los resultados para identificar los problemas.

[Seguimiento de uso][Seguimiento de uso]: averigüe cuántas personas usan una aplicación.

[Solución de problemas][Solución de problemas]: y preguntas y respuestas.

## Más información

-   [Application Insights: primeros pasos][Application Insights]
-   [Supervisar un servidor web activo en este momento][Supervisar el rendimiento de un sitio web activo ahora]
-   [Supervisar el rendimiento de aplicaciones web][Supervisar el rendimiento de aplicaciones web]
-   [Buscar registros de diagnóstico][Inserte y busque instrucciones de seguimiento]
-   [Seguimiento de disponibilidad con pruebas web][pruebas web]
-   [Seguir el uso][Seguimiento de uso]
-   [Preguntas y respuestas y solución de problemas][Solución de problemas]

<!--Link references-->

  [Application Insights]: ../app-insights-start-monitoring-app-health-usage/
  [Configurar la supervisión de rendimiento]: #setup
  [Consultar los datos]: #view
  [¿Qué significa todo esto?]: #metrics
  [Problemas de diagnóstico]: #diagnosis
  [Pasos siguientes]: #next
  [Supervisar el rendimiento de un sitio web activo ahora]: ../app-insights-monitor-performance-live-website-now/
  []: ./media/appinsights/appinsights-09eventcount.png
  [Haga clic con el botón secundario en el proyecto y abra el Portal de Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Haga clic en las distintas opciones para obtener más datos]: ./media/appinsights/appinsights-41firstHealth.png
  [Haga clic en las distintas opciones para obtener más datos y seleccionar un intervalo de tiempo]: ./media/appinsights/appinsights-48metrics.png
  [Haga clic en un gráfico para elegir las métricas]: ./media/appinsights/appinsights-61perfchoices.png
  [1]: ./media/appinsights/appinsights-42reqs.png
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-46failed.png
  [Cancele todas las métricas para ver el conjunto completo]: ./media/appinsights/appinsights-62allchoices.png
  [pruebas web]: ../app-insights-monitor-web-app-availability/
  [Inserte y busque instrucciones de seguimiento]: ../app-insights-search-diagnostic-logs/
  [Seguimiento de uso]: ../app-insights-web-track-usage/
  [Solución de problemas]: ../app-insights-troubleshoot-faq/
  [Supervisar el rendimiento de aplicaciones web]: ../app-insights-web-monitor-performance/
