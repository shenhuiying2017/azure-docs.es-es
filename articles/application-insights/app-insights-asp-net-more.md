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
	ms.date="08/10/2016" 
	ms.author="awills"/>

# Más telemetría desde Application Insights

Una vez que haya [agregado Application Insights a su código de ASP.NET](app-insights-asp-net.md), puede realizar varias acciones para obtener más datos de telemetría.

## Si la aplicación se ejecuta en un servidor IIS...

Si la aplicación está hospedada en servidores IIS que controle, instale al Monitor de estado de Application Insights en los servidores. Si ya está instalado, no es preciso hacer nada.

1. En cada servidor web de IIS, inicie sesión con las credenciales de administrador.
2. Descargue y ejecute el [instalador del Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648).
3. En el asistente de instalación, inicie sesión en Microsoft Azure.

No es preciso hacer nada más, pero puede confirmar que la supervisión está habilitada EN la aplicación.

![Extender en Azure](./media/app-insights-asp-net-more/025.png)

(También puede utilizar Monitor de estado para [habilitar la supervisión en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md), aunque no haya instrumentado las aplicaciones en Visual Studio.)

### ¿Qué obtiene?

Si Monitor de estado está instalado en las máquinas servidor, se obtienen datos de telemetría adicionales:

* Telemetría de dependencia (llamadas SQL y llamadas REST realizadas por la aplicación) para aplicaciones .NET 4.5. (Para las versiones posteriores de. NET, Monitor de estado no es necesario para la telemetría de dependencia.)
* Los seguimientos de la pila de excepciones muestran más detalles.
* Contadores de rendimiento. En Application Insights, estos contadores aparecen en la hoja Servidores.

![Extender en Azure](./media/app-insights-asp-net-more/070.png)

Para ver más o menos contadores, [edite los gráficos](app-insights-metrics-explorer.md). Si el contador de rendimiento que desea no está en el conjunto disponible, puede [agregarlo al conjunto recopilado por el módulo del contador de rendimiento](app-insights-web-monitor-performance.md#system-performance-counters).

## Si es una aplicación web de Azure...

Si la aplicación se ejecuta como una aplicación web de Azure, vaya al panel de control de Azure de la aplicación o la máquina virtual, y agregue la extensión de Application Insights. En **Herramientas**, abra **Supervisión de rendimiento** y configure **Application Insights**. Cuando se le solicite, elija el recurso de Application Insights que ha creado.

![Extender en Azure](./media/app-insights-asp-net-more/05-extend.png)

### ¿Qué obtiene?

* Los seguimientos de la pila de excepciones muestran más detalles.
* Telemetría de dependencia (llamadas SQL y llamadas REST realizadas por la aplicación) para aplicaciones .NET 4.5. (Para las versiones posteriores de. NET, la extensión no es necesaria para la telemetría de dependencia.)

![Extender en Azure](./media/app-insights-asp-net-more/080.png)

(También puede utilizar este método para [habilitar la supervisión en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md), aunque no haya instrumentado la aplicación en Visual Studio.)


## Rendimiento del explorador

Agregue el fragmento de código de JavaScript de Application Insights a sus páginas web para obtener datos de telemetría de los exploradores cliente.

1. En Azure, abra el recurso de Application Insights para su aplicación.
2. Abra Inicio rápido, Monitor Client Side (Supervisar lado de cliente) y copie el fragmento de código.
3. Péguelo para que aparezca en el encabezado de cada página web (para ello debe pegarlo en la página de diseño del patrón).

![Extender en Azure](./media/app-insights-asp-net-more/100.png)

### ¿Qué obtiene?

* Puede escribir JavaScript para enviar [datos de telemetría personalizados desde las páginas web](app-insights-api-custom-events-metrics.md), por ejemplo para realizar un seguimiento de los clics de los botones.
* En [Analytics](app-insights-analytics.md), datos en `pageViews` y datos de AJAX en `dependencies`.
* [Rendimiento de cliente y datos de uso](app-insights-javascript.md) en la hoja Exploradores.

![Extender en Azure](./media/app-insights-asp-net-more/090.png)

## Pruebas web de disponibilidad

Envíe las solicitudes HTTP de la aplicación web a intervalos regulares desde todo el mundo. Recibirá una alerta si la respuesta es lenta o poco confiable.

En el recurso de Application Insights para su aplicación, haga clic en el icono de disponibilidad para agregar, editar y ver las pruebas web.

Puede agregar varias pruebas que se ejecutan en distintas ubicaciones.

![Extender en Azure](./media/app-insights-asp-net-more/110.png)

[Más información](app-insights-monitor-web-app-availability.md)

## Registro y telemetría personalizada

Los paquetes de Application Insights que ha agregado al código proporcionan una API a la que puede llamar desde la aplicación.

* [Genere sus propios eventos y métricas](app-insights-api-custom-events-metrics.md), por ejemplo para contar eventos de negocio o supervisar el rendimiento.
* [Capture seguimientos de registro](app-insights-asp-net-trace-logs.md) de Log4Net, NLog o System.Diagnostics.Trace.
* [Filtre, modifique o aumente](app-insights-api-filtering-sampling.md) la telemetría estándar enviada desde su aplicación mediante la escritura de procesadores de telemetría.


## Análisis y presentación eficaces

Hay muchas formas de explorar los datos. Si ha iniciado recientemente con Application Insights, consulte estos artículos:

||
|---|---
|[**Búsqueda de diagnóstico de datos de instancia**](app-insights-visual-studio.md)<br/>Busque y filtre eventos como solicitudes, excepciones, llamadas de dependencia, seguimientos de registro y vistas de páginas. En Visual Studio, vaya al código desde los seguimientos de la pila.|![Visual studio](./media/app-insights-asp-net/61.png)
|[**Explorador de métricas para datos agregados**](app-insights-metrics-explorer.md)<br/>Explore, filtre y segmente datos agregados, como los índices de solicitudes, errores y excepciones; los tiempos de respuesta y los tiempos de carga de página.|![Visual studio](./media/app-insights-asp-net-more/060.png)
|[**Paneles**](app-insights-dashboards.md#dashboards)<br/>Combine datos de varios recursos y compártalos con otros. Ideal para aplicaciones de varios componentes y para la presentación continua en la sala de reuniones. |![Ejemplo de paneles](./media/app-insights-asp-net/62.png)
|[**Secuencia de métricas en directo**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>Al implementar una nueva compilación, fíjese en estos indicadores de rendimiento casi en tiempo real para asegurarse de que todo funciona según lo esperado.|![Ejemplo de análisis](./media/app-insights-asp-net-more/050.png)
|[**Analytics**](app-insights-analytics.md)<br/>Responda preguntas complejas acerca del uso y el rendimiento de su aplicación mediante este eficaz lenguaje de consulta.|![Ejemplo de análisis](./media/app-insights-asp-net-more/010.png)
|[**Alertas automáticas y manuales**](app-insights-alerts.md)<br/>Las alertas automáticas se adaptan a los patrones normales de telemetría de la aplicación y se desencadenan cuando algo no responde al patrón habitual. También puede establecer alertas sobre niveles de métricas estándares o personalizadas.|![Ejemplo de alerta](./media/app-insights-asp-net-more/020.png)

## Administración de datos

|||
|---|---|
|[**Exportación continua**](app-insights-export-telemetry.md)<br/>Copie todos los datos de telemetría en el almacenamiento para poder analizarlos a su ritmo.|
|**API de acceso a datos**<br/>Próximamente.|
|[**Muestreo**](app-insights-sampling.md)<br/>Reduce la velocidad de los datos y le permite permanecer dentro del límite de su plan de tarifa.|![Icono de muestreo](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0810_2016-->