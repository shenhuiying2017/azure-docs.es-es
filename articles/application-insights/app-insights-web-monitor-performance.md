---
title: "Supervisar el estado y el uso de su aplicación con Application Insights"
description: "Introducción a Application Insights. Analice el uso, la disponibilidad y el rendimiento de sus aplicaciones web de Microsoft Azure o local."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: mbullwin
ms.openlocfilehash: 32000f5a85c84913aa820df00f1bb7f877bf037f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-performance-in-web-applications"></a>Supervisar el rendimiento de aplicaciones web


Asegúrese de que la aplicación tendrá un rendimiento correcto y descubra rápidamente los posibles errores. [Application Insights][start] le indicará las excepciones y los problemas de rendimiento, y lo ayudará a buscar y diagnosticar las causas principales.

Application Insights puede supervisar aplicaciones y servicios web Java y ASP.NET y servicios WCF. Pueden estar hospedados localmente, en máquinas virtuales o como sitios web de Microsoft Azure. 

En el lado del cliente, Application Insights puede obtener datos de telemetría de páginas web y una amplia variedad de dispositivos, entre los que se incluyen iOS, Android y aplicaciones de la Tienda Windows.

>[!Note]
> Hemos puesto a su disposición una nueva experiencia que permite buscar las páginas que funcionan con lentitud en su aplicación web. Si no tiene acceso a ella, para habilitarla configure las opciones de vista previa con el [hoja Vista previa](app-insights-previews.md). Para obtener información acerca de esta nueva experiencia, lea [Búsqueda y corrección de cuellos de botella de rendimiento con la investigación interactiva del rendimiento](#Find-and-fix-performance-bottlenecks-with-an-interactive-Performance-investigation).

## <a name="setup"></a>Configuración de la supervisión de rendimiento
Si todavía no ha agregado Application Insights a un proyecto (es decir, si no dispone de ApplicationInsights.config), puede comenzar con uno de estos procedimientos:

* [Aplicaciones web ASP.NET](app-insights-asp-net.md)
  * [Agregar supervisión de excepciones](app-insights-asp-net-exceptions.md)
  * [Agregar supervisión de dependencias](app-insights-monitor-performance-live-website-now.md)
* [Aplicaciones web J2EE](app-insights-java-get-started.md)
  * [Agregar supervisión de dependencias](app-insights-java-agent.md)

## <a name="view"></a>Exploración de las métricas de rendimiento
En el [Portal de Azure](https://portal.azure.com), busque el recurso de Application Insights que configuró para la aplicación. La hoja de información general muestra los datos de rendimiento básicos:

Haga clic en cualquier gráfico para ver su contenido con mayor detalle y los resultados durante más tiempo. Por ejemplo, haga clic en el mosaico Solicitudes y seleccione un intervalo de tiempo:

![Haga clic en las distintas opciones para obtener más datos y seleccionar un intervalo de tiempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Haga clic en un gráfico para elegir las métricas mostradas o para agregar un nuevo gráfico y seleccionar sus métricas:

![Haga clic en un gráfico para elegir las métricas](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desactive todas las métricas** para ver todas las métricas disponibles. Las métricas se organizan en grupos. Cuando se selecciona un miembro de un grupo, solo aparecen los demás miembros de dicho grupo.

## <a name="metrics"></a>¿Qué significa todo esto? Informes y mosaicos de rendimiento
Puede obtener una gran variedad de métricas de rendimiento. Comencemos por las que aparecen de forma predeterminada en la hoja de la aplicación.

### <a name="requests"></a>Solicitudes
El número de solicitudes HTTP que se reciben en un período específico. Compare este dato con los resultados de otros informes para ver cómo se comporta la aplicación cuando la carga varía.

Las solicitudes HTTP incluyen todas las solicitudes GET o POST de páginas, datos e imágenes.

Haga clic en el mosaico para obtener las cuentas de URL específicas.

### <a name="average-response-time"></a>Tiempo de respuesta promedio
Mide el tiempo entre la entrada de una solicitud web y la devolución de la respuesta correspondiente.

Los puntos muestran una media móvil. Si se producen muchas solicitudes, es posible que algunas de ellas se desvíen de la media sin que en el gráfico se muestre ningún pico o descenso evidente.

Busque picos poco habituales. Por lo general, el tiempo de respuesta aumenta cuando aumentan las solicitudes. Si este aumento es desproporcionado, la aplicación puede alcanzar el límite de recursos (por ejemplo, de CPU o de un servicio que esté usando).

Haga clic en el icono para obtener los tiempos de direcciones URL concretas.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Solicitudes más lentas
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Muestra las solicitudes que pueden precisar un ajuste de rendimiento.

### <a name="failed-requests"></a>Error en las solicitudes
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Un recuento de las solicitudes que inician excepciones no detectadas.

Haga clic en el mosaico para ver los detalles de errores específicos y seleccione una solicitud individual para revisarla de forma más exhaustiva. 

Solo se conserva una muestra representativa de los errores para su inspección individual.

### <a name="other-metrics"></a>Otras métricas
Si desea ver las demás métricas que puede mostrar, haga clic en un gráfico y desactive todas las métricas para ver el conjunto completo de opciones disponibles. Haga clic en (i) para ver la definición de cada métrica.

![Cancele todas las métricas para ver el conjunto completo](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Si selecciona una métrica, se deshabilitan las demás, por lo que no pueden aparecer en el mismo gráfico.

## <a name="set-alerts"></a>Establecer alertas
Para recibir notificaciones por correo electrónico de los valores no habituales de cualquier métrica, agregue una alerta. Puede decidir si se debe enviar un mensaje de correo electrónico a los administradores de cuentas o a direcciones de correo electrónico específicas.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Establezca el recurso antes de las demás propiedades. No elija los recursos de la prueba web si desea establecer alertas en las métricas de rendimiento o de uso.

Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.

*No puedo ver el botón Agregar alerta* : ¿es una cuenta de grupo a la que tiene acceso de solo lectura? Consulte con el administrador de cuenta.

## <a name="diagnosis"></a>Diagnóstico de problemas
Para buscar y diagnosticar problemas de rendimiento, lea estas sugerencias:

* Configure las [pruebas web][availability] para recibir una alerta si el sitio web deja de funcionar o si no responde correctamente, o lo hace más lentamente de lo habitual. 
* Compare el recuento de Solicitudes con otras métricas para ver si la lentitud de respuesta o los errores se encuentran relacionados con la carga.
* [Inserte y busque instrucciones de seguimiento][diagnostic] en el código para facilitar la identificación de los problemas.
* Supervise el funcionamiento de la aplicación web junto con [Live Metrics Stream][livestream].
* Capture el estado de una aplicación .Net con [Snapshot Debugger][snapshot].

>[!Note]
> Estamos en proceso de transición de una investigación de rendimiento de Application Insights a una experiencia interactiva de pantalla completa. La siguiente documentación cubre la nueva experiencia en primer lugar y, a continuación, revisa la experiencia anterior, por si necesita tener acceso a ella, mientras sigue disponible durante toda la transición.

## <a name="find-and-fix-performance-bottlenecks-with-an-interactive-full-screen-performance-investigation"></a>Búsqueda y corrección de cuellos de botella de rendimiento con la investigación interactiva del rendimiento a pantalla completa

Puede usar la nueva función de investigación interactiva del rendimiento de Application Insights para revisar las operaciones de rendimiento lento de la aplicación web. Puede seleccionar una operación específica lenta rápidamente y usar [Profiler](app-insights-profiler.md) para encontrar la causa del funcionamiento lento en el código. Use la distribución de la nueva duración que se muestra para la operación seleccionada para evaluar rápidamente cómo perciben la experiencia los clientes. De hecho, para cada operación lenta, puede ver cuántas interacciones de usuario se vieron afectadas. En el siguiente ejemplo, hemos decidido echar un vistazo más de cerca a la experiencia de la operación GET Customers/Details. En la distribución de duración podemos ver que hay tres picos. El pico del extremo izquierdo es de, aproximadamente, 400 ms y representa una experiencia con una gran capacidad de respuesta. El pico intermedio es de, aproximadamente, 1,2 s y representa una experiencia mediocre. Finalmente, en 3,6 s tenemos un pequeño aumento que representa la experiencia de percentil 99, lo que podría hacer que nuestros clientes abandonaran insatisfechos. Esta experiencia es diez veces más lenta que la experiencia excelente de la misma operación. 

![Tres picos de duración de GET Customers/Details](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Para obtener una mejor idea de las experiencias de usuario para esta operación, podemos seleccionar un intervalo de tiempo mayor. A continuación, podemos reducir el tiempo a un intervalo concreto en que la operación fue especialmente lenta. En el siguiente ejemplo hemos cambiado el valor predeterminado de 24 horas por el de 7 días y hemos ampliado el periodo entre 9:47 y 12:47 del martes 12 y el miércoles 13. Tenga en cuenta que la distribución de la duración y el número de seguimientos de Profiler y de ejemplo se han actualizado a la derecha.

![Tres picos de duración para GET Customers/Details en un intervalo de 7 días en un periodo de tiempo](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Para limitar las experiencias lentas, a continuación, ampliamos las duraciones situadas en el percentil entre 95 y 99. Estas representan el 4 de % de las interacciones del usuario que eran especialmente lentas.

![Tres picos de duración para GET Customers/Details en un intervalo de 7 días en un periodo de tiempo](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Ahora podemos observar los ejemplos representativos haciendo clic en el botón Ejemplos o los seguimientos de Profiler haciendo clic en el botón Seguimientos de Profiler. En este ejemplo hay cuatro seguimientos que se han recopilado para obtener clientes y detalles en el periodo de tiempo y el intervalo de duración de interés.

A veces, el problema no estará en el código, sino en una dependencia que invoca el código. Puede cambiar a la pestaña Dependencias de la vista de evaluación de prioridades para investigar tales dependencias lentas. Tenga en cuenta que, de forma predeterminada, la vista de rendimiento muestra medias de tendencia, pero el dato relevante es el del percentil 95 (o 99, en caso de que esté supervisando un servicio muy consolidado). En el siguiente ejemplo, nos hemos centrado en la dependencia de blob de Azure lenta, donde se invocó PUT fabrikamaccount. Las buenas experiencias se agrupan alrededor de los 40 ms, mientras que las llamadas lentas a la misma dependencia son tres veces más lentas, aproximadamente 120 ms. No se necesitan muchas llamadas para que la operación respectiva se ralentice considerablemente. Puede profundizar en los ejemplos representativos y seguimientos de Profiler, exactamente igual que con la pestaña operaciones.

![Tres picos de duración para GET Customers/Details en un intervalo de 7 días en un periodo de tiempo](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

Otra característica muy eficaz que es una novedad de la investigación de rendimiento interactivo de pantalla completa es la integración con Insights. Application Insights puede detectar y exponer regresiones de capacidad de respuesta de información, así como ayudarle a identificar propiedades comunes en el conjunto de muestra en que decidió centrarse. La mejor manera de buscar en todas las informaciones disponibles es cambiar a un intervalo de tiempo de 30 días y, a continuación, seleccionar Información general para ver información de todas las operaciones del mes pasado.

![Tres picos de duración para GET Customers/Details en un intervalo de 7 días en un periodo de tiempo](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)

Application Insights en la nueva vista de evaluación de prioridades de rendimiento puede ayudarle a encontrar las agujas del pajar que provocan una mala experiencia para los usuarios de la aplicación web.

## <a name="deprecated-find-and-fix-performance-bottlenecks-with-a-narrow-bladed-legacy-performance-investigation"></a>Obsoleto: Búsqueda y corrección de cuellos de botella de rendimiento con la investigación del rendimiento de hoja restringida anterior

Puede usar la anterior función de investigación del rendimiento de hoja de Application Insights para buscar las áreas la aplicación web que ralentizan el rendimiento general. Puede buscar las páginas específicas que provocan la ralentización y usar [Profiler](app-insights-profiler.md) para detectar la causa raíz de estos problemas en el código. 

### <a name="create-a-list-of-slow-performing-pages"></a>Creación de una lista de páginas con un rendimiento lento 

El primer paso para encontrar problemas de rendimiento es obtener una lista de las páginas que responden con lentitud. La siguiente captura de pantalla muestra cómo utilizar la hoja Rendimiento para obtener una lista de posibles páginas que hay que investigar con mayor profundidad. Desde esta página se puede ver rápidamente que se ha producido un aumento puntual del tiempo de respuesta de la aplicación aproximadamente a las 6:00 p.m. y de nuevo aproximadamente a las 10:00 p.m. También puede ver que la operación GET de cliente o detalles tenía algunas operaciones de larga ejecución con un tiempo de respuesta medio de 507,05 milisegundos. 

![Rendimiento interactivo de Application Insights](./media/app-insights-web-monitor-performance/performance1.png)

### <a name="drill-down-on-specific-pages"></a>Exploración en profundidad de páginas concretas

Una vez que tenga una instantánea del rendimiento de su aplicación, puede obtener más información sobre las operaciones concretas cuyo rendimiento es bajo. Haga clic en cualquier operación de la lista para ver los detalles que se muestran a continuación. En el gráfico puede ver si el rendimiento se basaba en una dependencia. También puede ver el número de usuarios que han tenido los distintos tiempos de respuesta. 

![Hoja de operaciones de Application Insights](./media/app-insights-web-monitor-performance/performance5.png)

### <a name="drill-down-on-a-specific-time-period"></a>Exploración en profundidad de un período concreto

Después de haber identificado el punto específico que se va a investigar, explore en mayor profundidad para examinar las operaciones específicas que pueden haber provocado que la disminución de rendimiento. Al hacer clic en un punto específico, se obtienen los detalles de la página, como se muestra a continuación. En el ejemplo siguiente se pueden ver las operaciones enumeradas durante un período dado junto con los códigos de respuesta del servidor y la duración de la operación. También tiene la dirección URL para abrir un elemento de trabajo de TFS si necesita enviar esta información a su equipo de desarrollo.

![Porción de tiempo de Application Insights](./media/app-insights-web-monitor-performance/performance2.png)

### <a name="drill-down-on-a-specific-operation"></a>Exploración en profundidad de una operación concreta

Después de haber identificado el punto específico que se va a investigar, explore en mayor profundidad para examinar las operaciones específicas que pueden haber provocado que la disminución de rendimiento. Haga clic en cualquier operación de la lista para ver sus detalles como se muestra a continuación. En este ejemplo puede ver que la operación no se pudo realizar y que Application Insights ha proporcionado los detalles de la excepción que inició la aplicación. Una vez más, desde esta hoja se puede crear fácilmente un elemento de trabajo de TFS.

![Hoja de operaciones de Application Insights](./media/app-insights-web-monitor-performance/performance3.png)


## <a name="next"></a>Pasos siguientes
[Pruebas web][availability]: reciba solicitudes web en su aplicación de forma periódica desde distintos lugares del mundo.

[Capture y busque seguimientos de diagnóstico][diagnostic]: inserte llamadas de seguimiento y cribe los resultados para identificar los problemas.

[Seguimiento del uso][usage]: averigüe cuántas personas usan la aplicación.

[Solución de problemas][qna]: con preguntas y respuestas



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



