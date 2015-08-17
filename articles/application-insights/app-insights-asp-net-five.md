<properties 
	pageTitle="Application Insights para ASP.NET 5" 
	description="Supervise la disponibilidad, el rendimiento y el uso de las aplicaciones web." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="awills"/>

# Application Insights para ASP.NET 5

Visual Studio Application Insights le permite supervisar la disponibilidad, el rendimiento y el uso de su aplicación web. Con los comentarios que obtendrá sobre el rendimiento y la eficacia de la aplicación en su entorno natural, pueda tomar decisiones meditadas sobre la dirección del diseño en cada ciclo de vida de desarrollo.

![Ejemplo](./media/app-insights-asp-net-five/sample.png)

Necesitará una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft.

## Cree un proyecto de ASP.NET 5

...si aún no lo ha hecho

Use la plantilla de proyecto estándar de ASP.NET 5 en Visual Studio de 2015.


## Creación de recursos en Application Insights

En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Seleccione la opción ASP.NET.

![Haga clic en Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-asp-net-five/01-new-asp.png)

En la hoja de [recursos][roles] que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.

La elección del tipo de aplicación establece el contenido predeterminado de las hojas de recursos y las propiedades que estarán visibles en el [Explorador de métricas][metrics].

##  Configure su proyecto con la clave de instrumentación.

Copie la clave del recurso de Application Insights:

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-asp-net-five/02-props-asp.png)

En el proyecto de ASP.NET 5, péguela en `config.json`:

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

O bien, si prefiere que la configuración sea dinámica, puede agregar este código a la clase de inicio de la aplicación:

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## Agregar Application Insights a un proyecto


#### Referencia al paquete NuGet

Busque el [número de versión más reciente](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases) del paquete NuGet.

Abra `project.json` y edite la sección `dependencies`:

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta4",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta4"
      }
    }

#### Análisis del archivo de configuración

En `startup.cs`:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

En el método `Startup`:

    // Setup configuration sources.
    var configuration = new Configuration()
       .AddJsonFile("config.json")
       .AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    configuration.AddEnvironmentVariables();
    Configuration = configuration;

    if (env.IsEnvironment("Development"))
    {
      configuration.AddApplicationInsightsSettings(developerMode: true);
    }

En el método `ConfigurationServices`:

    services.AddApplicationInsightsTelemetry(Configuration);

En el método `Configure`:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## Agregar la instrumentación de cliente de JavaScript

Si tiene un archivo \_Layout.cshtml, inserte ahí el código siguiente. De lo contrario, coloque el código en cualquier página de la que quiera realizar un seguimiento.

Defina la inserción en la parte superior del archivo:

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

Inserte la aplicación auxiliar Html delante de la etiqueta `</head>` y delante de cualquier otro script. Cualquier telemetría de JavaScript personalizada que quiera notificar de la página se debe insertar después de este fragmento de código:

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## Ejecutar la aplicación

Depure la aplicación en Visual Studio o publíquela en el servidor web.

## Visualización de los datos de la aplicación

Vuelva al [Portal de Azure][portal] y busque el recurso de Application Insights. Si no hay ningún dato en la hoja Información general, espere un minuto o dos y haga clic en Actualizar.

* [Seguimiento del uso de la aplicación][usage]
* [Diagnóstico de problemas de rendimiento][detect]
* [Configuración de pruebas web para supervisar la disponibilidad][availability]



## Código abierto

[Lectura y contribución al código](https://github.com/Microsoft/ApplicationInsights-aspnet5)


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
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=August15_HO6-->