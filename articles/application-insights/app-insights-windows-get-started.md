<properties
	pageTitle="Análisis para aplicaciones de Windows Phone y de la Tienda Windows | Microsoft Azure"
	description="Analice el uso y el rendimiento de la aplicación de su dispositivo Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/11/2015"
	ms.author="awills"/>

# Análisis para aplicaciones de Windows Phone y de la Tienda Windows



Application Insights de Visual Studio permite supervisar una aplicación publicada en cuanto a uso y rendimiento:


> [AZURE.NOTE]Se recomienda usar [HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-store-apps-and-windows-phone-store-apps) para obtener informes de bloqueo, análisis, distribución y administración de comentarios.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


## Configuración de Application Insights para su proyecto de dispositivo Windows

Necesitará:

* Una suscripción a [Microsoft Azure][azure].
* Visual Studio 2013 o posterior.

**Aplicaciones C++ UAP**: consulte la [guía de configuración de Application Insights C++](https://github.com/Microsoft/ApplicationInsights-CPP)

### <a name="new"></a>Si crea un proyecto de aplicación de Windows...

Seleccione **Application Insights** en el cuadro de diálogo **Nuevo proyecto**.

Cuando se la pide que inicie sesión, use las credenciales de su cuenta de Azure.

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


### <a name="existing"></a>O si se trata de un proyecto existente...

Agregue Application Insights desde el Explorador de soluciones.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png) **Aplicaciones universales de Windows**: repítalo para el proyecto de Windows Phone y de la Tienda Windows. [Ejemplo de una aplicación universal de Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

## <a name="network"></a>3. Habilitación del acceso de red para la aplicación

Si la aplicación no [solicita acceso a Internet](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx) aún, tendrá que agregar esta acción a su manifiesto como una [funcionalidad obligatoria](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Ejecución del proyecto

[Ejecute la aplicación con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) y úsela para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han recibido.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

En modo de depuración, la telemetría se envía tan pronto como se genera. En modo de lanzamiento, la telemetría se almacena en el dispositivo y solo se envía cuando se reanuda la aplicación.


## <a name="monitor"></a>5. Consulta de los datos del monitor

En el [Portal de Azure](https://portal.azure.com), abra el recurso de Application Insights que creó anteriormente.

Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Si espera más datos, haga clic en **Actualizar** después de unos segundos.

Haga clic en un gráfico para ver su contenido con mayor detalle.


## <a name="deploy"></a>5. Publicación de la aplicación en Azure

[Publique la aplicación](http://dev.windows.com/publish) y vea cómo se acumulan los datos a medida que los usuarios la descargan y la usan.

## Personalización de la telemetría

#### Selección de los recopiladores

El SDK de Application Insights incluye varios recolectores que recopilan diferentes tipos de datos de la aplicación automáticamente. De forma predeterminada, todos están activos. Pero puede elegir los recolectores que quiere inicializar en el constructor de la aplicación:

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session
       | WindowsCollectors.UnhandledException);

#### Envío de datos de telemetría propios

Use la [API][api] para enviar eventos, métricas y datos de diagnóstico a Application Insights. En resumen:

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

Para obtener más información, consulte [Información general de la API: Eventos y métricas personalizados][api].

## Pasos siguientes

* [Detección y diagnóstico de bloqueos en la aplicación][windowsCrash]
* [Más información sobre las métricas][metrics]
* [Más información sobre Búsqueda de diagnóstico][diagnostic]




## Actualización a una nueva versión del SDK

Cuando [se lanza una nueva versión del SDK](app-insights-release-notes-windows.md):

* Haga clic con el botón derecho en el proyecto y elija Administrar paquetes de NuGet.
* Seleccione los paquetes de Application Insights instalados y elija **Acción: Actualizar**.


## <a name="usage"></a>Pasos siguientes


[Detección y diagnóstico de bloqueos en la aplicación][windowsCrash]

[Captura y búsqueda de registros de diagnóstico][diagnostic]


[Seguimiento del uso de la aplicación][windowsUsage]

[Uso de la API para enviar telemetría personalizada][api]

[Solución de problemas][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=Nov15_HO4-->