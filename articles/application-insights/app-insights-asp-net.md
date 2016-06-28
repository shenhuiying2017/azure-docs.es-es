<properties 
	pageTitle="Configurar el análisis de aplicaciones web para ASP.NET con Application Insights" 
	description="Configurar el análisis de rendimiento, disponibilidad y uso para el sitio web de ASP.NET, hospedado localmente o en Azure." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/21/2016" 
	ms.author="awills"/>


# Configurar Application Insights para ASP.NET

[Application Insights de Visual Studio](app-insights-overview.md) supervisa su aplicación activa para ayudarle a [detectar y diagnosticar problemas y excepciones de rendimiento](app-insights-detect-triage-diagnose.md), y [descubrir cómo se usa la aplicación](app-insights-overview-usage.md). Funciona con las aplicaciones hospedadas en máquinas virtuales en la nube o en sus servidores IIS locales propios, así como con las aplicaciones web de Azure.


## Antes de comenzar

Necesita:

* Visual Studio 2013, actualización 3 o superior. Es mejor que sea superior.
* Una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a esta con su [cuenta Microsoft](http://live.com). 

Puede consultar otros artículos si está interesado en:

* [La instrumentación de una aplicación web en tiempo de ejecución](app-insights-monitor-performance-live-website-now.md)
* [Servicios en la nube de Azure](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Incorporación del SDK de Application Insights


### Si se trata de un proyecto nuevo:

Cuando cree un proyecto nuevo en Visual Studio, asegúrese de que Application Insights está seleccionado.


![Crear un proyecto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ....o si se trata de un proyecto existente

Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y elija **Agregar telemetría de Application Insights** o **Configurar Application Insights**.

![Elija Agregar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ¿Es un proyecto de ASP.NET Core? [Siga estas instrucciones para corregir unas pocas líneas de código](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 



## <a name="run"></a> 2. Ejecutar la aplicación

Ejecute la aplicación con F5 y pruébela. Abra varias páginas para generar telemetría.

En Visual Studio, verá un recuento de los eventos que se han registrado.

![En Visual Studio, el botón de Application Insights se muestra durante la depuración.](./media/app-insights-asp-net/54.png)

## 3\. Visualización de los datos de telemetría...

### ... en Visual Studio

Abra la ventana de Application Insights en Visual Studio: haga clic en el botón Application Insights, o bien haga clic con el botón derecho en el proyecto en el Explorador de soluciones:

![En Visual Studio, el botón de Application Insights se muestra durante la depuración.](./media/app-insights-asp-net/55.png)

Esta vista muestra los datos de telemetría generados en el servidor de la aplicación. Experimente con los filtros y haga clic en cualquier evento para ver más información al respecto.

[Más información acerca de las herramientas de Application Insights en Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### ... en el portal

Salvo que elija *Install SDK only* (Instalar SDK solamente), también puede ver la telemetría en el portal web de Application Insights.

El portal tiene más gráficos, herramientas de análisis y paneles que Visual Studio.


Abra el recurso de Application Insights en el [Portal de Azure](https://portal.azure.com/).

![Haga clic con el botón secundario en el proyecto y abra el Portal de Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

El portal se abrirá en una vista de los datos de telemetría desde su aplicación: ![](./media/app-insights-asp-net/66.png)

* Los eventos individuales que aparecen en **Búsqueda** (1). Los datos aparecen aquí en primer lugar (y en una [transmisión de métricas en directo](app-insights-metrics-explorer.md#live-metrics-stream)). Haga clic en cualquier evento para ver sus propiedades. 
* Las métricas agregadas aparecen en los gráficos (2). Los datos pueden tardar uno o dos minutos en aparecer aquí. Haga clic en cualquiera de los gráficos para abrir una hoja en la que encontrará más información.

[Más información acerca del uso de Application Insights en el Portal de Azure](app-insights-dashboards.md).

##<a name="land"></a> ¿Qué hizo "Agregar Application Insights"?

Application Insights envía datos de telemetría desde su aplicación al portal de Application Insights (que está hospedado en Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

Por tanto, el comando realizó tres operaciones:

1. Agregar el paquete NuGet del SDK web de Application Insights al proyecto. Para verlo en Visual Studio, haga clic con el botón secundario en el proyecto y elija Administrar paquetes de NuGet.
2. Crear un recurso de Application Insights en el [Portal de Azure](https://portal.azure.com/). Es donde verá los datos. Recupera la *clave de instrumentación*, que identifica el recurso.
3. Insertar la clave de instrumentación en `ApplicationInsights.config`, con el fin de que el SDK pueda enviar datos de telemetría al portal.

Si lo desea, puede realizar estos pasos manualmente para [ASP.NET 4](app-insights-asp-net-manual.md) o [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## Para actualizar a futuras versiones del SDK

Para actualizar a una [nueva versión del SDK](app-insights-release-notes-dotnet.md), vuelva a abrir el Administrador de paquetes de NuGet y filtre los paquetes instalados. Seleccione Microsoft.ApplicationInsights.Web y elija Actualizar.

Si ha realizado personalizaciones en ApplicationInsights.config, guarde una copia del mismo antes de actualizar y después combine los cambios en la nueva versión.



## Pasos siguientes

| | 
|---|---
|**[Trabajo con Application Insights en Visual Studio](app-insights-visual-studio.md)**<br/>Depuración con telemetría, búsqueda de diagnóstico, profundización en el código.|![Visual studio](./media/app-insights-asp-net/61.png)
|**[Navegación y paneles en el portal de Application Insights](app-insights-dashboards.md)**<br/>Paneles, eficaces herramientas de diagnóstico y análisis, alertas, un mapa activo de dependencias de la aplicación y exportación de telemetría. |![Visual studio](./media/app-insights-asp-net/62.png)
|**[Adición de más datos](app-insights-asp-net-more.md)**<br/>Supervise el uso, la disponibilidad, las dependencias y las excepciones. Integrar seguimientos de marcos de registro. Escribir telemetría personalizada. | ![Visual studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0622_2016-->