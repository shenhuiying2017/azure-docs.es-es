<properties
   pageTitle="Application Insights para los Servicios en la nube de Azure"
   description="Supervisión de los roles web y de trabajo de manera eficaz con Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sdash"/>

# Application Insights para los Servicios en la nube de Azure


*Application Insights se encuentra en su versión de vista previa*.

[Las aplicaciones de los Servicios en la nube de Microsoft Azure](http://azure.microsoft.com/services/cloud-services/) se pueden supervisar mediante [Visual Studio Application Insights][start] para obtener información sobre la disponibilidad, el rendimiento, los errores y el uso.

<!-- For illustration purposes, we have added Application Insights to this [sample Azure cloud service](sample link). This code is available [here](git link), for you to follow along with the steps below. -->

## Creación de un recurso de Application Insights para cada rol

Un recurso de Application Insights es el lugar donde se analizan y muestran los datos de telemetría.

1.  En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Para el tipo de aplicación, elija la aplicación ASP.NET. 

    ![Haga clic en Nuevo, Application Insights.](./media/app-insights-cloudservices/01-new.png)

2.  Realice una copia de la clave de instrumentación. La necesitará en breve para configurar el SDK.

    ![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-cloudservices/02-props.png)


Normalmente es mejor crear un recurso independiente para los datos de cada rol web y de trabajo.

Como alternativa, puede enviar datos de todos los roles a un único recurso, pero establezca una [propiedad predeterminada][apidefaults] para que pueda filtrar o agrupar los resultados de cada rol.

## <a name="sdk"></a>Instalación del SDK en cada proyecto


1. En Visual Studio, edite los paquetes de NuGet de su proyecto de aplicación de escritorio. ![Haga clic con el botón secundario en el proyecto y seleccione Administrar paquetes de Nuget.](./media/app-insights-cloudservices/03-nuget.png)

2. Instale el SDK de Application Insights para aplicaciones web.

    ![Seleccione **En línea**, **Incluir versión preliminar** y busque "Application Insights".](./media/app-insights-cloudservices/04-ai-nuget.png)

    (Como alternativa, puede elegir el SDK de Application Insights para Aplicaciones web. Esto proporciona cierta telemetría de contador de rendimiento integrada).

3. Configure el SDK para enviar datos al recurso de Application Insights.

    Abra `ApplicationInsights.config` e inserte esta línea:

    `<InstrumentationKey>` *la clave de instrumentación que copió* `</InstrumentationKey>`

    Use la clave de instrumentación que copió del recurso de Application Insights.

    Como alternativa, puede [establecer la clave en tiempo de ejecución][apidynamicikey]. Esto le permite dirigir los informes de telemetría de diferentes entornos (desarrollo, prueba, producción) a distintos recursos.

## Ejecutar la aplicación

Ejecute el proyecto en modo de depuración en el equipo de desarrollo, o bien publíquelo en Azure. Úselo durante unos minutos para generar algunos datos de telemetría.

## <a name="monitor"></a> Visualización de los datos de telemetría

Vuelva al [Portal de Azure][portal] y busque los recursos de Application Insights.


Busque los datos en los gráficos de Información general. Al principio, solo aparecerán uno o dos puntos. Por ejemplo:

![Haga clic en las distintas opciones para obtener más datos](./media/app-insights-asp-net/12-first-perf.png)

Haga clic en cualquier gráfico para ver métricas más detalladas. [Más información acerca de las métricas][perf]

Publique ahora la aplicación y observe la acumulación de datos.


#### ¿No hay datos?

* Abra el icono [Buscar][diagnostic] para ver los eventos individuales.
* Use la aplicación y abra varias páginas para generar telemetría.
* Espere unos segundos y haga clic en Actualizar.
* Vea [Solución de problemas][qna].


## Finalización de la instalación.

Para obtener la vista completa de 360 grados de la aplicación, hay algunas cosas más que debe hacer:


* [Agregue el SDK de JavaScript a sus páginas web][client] para obtener telemetría basada en el explorador, como recuentos de vista de página, tiempos de carga de página y excepciones de script, y para poder editar telemetría personalizada en los scripts de página.
* Agregue seguimiento de dependencias para diagnosticar los problemas ocasionados por bases de datos u otros componentes usados por la aplicación.
 * [en su aplicación web o máquina virtual de Azure][azure]
 * [en el servidor IIS local][redfield]
* [Captura del seguimientos de registros][netlogs] de su marco de registro de favoritos
* [Realice el seguimiento de métricas y eventos personalizado][api] en el cliente, en el servidor o en ambos para obtener más información acerca de cómo se usa la aplicación.
* [Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.

#### Seguimiento de las solicitudes de roles de trabajo

Puede capturar el rendimiento de las llamadas a los roles de trabajo realizando su seguimiento de la misma manera que con las solicitudes HTTP. En Application Insights, el tipo de telemetría Solicitud mide una unidad de trabajo del lado servidor con nombre que se puede programar y puede realizarse correctamente o producir un error de forma independiente. Si bien el SDK captura automáticamente las solicitudes HTTP, puede insertar su propio código para realizar el seguimiento de las solicitudes a los roles de trabajo.

A continuación, se muestra un bucle de ejecución típico para un rol de trabajo:

```C#

    while (true)
    {
      Stopwatch s1 = Stopwatch.StartNew();
      var startTime = DateTimeOffset.UtcNow;
      try
      {
        // ... get and process messages ...

        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable",
            startTime, s1.Elapsed, SUCCESS_CODE, true);
      }
      catch (Exception ex)
      {
        string err = ex.Message;
        if (ex.InnerException != null)
        {
           err += " Inner Exception: " + ex.InnerException.Message;
        }
        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable", 
            startTime, s1.Elapsed, FAILURE_CODE, false);
        telemetryClient.TrackException(ex);

        // Don't flood if we have a bug in queue process loop.
        System.Threading.Thread.Sleep(60 * 1000);
      }
    }
```




[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=58-->