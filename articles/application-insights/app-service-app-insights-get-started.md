<properties 
	pageTitle="Supervisión del rendimiento y del uso de aplicaciones con Application Insights" 
	description="Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/26/2015" 
	ms.author="awills"/>

# Supervisión del rendimiento y del uso de aplicaciones con Application Insights

*Application Insights se encuentra en su versión de vista previa*.


Application Insights de Visual Studio supervisa su aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento][detect], y [descubrir cómo se usa la aplicación][knowUsers]. Se puede usar con una amplia variedad de tipos de aplicaciones: aplicaciones web de ASP.NET y Java iOS; aplicaciones de Android, Windows y de otros dispositivos; aplicaciones de HTML + JavaScript.

En este artículo nos centraremos en los tipos de aplicaciones que se desarrollan en Visual Studio. También hay extensiones de Application Insights para otros IDE.

Necesitará [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (o posterior) y una cuenta en [Microsoft Azure](http://azure.com).

## <a name="ide"></a>Incorporación de Application Insights a un proyecto

#### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio, asegúrese de seleccionar Application Insights.


![Crear un proyecto ASP.NET](./media/app-service-app-insights-get-started/appinsights-01-vsnewp1.png)

Visual Studio crea un recurso en Application Insights, agrega el SDK al proyecto y coloca la clave en el `.config` archivo.

Si el proyecto tiene páginas web, también agrega el [SDK de JavaScript][client] a la página web principal.

#### ....o si se trata de un proyecto existente

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Elija Agregar Application Insights](./media/app-service-app-insights-get-started/appinsights-03-addExisting.png)

Visual Studio crea un recurso en Application Insights, agrega el SDK al proyecto y coloca la clave en el `.config` archivo.

En este caso, no se agrega el [SDK de JavaScript][client] a las páginas web: le recomendamos que lo haga en el paso siguiente.

#### Opciones de configuración

La primera vez deberá iniciar sesión o registrarse en Microsoft Azure en vista previa. (Al margen de la cuenta de Visual Studio Online).

Si esta aplicación forma parte de una aplicación mayor, es posible que quiera usar **Configurar valor** para colocarla en el mismo grupo de recursos que los demás componentes.

*¿No encuentra la opción Application Insights? Para algunos tipos de proyecto, puede usar Application Insights [agregando manualmente el SDK][windows].*

#### Abra Application Insights desde un proyecto.

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/app-service-app-insights-get-started/appinsights-04-openPortal.png)


## <a name="run"></a> 3. Ejecución del proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![](./media/app-service-app-insights-get-started/appinsights-09eventcount.png)

## <a name="monitor"></a> 4. Visualización de la telemetría

Vuelva al [portal de Azure][portal] y busque el recurso de Application Insights.

Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-service-app-insights-get-started/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas.][perf]

Implemente ahora la aplicación y observe cómo se acumulan los datos.


Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Cuando se implementa una aplicación, los datos se acumulan a menor velocidad.


#### ¿No hay datos?

* Abra el mosaico [Búsqueda de diagnóstico][diagnostic] mosaico, para ver los eventos individuales.
* Use la aplicación y abra varias páginas para generar telemetría.
* Espere unos segundos y haga clic en Actualizar.
* Vea [Solución de problemas][qna].


## Pasos siguientes

Ahora que va a enviar datos desde el lado servidor de la aplicación, siga estos pasos para obtener una vista de 360 grados:

* [Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.
* [Agregue el SDK de JavaScript a sus páginas web][client] para obtener telemetría basada en el explorador, como recuentos de vista de página, tiempos de carga de página y excepciones de script, y para poder editar telemetría personalizada en los scripts de página.
* Agregue el seguimiento de dependencia para diagnosticar problemas causados por bases de datos u otros componentes usados por la aplicación. 
 * [Incorporación del seguimiento de dependencia en servidores IIS][redfield]
 * [Incorporación del seguimiento de dependencia en aplicaciones web de Azure][azure]
* [Captura del seguimientos de registros][netlogs] de su marco de registro de favoritos
* [Realice el seguimiento de métricas y eventos personalizado][api] en el cliente, en el servidor o en ambos para obtener más información acerca de cómo se usa la aplicación.


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54--> 