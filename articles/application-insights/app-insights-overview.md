---
title: Tutorial de Application Insights | Microsoft Docs
description: Realice el seguimiento del uso y el rendimiento de su aplicación web activa. Detecte, evalúe y diagnostique problemas. Supervise y mejore continuamente el éxito con los usuarios.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: awills

---
# Application Insights: introducción
Application Insights de Visual Studio es un servicio de análisis extensible que supervisa su aplicación web activa. Con él podrá detectar y diagnosticar problemas de rendimiento y comprender qué hacen los usuarios realmente con su aplicación. Está diseñado para desarrolladores, para ayudar a mejorar continuamente el rendimiento y la facilidad de uso de la aplicación. Funciona con diversas aplicaciones y en una amplia variedad de plataformas, como .NET, Node.js o J2EE, tanto hospedadas localmente como en la nube.

![Cree un gráfico de estadísticas de la actividad del usuario o explore en profundidad eventos específicos.](./media/app-insights-overview/00-sample.png)

[Eche un vistazo a la animación de introducción](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## ¿Cómo funciona?
Hay que instalar un pequeño paquete de instrumentación en la aplicación y configurar un recurso de Application Insights en el portal de Microsoft Azure. El paquete de instrumentación supervisa la aplicación y envía los datos de telemetría al portal. El portal le muestra gráficos estadísticos y ofrece eficaces herramientas de búsqueda para ayudarle a diagnosticar los problemas.

![La instrumentación de Application Insights de la aplicación envía datos de telemetría al recurso de Application Insights en el Portal de Azure.](./media/app-insights-overview/01-scheme.png)

Application Insights tiene varios [módulos de instrumentación estándar](app-insights-configuration-with-applicationinsights-config.md) que recopilan diferentes tipos de datos de telemetría como los tiempo de respuesta a las solicitudes, las excepciones y las llamadas de dependencia. También se puede [escribir código para enviar telemetría personalizada](app-insights-api-custom-events-metrics.md) al portal.

### ¿Cuál es la sobrecarga?
El impacto sobre el rendimiento de la aplicación es muy pequeño. Las llamadas de seguimiento no suponen ningún tipo de bloqueo y se agrupan por lotes y se envían en un subproceso aparte.

## ¿Para qué sirve?
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

## Primeros pasos
Application Insights es uno de los muchos servicios hospedados en Microsoft Azure y los datos de telemetría se envían ahí para analizarlos y mostrarlos. Por tanto, antes de nada, se necesita una suscripción a [Microsoft Azure](http://azure.com). Suscribirse es gratis y puede elegir el [plan de tarifa](https://azure.microsoft.com/pricing/details/application-insights/) gratuito de Application Insights. Si la organización ya tiene una suscripción, puede agregarle su cuenta de Microsoft.

Hay varias formas de empezar. Comience con la que más se ajuste a sus necesidades. Puede agregar los demás posteriormente.

* **En tiempo de ejecución: instrumente su aplicación web en el servidor.** Evita toda actualización del código. Se necesita acceso de administrador al servidor.
  * [**IIS local o en una máquina virtual**](app-insights-monitor-performance-live-website-now.md)
  * [**Aplicación web o máquina virtual de Azure**](app-insights-monitor-performance-live-website-now.md#if-your-app-runs-as-an-azure-web-app)
  * [**J2EE**](app-insights-java-live.md)
* **En tiempo de desarrollo: agregue Application Insights al código.** Le permite escribir datos de telemetría personalizados e instrumentalizar las aplicaciones de back-end y de escritorio.
  * [Visual Studio](app-insights-asp-net.md) 2013, actualización 2 o superior.
  * Java en [Eclipse](app-insights-java-eclipse.md) u [otras herramientas](app-insights-java-get-started.md)
  * [Node.js](app-insights-nodejs.md)
  * [Otras plataformas](app-insights-platforms.md)
* **[Instrumente sus páginas web](app-insights-javascript.md)** para la vista de página, AJAX y otros datos de telemetría del lado cliente.
* **[Pruebas de disponibilidad](app-insights-monitor-web-app-availability.md)**: haga ping a su sitio web de manera regular desde nuestros servidores.

> [!NOTE]
> En este punto, quizás desea seguir adelante y experimentar. Sin embargo, si desea ver qué puede hacer Application Insights, siga leyendo...
> 
> 

## Explorar métricas
Ejecute la aplicación (ya sea en modo de depuración en el equipo de desarrollo o implementándola en un servidor) y úsela durante un tiempo. Luego, inicie sesión en el [Portal de Azure](https://portal.azure.com).

Vaya a la hoja de información general de Application Insights de la aplicación:

![Hoja de información general](./media/app-insights-overview/01-find.png)

La información general le permite saber de inmediato cuál es el rendimiento de la aplicación. Puede comparar la carga (en términos de velocidad de las solicitudes) con el tiempo que ha tardado la aplicación en responder a las solicitudes. Así, si existe un aumento desproporcionado del tiempo de respuesta cuando la carga aumenta, puede asignar más recursos a la aplicación. Si se producen más errores en las respuestas después de haber implementado una nueva compilación, probablemente convenga revertir esa implementación.

#### Obtener más detalles
Haga clic en cualquiera de los gráficos para ver un conjunto de métricas más detallado. Por ejemplo, el gráfico de tiempo de respuesta del servidor lleva a gráficos que reflejan la velocidad de las solicitudes, los tiempos de respuesta y los tiempos de respuesta de dependencias (es decir, los servicios a los que llama la aplicación).

![Hoja de servidores](./media/app-insights-overview/04.png)

El gráfico de dependencias es útil porque ayuda a ver si las bases de datos y la API de REST que la aplicación usa responden correctamente o provocan retrasos.

#### Personalizar un gráfico
Pruebe a modificar uno de estos gráficos. Por ejemplo, si la aplicación web se ejecuta en una colección de instancias de servidor, puede comparar los tiempos de respuesta de distintas instancias de servidor:

![editar el gráfico](./media/app-insights-overview/05.png)

1. Mantenga el mouse sobre el gráfico y haga clic en Editar.
2. Elija un modelo. En un gráfico se pueden mostrar varias métricas, pero solo en determinadas combinaciones: puede que tenga que anular la selección de una métrica para poder seleccionar la que quiera.
3. Use la opción de agrupación para segmentar una métrica a partir de una propiedad. En este ejemplo, mostramos líneas independientes para distintos tiempos de respuesta.
   
    Conviene recordar que hay que seleccionar una propiedad válida para la métrica o, de lo contrario, el gráfico no mostrará ningún dato.
4. Seleccione un tipo de gráfico. Los gráficos de áreas y de barras muestran una pantalla apilada apropiada cuando el tipo de agregación es "Sum".

[Más información sobre la exploración de métricas](app-insights-metrics-explorer.md).

## Buscar datos de instancia
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

## Análisis
[Analytics](app-insights-analytics.md) es una característica de búsqueda y análisis más eficaz en la que se pueden escribir consultas de tipo SQL sobre los datos de telemetría, ya sea para buscar problemas concretos o para compilar información estadística.

![Análisis](./media/app-insights-overview/10.png)

Abra la ventana del tutorial para ver y ejecutar ejemplos de consultas sobre sus datos, o para leer el [tutorial](app-insights-analytics-tour.md). IntelliSense le pregunta qué consultas puede usar y hay una [referencia del lenguaje completa](app-insights-analytics-reference.md).

Las consultas suelen comenzar por el nombre de una secuencia de datos de telemetría, como solicitudes, excepciones o dependencias. Abra la barra de esquema a la izquierda para ver una lista de las secuencias de telemetría disponibles. La consulta es una canalización de [operaciones de consulta](app-insights-analytics-reference.md#queries-and-operators) como `where` (un filtro booleano) o `project` (que calcula nuevas propiedades). `summarize` [agrega instancias](app-insights-analytics-tour.md#aggregate-groups-of-rows), agrupándolas por las funciones que se hayan definido y, luego, aplicando funciones de agregación a los datos agrupados.

Los resultados se pueden [representar en tablas o en distintos tipos de gráfico](app-insights-analytics-tour.md#charting-the-results).

## Telemetría personalizada
La telemetría integrada que se obtiene solo con instalar Application Insights permite analizar los recuentos, tasas de éxito y tiempos de respuesta tanto de las solicitudes web realizadas en su aplicación como de las dependencias (esto es, las llamadas desde la aplicación a API de REST y SQL). También dispondrá de seguimientos de excepción y contadores de rendimiento del sistema (con el Monitor de estado del servidor). Si agrega el fragmento de código de cliente a las páginas web, obtendrá los tiempos de carga y recuentos de visualizaciones de página, excepciones de cliente y tasas de éxito y de respuesta de llamadas AJAX.

Analizar todos estos datos de telemetría puede aportar cuantiosa información sobre el uso y el rendimiento de la aplicación. Pero a veces no es suficiente. Puede que quiera supervisar la longitud de una cola para ajustar el rendimiento; hacer un recuento de las ventas y segmentarlas por ubicación, o bien, en el lado cliente, averiguar con qué frecuencia se hace clic en un botón determinado para optimizar la experiencia de usuario.

La [API de Application Insights](app-insights-api-custom-events-metrics.md) proporciona llamadas `TrackEvent(name)` y `TrackMetric(name, value)` para que pueda enviar sus propias métricas y eventos personalizados. Hay llamadas equivalentes para el lado cliente.

Por ejemplo, si la página web es una aplicación de juego de una sola página, podría insertar líneas en los lugares adecuados para iniciar sesión cuando el usuario gane o pierda una partida:

    appInsights.trackEvent("WinGame");
    ...
    appInsights.trackEvent("LoseGame");

Luego, podemos trazar un gráfico de los recuentos de eventos personalizados, segmentándolos por nombre de evento:

![Análisis](./media/app-insights-overview/11.png)

### Seguimientos de registro
De cara a realizar diagnósticos, hay un evento personalizado `TrackTrace(message)` que se puede usar para el seguimiento de las ejecuciones. En las características de búsqueda y Analytics, puede buscar en el contenido del mensaje, que es más largo que un nombre de evento.

Si ya usa una plataforma de registro como Log4Net, NLog, Log4J o System.Diagnostic.Trace, Application Insights puede capturar esas llamadas de seguimiento y, por tanto, aparecerán junto a otros datos de telemetría. Las herramientas de Visual Studio agregan automáticamente el módulo SDK apropiado.

## Paneles
Muchas aplicaciones constan de varios componentes, como un servicio web y uno o varios procesadores back-end. Cada componente se supervisará por medio de un recurso de Application Insights separado. Si el sistema se ejecuta en Azure, puede que también use (y supervise) servicios como centros de eventos y aprendizaje automático.

Para supervisar todo el sistema puede seleccionar los gráficos más interesantes de diferentes aplicaciones y anclarlos a un [panel](app-insights-dashboards.md) de Azure, lo que le permitirá estar al tanto de todo el sistema continuamente.

![Paneles](./media/app-insights-overview/12.png)

De hecho, puede crear varios paneles; por ejemplo, un panel de centro de reunión para supervisar el estado general del sistema; un panel de diseño centrado en el uso de diferentes características; un panel independiente para los componentes en pruebas, y así sucesivamente.

Al igual que los recursos, los paneles se pueden compartir entre los miembros del equipo.

## Desarrollo en Visual Studio
Si usa Visual Studio para desarrollar la aplicación, verá que hay varias herramientas de Application Insights integradas.

### Búsqueda de diagnóstico
La ventana de búsqueda muestra los eventos que se han registrado. (Si inició la sesión en Azure al configurar Application Insights, podrá buscar los mismos eventos en el portal.)

![Haga clic con el botón derecho en el proyecto y seleccione Application Insights, Búsqueda.](./media/app-insights-visual-studio/34.png)

La búsqueda de texto sin formato funciona en todos los campos de los eventos. Por ejemplo, buscar parte de la dirección URL de una página; o el valor de una propiedad, como la ciudad del cliente; o palabras específicas en un registro de seguimiento.

Haga clic en cualquier evento para ver sus propiedades con todo detalle.

También puede abrir la pestaña de elementos relacionados para ayudar a diagnosticar las solicitudes con errores o las excepciones.

![](./media/app-insights-visual-studio/41.png)

### Concentrador de diagnósticos
El concentrador de diagnósticos (en Visual Studio 2015 o posterior) muestra los datos de telemetría del servidor de Application Insights cuando se generan. Esto funciona incluso si solo ha optado por instalar el SDK, sin conectarlo a un recurso del Portal de Azure.

![Abra la ventana Herramientas de diagnóstico e inspeccione los eventos de Application Insights.](./media/app-insights-visual-studio/31.png)

### Excepciones
Si ha [configurado la supervisión de excepciones](app-insights-asp-net-exceptions.md), los informes de excepciones se mostrarán en la ventana de búsqueda.

Haga clic en una excepción para obtener un seguimiento de la pila. Si el código de la aplicación es abierto en Visual Studio, puede hacer clic para recorrer el seguimiento de la pila hasta dar con la línea correspondiente del código.

![Seguimiento de la pila de excepción](./media/app-insights-visual-studio/17.png)

Además, en la línea de Code Lens sobre cada método, verá un recuento de las excepciones registradas por Application Insights en las últimas 24 horas.

![Seguimiento de la pila de excepción](./media/app-insights-visual-studio/21.png)

### Supervisión local
(Desde Visual Studio 2015 Update 2) Si no ha configurado el SDK para enviar datos de telemetría al portal de Application Insights (para que no haya ninguna clave de instrumentación en ApplicationInsights.config), la ventana diagnóstico mostrará los datos de telemetría de la sesión de depuración más reciente.

Esto es conveniente si ya ha publicado una versión anterior de la aplicación. No quiere que los datos de telemetría de las sesiones de depuración se mezclen con los datos de telemetría en el portal de Application Insights de la aplicación publicada.

También es útil si tiene [datos de telemetría personalizados](app-insights-api-custom-events-metrics.md) que desea depurar antes de enviarlos al portal.

* *En primer lugar, configuré totalmente Application Insights para enviar los datos de telemetría al portal. Pero ahora me gustaría ver los datos de telemetría solo en Visual Studio.*
  
  * En la configuración de la ventana de búsqueda, hay una opción para buscar diagnósticos locales, incluso si la aplicación envía datos de telemetría al portal.
  * Para detener el envío de datos de telemetría al portal, convierta en comentario la línea `<instrumentationkey>...` de ApplicationInsights.config. Cuando esté listo para enviar de nuevo datos de telemetría al portal, quite los comentarios.

## Tendencias
Tendencias es una herramienta de Visual Studio para visualizar cómo se comporta la aplicación con el paso del tiempo.

Elija **Explorar tendencias de telemetría** con el botón de la barra de herramientas de Application Insights o en la ventana Búsqueda de Application Insights. Seleccione una de las cinco consultas comunes para empezar. Puede analizar diferentes conjuntos de datos en función de los tipos de telemetría, los intervalos de tiempo y otras propiedades.

Para detectar anomalías en los datos, elija una de las opciones de anomalía en la lista desplegable "Tipo de vista". Con las opciones de filtrado de la parte inferior de la ventana, resulta más sencillo centrarse en subconjuntos específicos de la telemetría.

![Tendencias](./media/app-insights-visual-studio/51.png)

## Publicar una nueva compilación
### Secuencia de métricas en directo
Stream de métricas en vivo muestra las métricas de la aplicación de este mismo momento, con una latencia casi de tiempo real de 1 segundo. Esto resulta muy útil si está publicando una compilación nueva y desea asegurarse de que todo esté funcionando como se esperaba o si quiere investigar un incidente en tiempo real.

![En la hoja de información general, haga clic en Stream en vivo.](./media/app-insights-metrics-explorer/live-stream.png)

A diferencia del Explorador de métricas, la Secuencia de métricas en directo muestra un conjunto fijo de métricas. Los datos se conservan solo mientras se encuentran en el gráfico, y luego se descartan.

### Anotaciones
Las [anotaciones de la versión](app-insights-annotations.md) de los gráficos de métricas muestran dónde se ha implementado una nueva compilación. Permiten ver fácilmente si los cambios tuvieron algún efecto en el rendimiento de la aplicación. Los puede crear automáticamente no solo el [sistema de compilación de Visual Studio Team Services](https://www.visualstudio.com/es-ES/get-started/build/build-your-app-vs), sino también [PowerShell](#create-annotations-from-powershell).

![Ejemplo de anotaciones con correlación visible con el tiempo de respuesta del servidor](./media/app-insights-annotations/00.png)

Las anotaciones de versión son una característica del servicio de versiones y compilaciones basado en la nube de Visual Studio Team Services.

## Alertas
Si algo va mal en la aplicación, querrá saberlo inmediatamente.

Application Insights ofrece tres tipos de alerta, que se entregan por correo electrónico.

### Diagnóstico proactivo
El [diagnóstico proactivo](app-insights-nrt-proactive-diagnostics.md)** se configura automáticamente, no hay que hacer nada. Siempre que el sitio tenga suficiente tráfico, recibirá un correo electrónico si hay un aumento poco habitual en las solicitudes con error para el momento del día o la tasa de solicitudes. La alerta contiene información de diagnóstico.

Esta es una alerta de ejemplo.

![El ejemplo de alerta inteligente muestra el análisis del clúster sobre el error](./media/app-insights-nrt-proactive-diagnostics/010.png)

Un segundo tipo de detección proactiva detecta las correlaciones entre errores y factores como la ubicación, el sistema operativo de cliente o el tipo de explorador.

### Alertas de métricas
Puede configurar [alertas de métricas](app-insights-alerts.md) que le avisen en el momento en que cualquier métrica supera un valor de umbral durante un período determinado (por ejemplo, recuentos de error, memoria o vistas de página).

![En el Explorador de métricas, elija Reglas de alertas, Agregar alerta](./media/app-insights-metrics-explorer/appinsights-413setMetricAlert.png)

### Disponibilidad
Las [pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) envían solicitudes a su sitio desde nuestros servidores en varias ubicaciones de todo el mundo. Estas pruebas informan de cuándo el sitio no está disponible en Internet o responde lentamente.

![Ejemplo de prueba web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

## Exportación
Hay varias formas de obtener datos de telemetría del portal de Application Insights:

* La [exportación continua](app-insights-export-telemetry.md) es ideal si desea conservar grandes cantidades de datos de telemetría durante más tiempo del período de retención estándar establecido.
* [El botón Exportar](app-insights-metrics-explorer.md#export-to-excel) de la parte superior de una hoja de búsqueda o métricas permite transferir tablas y gráficos a una hoja de cálculo de Excel.
* [Analytics](app-insights-analytics.md) proporciona un lenguaje de consulta eficaz para telemetría y también permite exportar los resultados.
* La API de REST de acceso a datos se puede usar para buscar y extraer datos. Puede ejecutar consultas de Analytics.
* Si lo que le interesa es [explorar los datos en Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx), puede hacerlo sin usar la exportación continua.

![Visualización de datos en Power BI](./media/app-insights-overview/210.png)

## Administración de datos
Existen límites en el uso de Application Insights, que dependen en cierta medida del esquema de precios que haya elegido. Estos son los límites más importantes:

* Tasa de telemetría por minuto
* Número de puntos de datos por mes
* Período de retención de los datos

El [muestreo](app-insights-sampling.md) es un mecanismo para reducir costos y evitar la limitación. Descarta un porcentaje de los datos de telemetría y conserva una muestra representativa. Los elementos asociados (como las excepciones y las solicitudes que los han provocado) se conservan o descartan juntos. En el caso de las aplicaciones ASP.NET, el muestreo es automático y se realiza en la aplicación; de lo contrario, puede establecer que se realice en la ingesta en el portal.

## Pasos siguientes
Comience en el tiempo de ejecución con:

* [Servidor IIS](app-insights-monitor-performance-live-website-now.md)
* [Servidor de J2EE](app-insights-java-live.md)

Comience en el tiempo de desarrollo con:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## Soporte y comentarios
* Preguntas y problemas:
  * [Solución de problemas][qna]
  * [Foro de MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
  * [Obtener soporte técnico Developer](app-insights-get-dev-support.md)
* Sus sugerencias:
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Blog:
  * [Blog de Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## Vídeos
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



<!---HONumber=AcomDC_0914_2016-->