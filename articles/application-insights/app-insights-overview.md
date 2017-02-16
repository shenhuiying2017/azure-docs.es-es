---
title: Application Performance Management con Azure Application Insights | Microsoft Docs
description: "Realice el seguimiento del rendimiento y el uso de su aplicación web activa.  Detecte, evalúe y diagnostique problemas."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 3e9476f8edc4186af026495bc575b8203c35c619
ms.openlocfilehash: b01de7724ec116e5e27369f7c0f852f82ab0348b


---
# <a name="application-performance-management-with-application-insights"></a>Application Performance Management con Application Insights
Application Insights es un servicio de Application Performance Management (APM) extensible para desarrolladores web. Úselo para supervisar la aplicación web en directo. Se detectarán automáticamente las anomalías de rendimiento. Incluye herramientas de análisis eficaces que le ayudan a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación.  Está diseñado para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso. Funciona con diversas aplicaciones y en una amplia variedad de plataformas, como .NET, Node.js o J2EE, tanto hospedadas localmente como en la nube. También se integra con el proceso de devOps y tiene puntos de conexión para una amplia variedad de otras herramientas.

![Cree un gráfico de estadísticas de la actividad del usuario o explore en profundidad eventos específicos.](./media/app-insights-overview/00-sample.png)

[Eche un vistazo a la animación de introducción](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-it-work"></a>¿Cómo funciona?
Hay que instalar un pequeño paquete de instrumentación en la aplicación y configurar un recurso de Application Insights en el portal de Microsoft Azure. El paquete de instrumentación supervisa la aplicación y envía los datos de telemetría al portal. El portal le muestra gráficos estadísticos y ofrece eficaces herramientas de búsqueda para ayudarle a diagnosticar los problemas.

![La instrumentación de Application Insights de la aplicación envía datos de telemetría al recurso de Application Insights en el Portal de Azure.](./media/app-insights-overview/01-scheme.png)

Application Insights tiene varios [módulos de instrumentación estándar](app-insights-configuration-with-applicationinsights-config.md) que recopilan diferentes tipos de datos de telemetría como los tiempo de respuesta a las solicitudes, las excepciones y las llamadas de dependencia. También se puede [escribir código para enviar telemetría personalizada](app-insights-api-custom-events-metrics.md) al portal.

### <a name="whats-the-overhead"></a>¿Cuál es la sobrecarga?
El impacto sobre el rendimiento de la aplicación es muy pequeño. Las llamadas de seguimiento no suponen ningún tipo de bloqueo y se agrupan por lotes y se envían en un subproceso aparte.

## <a name="what-does-it-do"></a>¿Para qué sirve?
Application Insights está dirigido al equipo de desarrollo y sirve ayudarle a conocer el rendimiento de una aplicación y cómo se utiliza. Ofrece:

Tipos de telemetría:

* Velocidad de solicitudes HTTP, tiempos de respuesta y niveles de éxito.
* Tarifas de llamadas (HTTP y SQL) de dependencia, tiempos de respuesta, niveles de éxito.
* Seguimiento de excepciones de servidor y cliente.
* Seguimiento de registro de diagnóstico.
* Recuentos de vistas de página, recuentos de usuarios y sesiones, tiempos de carga del explorador y excepciones.
* Tarifas de llamadas de AJAX, tiempos de respuesta y niveles de éxito.
* Contadores de rendimiento de servidor.
* Telemetría personalizada de cliente y servidor.
* Segmentación por ubicación de cliente, versión de explorador, versión de sistema operativo, instancia de servidor, dimensiones personalizadas, etc.
* Pruebas de disponibilidad

Herramientas de análisis y diagnóstico:

* Alertas inteligentes y manuales de los índices de error y otras métricas, así como de la disponibilidad.
* Gráficos de las métricas agregadas con el paso del tiempo.
* Búsqueda de diagnósticos en instancias de las solicitudes, excepciones, eventos personalizados, seguimientos de registros, vistas de página, dependencias y llamadas AJAX.
* Análisis: un lenguaje de consulta eficaz a través de los datos de telemetría
* Paneles: componga los gráficos que necesita para supervisar todos los componentes de la aplicación.

## <a name="how-do-i-use-it"></a>¿Cómo se usa?
### <a name="monitor"></a>Supervisión
Instale Application Insights en su aplicación web, configure las pruebas web de disponibilidad y:

* Configure un panel para que su equipo no pierda de vista la carga, la capacidad de respuesta y el rendimiento de las dependencias, cargas de páginas y llamadas AJAX.
* Detecte cuáles son las solicitudes más lentas y con mayor número de errores.
* Vea Live Stream cuando implemente una versión nueva, con el fin de conocer inmediatamente la existencia de cualquier degradación.

### <a name="diagnose"></a>Diagnóstico
Cuando reciba una alerta o detecte un problema:

* Correlacione los errores con las excepciones, las llamadas de dependencia y los seguimientos.
* Examine los registros de seguimiento y los volcados de pila.

### <a name="assess"></a>Evaluación
Mida la eficacia de cada característica nueva que implemente.

* Planee la medición de la forma en que los clientes utilizan las nuevas características empresariales y de experiencia de usuario.
* Escriba datos de telemetría personalizados en el código de registro del uso.
* Base cada ciclo de desarrollo en pruebas contundentes de la telemetría.

## <a name="get-started"></a>Primeros pasos
Application Insights es uno de los muchos servicios hospedados en Microsoft Azure y los datos de telemetría se envían ahí para analizarlos y mostrarlos. Por tanto, antes de nada, se necesita una suscripción a [Microsoft Azure](http://azure.com). El registro es gratuito y, si elige el [plan de precios](https://azure.microsoft.com/pricing/details/application-insights/) básico de Application Insights, no habrá cargo alguno hasta que la aplicación tenga un uso considerable. Si la organización ya tiene una suscripción, puede agregarle su cuenta de Microsoft.

Hay varias formas de empezar. Comience con la que más se ajuste a sus necesidades. Puede agregar los demás posteriormente.

* **En tiempo de ejecución: instrumente su aplicación web en el servidor.** Evita toda actualización del código. Se necesita acceso de administrador al servidor.
  * [**IIS local o en una máquina virtual**](app-insights-monitor-performance-live-website-now.md)
  * [**Máquina virtual o aplicación web de Azure**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **En tiempo de desarrollo: agregue Application Insights al código.** Le permite escribir datos de telemetría personalizados e instrumentalizar las aplicaciones de back-end y de escritorio.
  * [Visual Studio](app-insights-asp-net.md) 2013, actualización 2 o superior.
  * Java en [Eclipse](app-insights-java-eclipse.md) u [otras herramientas](app-insights-java-get-started.md)
  * [Node.js](app-insights-nodejs.md)
  * [Otras plataformas](app-insights-platforms.md)
* **[Instrumente sus páginas web](app-insights-javascript.md)** para la vista de la página, AJAX y otros datos de telemetría del lado cliente.
* **[Pruebas de disponibilidad](app-insights-monitor-web-app-availability.md)** : haga ping a su sitio web de manera regular desde nuestros servidores.

> [!NOTE]
> En este punto, quizás desea seguir adelante y experimentar. Sin embargo, si desea ver qué puede hacer Application Insights, siga leyendo...
>
>

## <a name="explore-metrics"></a>Explorar métricas
Ejecute la aplicación (ya sea en modo de depuración en el equipo de desarrollo o implementándola en un servidor) y úsela durante un tiempo. Luego, inicie sesión en el [Portal de Azure](https://portal.azure.com).

Vaya a la hoja de información general de Application Insights de la aplicación:

![Hoja de información general](./media/app-insights-overview/01-find.png)

La información general le permite saber de inmediato cuál es el rendimiento de la aplicación. Puede comparar la carga (en términos de velocidad de las solicitudes) con el tiempo que ha tardado la aplicación en responder a las solicitudes. Así, si existe un aumento desproporcionado del tiempo de respuesta cuando la carga aumenta, puede asignar más recursos a la aplicación. Si se producen más errores en las respuestas después de haber implementado una nueva compilación, probablemente convenga revertir esa implementación.

#### <a name="get-more-detail"></a>Obtener más detalles
Haga clic en cualquiera de los gráficos para ver un conjunto de métricas más detallado. Por ejemplo, el gráfico de tiempo de respuesta del servidor lleva a gráficos que reflejan la velocidad de las solicitudes, los tiempos de respuesta y los tiempos de respuesta de dependencias (es decir, los servicios a los que llama la aplicación).  

![Hoja de servidores](./media/app-insights-overview/04.png)

El gráfico de dependencias es útil porque ayuda a ver si las bases de datos y la API de REST que la aplicación usa responden correctamente o provocan retrasos.

#### <a name="customize-a-chart"></a>Personalizar un gráfico
Pruebe a modificar uno de estos gráficos. Por ejemplo, si la aplicación web se ejecuta en una colección de instancias de servidor, puede comparar los tiempos de respuesta de distintas instancias de servidor:

![editar el gráfico](./media/app-insights-overview/05.png)

1. Mantenga el mouse sobre el gráfico y haga clic en Editar.
2. Elija un modelo. En un gráfico se pueden mostrar varias métricas, pero solo en determinadas combinaciones: puede que tenga que anular la selección de una métrica para poder seleccionar la que quiera.
3. Use la opción de agrupación para segmentar una métrica a partir de una propiedad. En este ejemplo, mostramos líneas independientes para distintos tiempos de respuesta.

    Conviene recordar que hay que seleccionar una propiedad válida para la métrica o, de lo contrario, el gráfico no mostrará ningún dato.
4. Seleccione un tipo de gráfico. Los gráficos de áreas y de barras muestran una pantalla apilada apropiada cuando el tipo de agregación es "Sum".

[Más información sobre la exploración de métricas](app-insights-metrics-explorer.md).

## <a name="search-instance-data"></a>Buscar datos de instancia
Para investigar un problema, es útil inspeccionar instancias de evento específicas.

Haga clic en un gráfico de métricas para buscar en los datos de instancia mediante filtros e intervalos de tiempo adecuados. Por ejemplo, haga clic en los distintos recuentos de solicitud de servidor para ver informes de solicitud individuales.

También puede ir directamente a los datos de instancia desde la función de búsqueda de la página de información general:

![Buscar instancias](./media/app-insights-overview/06.png)

Use filtros para centrarse en determinados tipos de evento y en valores de propiedad de su elección:

![Filtrar por propiedades](./media/app-insights-overview/07.png)

Haga clic en "..." para ver una lista completa de propiedades o abrir otros eventos relacionados con la misma solicitud. En este ejemplo, la solicitud con error tiene un informe de excepciones asociado:

![Elementos relacionados y detalles de propiedades](./media/app-insights-overview/08.png)

Abra un evento (en este ejemplo, la excepción relacionada). Puede crear un elemento de trabajo (si usa Visual Studio Team Services para llevar un seguimiento de las tareas).

![Crear un elemento de trabajo](./media/app-insights-overview/09.png)

## <a name="analytics"></a>Análisis
[Analytics](app-insights-analytics.md) es una característica de búsqueda y análisis más eficaz en la que se pueden escribir consultas de tipo SQL sobre los datos de telemetría, ya sea para buscar problemas concretos o para compilar información estadística.

![Análisis](./media/app-insights-overview/10.png)

Abra la ventana del tutorial para ver y ejecutar ejemplos de consultas sobre sus datos, o para leer el [tutorial](app-insights-analytics-tour.md). IntelliSense le pregunta qué consultas puede usar y hay una [referencia del lenguaje completa](app-insights-analytics-reference.md).

Las consultas suelen comenzar por el nombre de una secuencia de datos de telemetría, como solicitudes, excepciones o dependencias. Abra la barra de esquema a la izquierda para ver una lista de las secuencias de telemetría disponibles. La consulta es una canalización de [operaciones de consulta](app-insights-analytics-reference.md#queries-and-operators) como `where` (un filtro booleano) o `project` (que calcula nuevas propiedades). `summarize` agrega instancias, agrupándolas por las funciones que se hayan definido y, luego, aplicando funciones de agregación a los datos agrupados.

Los resultados se pueden [representar en tablas o en distintos tipos de gráfico](app-insights-analytics-tour.md#charting-the-results).

## <a name="custom-telemetry"></a>Telemetría personalizada
La telemetría integrada que se obtiene solo con instalar Application Insights permite analizar los recuentos, tasas de éxito y tiempos de respuesta tanto de las solicitudes web realizadas en su aplicación como de las dependencias (esto es, las llamadas desde la aplicación a API de REST y SQL). También dispondrá de seguimientos de las excepciones y contadores de rendimiento del sistema (con el Monitor de estado en el servidor). Si agrega el fragmento de código de cliente a las páginas web, obtendrá los tiempos de carga y recuentos de visualizaciones de página, excepciones de cliente y tasas de éxito y de respuesta de llamadas AJAX.

Analizar todos estos datos de telemetría puede aportar cuantiosa información sobre el uso y el rendimiento de la aplicación. Pero a veces no es suficiente. Puede que quiera supervisar la longitud de una cola para ajustar el rendimiento; hacer un recuento de las ventas y segmentarlas por ubicación, o bien, en el lado cliente, averiguar con qué frecuencia se hace clic en un botón determinado para optimizar la experiencia de usuario.

La [API de Application Insights](app-insights-api-custom-events-metrics.md) proporciona llamadas `TrackEvent(name)` y `TrackMetric(name, value)` para que pueda enviar sus propias métricas y eventos personalizados. Hay llamadas equivalentes para el lado cliente.

Por ejemplo, si la página web es una aplicación de juego de una sola página, podría insertar líneas en los lugares adecuados para iniciar sesión cuando el usuario gane o pierda una partida:

    appInsights.trackEvent("WinGame");
    ...
    appInsights.trackEvent("LoseGame");

Luego, podemos trazar un gráfico de los recuentos de eventos personalizados, segmentándolos por nombre de evento:

![Análisis](./media/app-insights-overview/11.png)

### <a name="log-traces"></a>Seguimientos de registro
De cara a realizar diagnósticos, hay un evento personalizado `TrackTrace(message)` que se puede usar para el seguimiento de las ejecuciones. En las características de búsqueda y Analytics, puede buscar en el contenido del mensaje, que es más largo que un nombre de evento.

Si ya usa una plataforma de registro como Log4Net, NLog, Log4J o System.Diagnostic.Trace, Application Insights puede capturar esas llamadas de seguimiento y, por tanto, aparecerán junto a otros datos de telemetría. Las herramientas de Visual Studio agregan automáticamente el módulo SDK apropiado.

## <a name="profiling-your-live-site"></a>Generación de perfiles del sitio activo

¿Quiere saber en qué se invierte el tiempo? El generador de perfiles de Application Insights realizará un seguimiento de las llamadas HTTP a los sitios activos y mostrará qué funciones del código tardan más tiempo en ejecutarse. Esta herramienta está actualmente en fase de versión preliminar limitada: puede [registrarse aquí para probarlo](https://aka.ms/AIProfilerPreview).

## <a name="dashboards"></a>Paneles
Muchas aplicaciones constan de varios componentes, como un servicio web y uno o varios procesadores back-end. Cada componente se supervisará por medio de un recurso de Application Insights separado. Si el sistema se ejecuta en Azure, puede que también use (y supervise) servicios como centros de eventos y aprendizaje automático.

Para supervisar todo el sistema puede seleccionar los gráficos más interesantes de diferentes aplicaciones y anclarlos a un [panel](app-insights-dashboards.md)de Azure, lo que le permitirá estar al tanto de todo el sistema continuamente.

![Paneles](./media/app-insights-overview/12.png)

De hecho, puede crear varios paneles; por ejemplo, un panel de centro de reunión para supervisar el estado general del sistema; un panel de diseño centrado en el uso de diferentes características; un panel independiente para los componentes en pruebas, y así sucesivamente.  

Al igual que los recursos, los paneles se pueden compartir entre los miembros del equipo.

## <a name="development-in-visual-studio"></a>Desarrollo en Visual Studio
Si usa Visual Studio para desarrollar la aplicación, verá que hay varias herramientas de Application Insights integradas.

### <a name="diagnostic-search"></a>Búsqueda de diagnóstico
La ventana de búsqueda muestra los eventos que se han registrado. (Si inició la sesión en Azure al configurar Application Insights, podrá buscar los mismos eventos en el portal.)

![Haga clic con el botón derecho en el proyecto y seleccione Application Insights, Búsqueda.](./media/app-insights-overview/34.png)

La búsqueda de texto sin formato funciona en todos los campos de los eventos. Por ejemplo, buscar parte de la dirección URL de una página; o el valor de una propiedad, como la ciudad del cliente; o palabras específicas en un registro de seguimiento.

Haga clic en cualquier evento para ver sus propiedades con todo detalle.

También puede abrir la pestaña de elementos relacionados para ayudar a diagnosticar las solicitudes con errores o las excepciones.

![](./media/app-insights-overview/41.png)

### <a name="diagnostics-hub"></a>Concentrador de diagnósticos
El concentrador de diagnósticos (en Visual Studio 2015 o posterior) muestra los datos de telemetría del servidor de Application Insights cuando se generan. Esto funciona incluso si solo ha optado por instalar el SDK, sin conectarlo a un recurso del Portal de Azure.

![Abra la ventana Herramientas de diagnóstico e inspeccione los eventos de Application Insights.](./media/app-insights-overview/31.png)

### <a name="exceptions"></a>Excepciones
Si ha [configurado la supervisión de excepciones](app-insights-asp-net-exceptions.md), los informes de excepciones se mostrarán en la ventana de búsqueda.

Haga clic en una excepción para obtener un seguimiento de la pila. Si el código de la aplicación es abierto en Visual Studio, puede hacer clic para recorrer el seguimiento de la pila hasta dar con la línea correspondiente del código.

![Seguimiento de la pila de excepción](./media/app-insights-overview/17.png)

Además, en la línea de Code Lens sobre cada método, verá un recuento de las excepciones registradas por Application Insights en las últimas 24 horas.

![Seguimiento de la pila de excepción](./media/app-insights-overview/21.png)

### <a name="local-monitoring"></a>Supervisión local
(Desde Visual Studio 2015 Update 2) Si no ha configurado el SDK para enviar datos de telemetría al portal de Application Insights (para que no haya ninguna clave de instrumentación en ApplicationInsights.config), la ventana diagnóstico mostrará los datos de telemetría de la sesión de depuración más reciente.

Esto es conveniente si ya ha publicado una versión anterior de la aplicación. No quiere que los datos de telemetría de las sesiones de depuración se mezclen con los datos de telemetría en el portal de Application Insights de la aplicación publicada.

También es útil si tiene [datos de telemetría personalizados](app-insights-api-custom-events-metrics.md) que desea depurar antes de enviarlos al portal.

* *En primer lugar, configuré totalmente Application Insights para enviar los datos de telemetría al portal. Pero ahora me gustaría ver los datos de telemetría solo en Visual Studio.*

  * En la configuración de la ventana de búsqueda, hay una opción para buscar diagnósticos locales, incluso si la aplicación envía datos de telemetría al portal.
  * Para detener el envío de datos de telemetría al portal, convierta en comentario la línea `<instrumentationkey>...` de ApplicationInsights.config. Cuando esté listo para enviar de nuevo datos de telemetría al portal, quite los comentarios.

### <a name="trends"></a>Tendencias
Tendencias es una herramienta de Visual Studio para visualizar cómo se comporta la aplicación con el paso del tiempo.

Elija **Explorar tendencias de telemetría** con el botón de la barra de herramientas de Application Insights o en la ventana Búsqueda de Application Insights. Seleccione una de las cinco consultas comunes para empezar. Puede analizar diferentes conjuntos de datos en función de los tipos de telemetría, los intervalos de tiempo y otras propiedades.

Para detectar anomalías en los datos, elija una de las opciones de anomalía en la lista desplegable "Tipo de vista". Con las opciones de filtrado de la parte inferior de la ventana, resulta más sencillo centrarse en subconjuntos específicos de la telemetría.

![Tendencias](./media/app-insights-overview/51.png)

## <a name="releasing-a-new-build"></a>Publicar una nueva compilación
### <a name="live-metrics-stream"></a>Secuencia de métricas en directo
Stream de métricas en vivo muestra las métricas de la aplicación de este mismo momento, con una latencia casi de tiempo real de 1 segundo. Esto resulta muy útil si está publicando una compilación nueva y desea asegurarse de que todo esté funcionando como se esperaba o si quiere investigar un incidente en tiempo real.

![En la hoja de información general, haga clic en Stream en vivo.](./media/app-insights-overview/live-stream.png)

A diferencia del Explorador de métricas, la Secuencia de métricas en directo muestra un conjunto fijo de métricas. Los datos se conservan solo mientras se encuentran en el gráfico, y luego se descartan.

### <a name="annotations"></a>Anotaciones
[anotaciones de la versión](app-insights-annotations.md) de los gráficos de métricas muestran dónde se ha implementado una nueva compilación. Permiten ver fácilmente si los cambios tuvieron algún efecto en el rendimiento de la aplicación. Los puede crear automáticamente el [sistema de compilación de Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs) y también mediante las implementaciones web de Visual Studio. Y, también puede [crearlos desde PowerShell](#create-annotations-from-powershell).

![Ejemplo de anotaciones con correlación visible con el tiempo de respuesta del servidor](./media/app-insights-overview/00.png)

Las anotaciones de versión son una característica del servicio de versiones y compilaciones basado en la nube de Visual Studio Team Services.



## <a name="alerts"></a>Alertas
Si algo va mal en la aplicación, querrá saberlo inmediatamente.

Application Insights ofrece tres tipos de alerta, que se entregan por correo electrónico.

### <a name="proactive-diagnostics"></a>Diagnóstico proactivo
El [diagnóstico proactivo](app-insights-proactive-failure-diagnostics.md) se configura automáticamente, no hay que hacer nada. Siempre que el sitio tenga suficiente tráfico, recibirá un correo electrónico si hay un aumento poco habitual en las solicitudes con error para el momento del día o la tasa de solicitudes. La alerta contiene información de diagnóstico.

Esta es una alerta de ejemplo.

![El ejemplo de alerta inteligente muestra el análisis del clúster sobre el error](./media/app-insights-overview/proactive-alert.png)

Un segundo tipo de detección proactiva detecta las correlaciones entre errores y factores como la ubicación, el sistema operativo de cliente o el tipo de explorador.

### <a name="metric-alerts"></a>Alertas de métricas
Puede configurar [alertas de métricas](app-insights-alerts.md) que le avisen en el momento en que cualquier métrica supera un valor de umbral durante un período determinado (por ejemplo, recuentos de error, memoria o vistas de página).

![En el Explorador de métricas, elija Reglas de alertas, Agregar alerta](./media/app-insights-overview/appinsights-413setMetricAlert.png)

### <a name="availability"></a>Disponibilidad
[pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) envían solicitudes a su sitio desde nuestros servidores en varias ubicaciones de todo el mundo. Estas pruebas informan de cuándo el sitio no está disponible en Internet o responde lentamente.

![Ejemplo de prueba web](./media/app-insights-overview/appinsights-10webtestresult.png)

## <a name="export-and-api"></a>Exportación y API
Hay varias formas de obtener datos de telemetría del portal de Application Insights:

* La [API de REST de Data Access](https://dev.applicationinsights.io/) se puede usar para buscar y extraer datos, incluyendo las consultas de Analytics activas. 
* Exporte las [consultas de Analytics a los paneles de Power BI](app-insights-export-power-bi.md) y vea los resultados en las visualizaciones de Power BI, que se pueden actualizar automáticamente.
* [Exportación continua](app-insights-export-telemetry.md) es ideal si desea conservar grandes cantidades de datos de telemetría durante más tiempo del período de retención estándar establecido.
* Las tablas de [métricas](app-insights-metrics-explorer.md#export-to-excel), los resultados de las búsquedas y los resultados de [Analytics](app-insights-analytics.md) se pueden exportar a una hoja de cálculo de Excel.

![Visualización de datos en Power BI](./media/app-insights-overview/210.png)

## <a name="data-management"></a>Administración de datos
Existen límites en el uso de Application Insights, que dependen en cierta medida del esquema de precios que haya elegido. Estos son los límites más importantes:

* Tasa de telemetría por minuto
* Número de puntos de datos por mes
* Período de retención de los datos

[muestreo](app-insights-sampling.md) es un mecanismo para reducir costos y evitar la limitación. Descarta un porcentaje de los datos de telemetría y conserva una muestra representativa. Los elementos asociados (como las excepciones y las solicitudes que los han provocado) se conservan o descartan juntos. En el caso de las aplicaciones ASP.NET, el muestreo es automático y se realiza en la aplicación; de lo contrario, puede establecer que se realice en la ingesta en el portal.

## <a name="next-steps"></a>Pasos siguientes
Comience en el tiempo de ejecución con:

* [Servidor IIS](app-insights-monitor-performance-live-website-now.md)
* [Servidor de J2EE](app-insights-java-live.md)

Comience en el tiempo de desarrollo con:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Soporte y comentarios
* Preguntas y problemas:
  * [Solución de problemas][qna]
  * [Foro de MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
  * [Obtener soporte técnico Developer](app-insights-get-dev-support.md)
* Sus sugerencias:
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Blog:
  * [Blog de Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Vídeos
[![Introducción animada](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/218/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/231/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]
>
> [Animación de introducción](https://www.youtube.com/watch?v=fX2NtGrh-Y0)
>
>

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md



<!--HONumber=Jan17_HO4-->


