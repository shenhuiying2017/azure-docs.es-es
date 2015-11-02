<properties 
	pageTitle="Application Insights para ASP.NET" 
	description="Analice los patrones de uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." 
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
	ms.date="10/13/2015" 
	ms.author="awills"/>


# Configurar Application Insights para ASP.NET

*Application Insights se encuentra en su versión de vista previa.*

<a name="selector1"></a>

[Application Insights de Visual Studio](http://azure.microsoft.com/services/application-insights) supervisa su aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento][detect], y [descubrir cómo se usa la aplicación][knowUsers]. Funciona instalando un SDK en la aplicación. El SDK envía telemetría sobre la aplicación al servicio de Application Insights, donde analiza y visualiza el comportamiento de la aplicación.


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Agregue el SDK a su aplicación en Visual Studio y obtendrá gráficos de solicitudes de servidor, tiempos de respuesta y errores.

![Gráficos de supervisión de rendimiento de ejemplo](./media/app-insights-asp-net/10-perf.png)

También podrá usar la API para supervisar el uso en detalle.

#### Antes de comenzar

Necesita:

* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com).
* Visual Studio 2013, actualización 3 o superior.

## <a name="ide"></a> Adición de Application Insights a su proyecto de Visual Studio

#### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio, asegúrese de seleccionar Application Insights.


![Creación de un proyecto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ....o si se trata de un proyecto existente

Haga clic con el botón secundario en el Explorador de soluciones y seleccione Agregar Application Insights.

![Elija Agregar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Opciones de configuración

La primera vez deberá iniciar sesión o registrarse en Microsoft Azure.

Si esta aplicación forma parte de una aplicación mayor, es posible que quiera usar **Configurar valor** para colocarla en el mismo grupo de recursos que los demás componentes.


####<a name="land"></a> ¿Qué hizo "Agregar Application Insights"?

El comando realizó estos pasos (que podría [hacer manualmente](app-insights-start-monitoring-app-health-usage.md) en su lugar si lo prefiere):

* Crea un recurso de Application Insights en el [portal de Azure][portal]. Es donde verá los datos. Recupera la *clave de instrumentación*, que identifica el recurso.
* Agrega el paquete NuGet del SDK web de Application Insights al proyecto. Para verlo en Visual Studio, haga clic con el botón secundario en el proyecto y elija Administrar paquetes de NuGet.
* Coloca la clave de instrumentación en `ApplicationInsights.config`.


## <a name="run"></a> Ejecución del proyecto

Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Apertura de Application Insights

Abra el recurso de Application Insights en el [Portal de Azure][portal].

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### Métricas: datos agregados

Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-asp-net/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas][perf]

* *¿No hay datos de usuario o página?* - [Agregar datos de usuario y página](../article/application-insights/app-insights-asp-net-client.md)

### Búsqueda: eventos individuales

Abra Búsqueda para investigar solicitudes individuales y sus eventos asociados.

![](./media/app-insights-asp-net/21-search.png)

[Más información sobre la búsqueda](app-insights-diagnostic-search.md)

* *¿No hay eventos asociados?* Configure [excepciones de servidor](../article/application-insights/app-insights-asp-net-exception-mvc.md) y [dependencias](../article/application-insights/app-insights-asp-net-dependencies.md).

### ¿No hay datos?

* Asegúrese de que está viendo lo correcto. Inicie sesión en el [portal de Azure](https://portal.azure.com), haga clic en "Examinar" >, "Application Insights" y, a continuación, seleccione la aplicación.
* Use la aplicación y abra varias páginas para generar telemetría.
* Abra la hoja [Buscar][diagnostic] para ver los eventos individuales. A veces, los eventos tardan un poco en llegar a través de la canalización de métricas.
* Espere unos segundos y haga clic en Actualizar.
* Vea [Solución de problemas][qna].


## Publicación de la aplicación

Implemente ahora la aplicación y observe cómo se acumulan los datos.

Si se trabaja en modo de depuración, la telemetría se agiliza a través de la canalización y los datos aparecen en cuestión de segundos. Cuando se implementa una aplicación, los datos se acumulan a menor velocidad.

#### ¿Tiene problemas el servidor de compilación?

Consulte [este apartado de la solución de problemas](app-insights-troubleshoot-faq.md#NuGetBuild).

## Pasos siguientes

- [Datos de página y usuario](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Excepciones](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Dependencias](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Disponibilidad](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## Para actualizar a futuras versiones del SDK

Para actualizar a una [nueva versión del SDK](app-insights-release-notes-dotnet.md), vuelva a abrir el Administrador de paquetes de NuGet y filtre los paquetes instalados. Seleccione Microsoft.ApplicationInsights.Web y elija Actualizar.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.



## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO4-->