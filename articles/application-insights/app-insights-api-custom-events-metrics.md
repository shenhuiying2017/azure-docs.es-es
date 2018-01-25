---
title: "API de Application Insights para eventos y métricas personalizados | Microsoft Docs"
description: "Inserte unas cuantas líneas de código en su aplicación de dispositivo o de escritorio, página o servicio web, para realizar el seguimiento del uso y diagnosticar problemas."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 7d797716fb98ac85f11f956e732e08820b56affc
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API de Application Insights para eventos y métricas personalizados

Inserte unas cuantas líneas de código en la aplicación para averiguar qué uso hacen de ella los usuarios o para ayudarle a diagnosticar problemas. Puede enviar datos de telemetría desde aplicaciones de escritorio y de dispositivo y desde clientes y servidores web. Use la API de telemetría principal de [Azure Application Insights](app-insights-overview.md) para enviar métricas y eventos personalizados, así como sus propias versiones de telemetría estándar. Esta API es la misma que usan los recopiladores de datos estándar de Application Insights.

## <a name="api-summary"></a>API summary
La API es uniforme en todas las plataformas, excepto por algunas pequeñas variaciones.

| Método | Usado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, pantallas, hojas o formularios. |
| [`TrackEvent`](#trackevent) |Acciones de usuario y otros eventos. Se usa para realizar el seguimiento del comportamiento de los usuarios o para supervisar el rendimiento. |
| [`TrackMetric`](#trackmetric) |Las medidas de rendimiento, como las longitudes de cola, no están relacionadas con eventos específicos. |
| [`TrackException`](#trackexception) |Excepciones de registro para diagnóstico. Permite realizar el seguimiento del lugar donde se producen en relación con otros eventos y examinar los seguimientos de pila. |
| [`TrackRequest`](#trackrequest) |Registro de la frecuencia y duración de las solicitudes de servidor para el análisis de rendimiento. |
| [`TrackTrace`](#tracktrace) |Mensajes de registro de diagnóstico. También puede capturar registros de terceros. |
| [`TrackDependency`](#trackdependency) |Registro de la duración y frecuencia de las llamadas a componentes externos de los que depende la aplicación. |

Puede [adjuntar propiedades y métricas](#properties) a la mayoría de estas llamadas de telemetría.

## <a name="prep"></a>Antes de comenzar
Si aún no tiene una referencia en el SDK de Application Insights:

* Agregue el SDK de Application Insights a su proyecto:

  * [Proyecto de ASP.NET](app-insights-asp-net.md)
  * [Proyecto de Java](app-insights-java-get-started.md)
  * [Proyecto de Node.js](app-insights-nodejs.md)
  * [JavaScript en cada página web](app-insights-javascript.md) 
* En el código de servidor web o de dispositivo, incluya:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`
    
    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obtención de una instancia de TelemetryClient
Obtenga una instancia de `TelemetryClient` (excepto en JavaScript en páginas web):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient es seguro para subprocesos.

En proyectos de ASP.NET y Java, se recomienda que cree una instancia de TelemetryClient para cada módulo de la aplicación. Por ejemplo, puede tener una instancia de TelemetryClient en el servicio web para informar de las solicitudes HTTP entrantes y otra en una clase de middleware para notificar eventos de la lógica de negocios. Puede establecer propiedades tales como `TelemetryClient.Context.User.Id` para realizar el seguimiento de los usuarios y de las sesiones, o bien `TelemetryClient.Context.Device.Id` para identificar el equipo. Esta información se adjunta a todos los eventos enviados por la instancia.

En proyectos de Node.js, puede usar `new applicationInsights.TelemetryClient(instrumentationKey?)` para crear una nueva instancia, pero esto solo se recomienda para escenarios que requieren configuración aislada del singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent
En Application Insights, un *evento personalizado* es un punto de datos que se puede mostrar en el [Explorador de métricas](app-insights-metrics-explorer.md) como recuento agregado, y como repeticiones individuales en [Búsqueda de diagnóstico](app-insights-diagnostic-search.md). (No está relacionado con MVC ni con "eventos" de otro marco).

Inserte llamadas a `TrackEvent` en el código para contabilizar diversos eventos: la frecuencia con la que los usuarios eligen una determinada característica, con la que logran unos determinados objetivos o con la que cometen determinados tipos de errores.

Por ejemplo, en una aplicación de juego, envíe un evento cada vez que un usuario gane el juego:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Visualización de eventos en Microsoft Azure Portal
Para ver un recuento de los eventos, abra una hoja [Explorador de métricas](app-insights-metrics-explorer.md), agregue un nuevo gráfico y seleccione **Eventos**.  

![Visualización de un recuento de eventos personalizados](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar los recuentos de eventos diferentes, establezca el tipo de gráfico en **Cuadrícula** y el grupo por nombre de evento:

![Establecimiento del tipo de gráfico y agrupación](./media/app-insights-api-custom-events-metrics/07-grid.png)

En la cuadrícula, haga clic en un nombre de evento para ver todas las repeticiones individuales de ese evento. Para más información, haga clic en cualquier elemento de la lista.

![Obtenga detalles de los eventos.](./media/app-insights-api-custom-events-metrics/03-instances.png)

Para centrarse en eventos concretos de la Búsqueda o el Explorador de métricas, establezca el filtro de la hoja en los nombres de eventos que le interesan:

![Abre filtros, expanda el nombre del evento y seleccione uno o varios valores.](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Eventos personalizados en Analytics

La telemetría está disponible en la tabla `customEvents` de [Analytics de Application Insights](app-insights-analytics.md). Cada fila representa una llamada a `trackEvent(..)` en la aplicación. 

Si el [muestreo](app-insights-sampling.md) está en uso, en la propiedad itemCount se muestra un valor mayor que 1. Por ejemplo, itemCount==10 significa que de cada 10 llamadas a trackEvent(), el proceso de muestreo solo transmite una. Para obtener un recuento correcto de eventos personalizados, debería usar código como `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Application Insights puede crear gráficos de métricas que no estén conectadas a eventos concretos. Por ejemplo, puede supervisar una longitud de cola a intervalos regulares. En el caso de las métricas, las mediciones individuales tienen menos interés que las variaciones y tendencias; por tanto, los gráficos estadísticos resultan útiles.

Para enviar las métricas a Application Insights, puede usar la API `TrackMetric(..)`. Hay dos formas de enviar una métrica: 

* Valor único. Cada vez que realiza una medición en la aplicación, envía el valor correspondiente a Application Insights. Por ejemplo, suponga que cuenta con una métrica que describe el número de elementos de un contenedor. Durante un período determinado, primero coloca tres elementos en el contenedor y seguidamente retira dos. En consecuencia, llamaría a `TrackMetric` dos veces: la primera, para pasar el valor `3` y a continuación el valor `-2`. Application Insights almacena ambos valores en su nombre. 

* Agregación. Al trabajar con métricas, las mediciones individuales pocas veces resultan de interés. En su lugar, lo importante son los resúmenes de acontecimientos durante un período determinado. Los resúmenes de este tipo se denominan _agregaciones_. En el ejemplo anterior, la suma de las métricas agregadas del período correspondiente es de `1` y el recuento de los valores de las métricas es de `2`. Al usar el método de agregación, solo invocará `TrackMetric` una vez por período y enviará los valores agregados. Este es el método que se recomienda usar, ya que puede reducir significativamente los costos y la sobrecarga de rendimiento gracias a que envía menos puntos de datos a Application Insights, a pesar de seguir recopilado toda la información pertinente.

### <a name="examples"></a>Ejemplos:

#### <a name="single-values"></a>Valores únicos

Para enviar un único valor de métrica:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#, Java*

```csharp
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Agregación de métricas

Se recomienda agregar las métricas antes de enviarlas desde la aplicación para reducir el ancho de banda y los costos, y aumentar el rendimiento.
Este es un ejemplo de código de agregación:

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Métricas personalizadas en el Explorador de métricas

Para ver los resultados, abra el Explorador de métricas y agregue un gráfico nuevo. Edite el gráfico para que muestre la métrica.

> [!NOTE]
> La métrica personalizada puede tardar varios minutos en aparecer en la lista de métricas disponibles.
>

![Agregue un gráfico nuevo o seleccione un gráfico y, en Personalizada, seleccione su métrica.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas en Analytics

La telemetría está disponible en la tabla `customMetrics` de [Analytics de Application Insights](app-insights-analytics.md). Cada fila representa una llamada a `trackMetric(..)` en la aplicación.
* `valueSum`: es la suma de las medidas. Para obtener el valor medio, divídalo por `valueCount`.
* `valueCount`: el número de medidas que se agregaron en esta llamada a `trackMetric(..)`.

## <a name="page-views"></a>Vistas de página
En una aplicación de dispositivo o de página web, se envían datos de telemetría de la vista de página de forma predeterminada cuando se carga cada pantalla o página. Sin embargo, puede cambiar esta frecuencia para que se realice el seguimiento de las vistas de página en momentos diferentes o adicionales. Por ejemplo, en una aplicación que muestra pestañas u hojas, quizás quiera realizar el seguimiento de una página siempre que el usuario abra una nueva hoja.

![Uso de modos en la hoja Información general](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Los datos de usuario y de sesión se envían como propiedades junto con las vistas de página, por lo que los gráficos de usuario y de sesión se activan cuando hay datos de telemetría de vistas de página.

### <a name="custom-page-views"></a>Vistas de página personalizadas
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Si tiene varias fichas dentro de páginas HTML diferentes, puede especificar también la dirección URL:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Cronometrar las vistas de página
De forma predeterminada, los tiempos notificados como **Tiempo de carga de la vista de página** se miden desde que el explorador envía la solicitud hasta que se llama al evento de carga de página del explorador.

En su lugar, puede:

* Establecer una duración explícita en la llamada [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview): `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Usar las llamadas para cronometrar la vista de página `startTrackPage` y `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

El nombre que use como primer parámetro asocia las llamadas inicial y final. El valor predeterminado es el nombre de la página actual.

Las duraciones de carga de página resultantes que se muestran en el Explorador de métricas se derivan del intervalo que media entre las llamadas inicial y final. Depende del usuario qué intervalo se cronometra realmente.

### <a name="page-telemetry-in-analytics"></a>Telemetría de páginas en Analytics

En [Analytics](app-insights-analytics.md) hay dos tablas en las que se muestran datos de operaciones de explorador:

* La tabla `pageViews` contiene datos sobre la URL y el título de la página.
* La tabla `browserTimings` contiene datos sobre el rendimiento del cliente, como el tiempo que se tarda en procesar los datos entrantes.

Para averiguar cuánto tarda el explorador en procesar diferentes páginas:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

Para descubrir la popularidad de distintos exploradores:

```
pageViews | summarize count() by client_Browser
```

Para asociar las vistas de página a las llamadas AJAX, realice una combinación con dependencias:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
El SDK del servidor usa TrackRequest para registrar las solicitudes de HTTP.

También puede llamarlo usted mismo si quiere simular solicitudes en un contexto en el que no se está ejecutando el módulo de servicio web.

Sin embargo, lo que se recomienda para enviar telemetría de solicitudes es que la solicitud actúe como <a href="#operation-context">contexto de operación</a>.

## <a name="operation-context"></a>Contexto de operación
Puede correlacionar los elementos de telemetría juntos mediante su asociación con el contexto de la operación. El módulo de seguimiento de solicitud estándar realiza esta operación para excepciones y otros eventos enviados al procesar una solicitud HTTP. En [Búsqueda](app-insights-diagnostic-search.md) y [Análisis](app-insights-analytics.md), puede encontrar fácilmente cualquier evento asociado a la solicitud mediante su identificador de operación.

Para más información sobre la correlación, vea [Correlación de telemetría en Application Insights](application-insights-correlation.md).

Al realizar el seguimiento de la telemetría manualmente, la forma más fácil de garantizar la correlación de telemetría es mediante el uso de este patrón:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Junto con la configuración de un contexto de la operación, `StartOperation` crea un elemento de telemetría del tipo que especifique. Envía el elemento de telemetría al eliminar la operación, o si llama explícitamente a `StopOperation`. Si usa `RequestTelemetry` como tipo de telemetría, su duración se establece en el intervalo cronometrado entre el inicio y la detención.

Los elementos de telemetría notificados dentro de un ámbito de operación se convierten en "elementos secundarios" de dicha operación. Los contextos de operación pueden estar anidados. 

En la Búsqueda, el contexto de la operación se utiliza para crear la lista de **Elementos relacionados**:

![Elementos relacionados](./media/app-insights-api-custom-events-metrics/21.png)

Consulte [Seguimiento de las operaciones personalizadas con el SDK de .NET para Application Insights](application-insights-custom-operations-tracking.md) para más información sobre el seguimiento de las operaciones personalizadas.

### <a name="requests-in-analytics"></a>Solicitudes en Analytics 

En [Analytics de Application Insights](app-insights-analytics.md), las solicitudes aparecen en la tabla `requests`.

Si el [muestreo](app-insights-sampling.md) está en uso, en la propiedad de itemCount se mostrará un valor superior a 1. Por ejemplo, itemCount==10 significa que de cada 10 llamadas a trackRequest(), el proceso de muestreo solo transmite una. Para obtener un recuento correcto de solicitudes y la duración media segmentada por nombres de solicitudes, use código como el siguiente:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Enviar excepciones a Application Insights:

* Para [contarlas](app-insights-metrics-explorer.md), como indicación de la frecuencia de un problema.
* Para [examinar los casos individuales](app-insights-diagnostic-search.md).

Los informes incluyen los seguimientos de la pila.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

Los SDK capturan muchas excepciones automáticamente, por lo que no siempre es necesario llamar explícitamente a TrackException.

* ASP.NET: [escritura de código para detectar excepciones](app-insights-asp-net-exceptions.md).
* J2EE: [las excepciones se detectan automáticamente](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: las excepciones se detectan automáticamente. Si desea deshabilitar la colección automática, agregue una línea al fragmento de código que se inserta en las páginas web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Excepciones en Analytics

En [Analytics de Application Insights](app-insights-analytics.md), las excepciones aparecen en la tabla `exceptions`.

Si el [muestreo](app-insights-sampling.md) está en uso, en la propiedad `itemCount` se muestra un valor mayor que 1. Por ejemplo, itemCount==10 significa que de cada 10 llamadas a trackException(), el proceso de muestreo solo transmite una. Para obtener un recuento correcto de excepciones segmentadas por tipo de excepción, use código como el siguiente:

```
exceptions | summarize sum(itemCount) by type
```

La mayor parte de la información importante sobre pilas ya se extrajo en variables independientes, pero puede desmontar la estructura `details` para obtener más. Puesto que se trata de una estructura dinámica, debería convertir el resultado al tipo que espere. Por ejemplo: 

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para asociar las excepciones a sus respectivas solicitudes, use una combinación:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Use TrackTrace para ayudar a diagnosticar problemas mediante el envío de una ''ruta de exploración'' a Application Insights. Puede enviar fragmentos de datos de diagnóstico e inspeccionarlos en [Búsqueda de diagnóstico](app-insights-diagnostic-search.md).

Los [adaptadores de registro](app-insights-asp-net-trace-logs.md) usan esta API para enviar registros de terceros al portal.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Puede buscar en el contenido del mensaje, pero (a diferencia de los valores de propiedad) no puede filtrar por él.

El límite de tamaño en `message` es mucho mayor que el límite en propiedades.
Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, aquí puede codificar datos POST.  

Además, puede agregar un nivel de gravedad al mensaje. Y, al igual que con otra telemetría, puede agregar valores de propiedad para ayudar a filtrar o buscar distintos conjuntos de seguimientos. Por ejemplo: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

En [Búsqueda](app-insights-diagnostic-search.md), puede filtrar fácilmente todos los mensajes de un determinado nivel de gravedad relativos a una determinada base de datos.


### <a name="traces-in-analytics"></a>Seguimientos en Analytics

En [Analytics de Application Insights](app-insights-analytics.md), las llamadas a TrackTrace aparecen en la tabla `traces`.

Si el [muestreo](app-insights-sampling.md) está en uso, en la propiedad itemCount se muestra un valor mayor que 1. Por ejemplo, itemCount==10 significa que de cada 10 llamadas a `trackTrace()`, el proceso de muestreo solo transmite una. Para obtener un recuento correcto de llamadas de seguimiento, debería codificar por tanto como `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
Utilice la llamada de TrackDependency para realizar un seguimiento de los tiempos de respuesta y las tasas de éxito de las llamadas a un fragmento de código externo. Los resultados se muestran en los gráficos de dependencia del portal.

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

Recuerde que los SDK del servidor incluyen un [módulo de dependencia](app-insights-asp-net-dependencies.md) que detecta y realiza automáticamente el seguimiento de ciertas llamadas de dependencia; por ejemplo, a bases de datos y API de REST. Debe instalar un agente en el servidor para que el módulo funcione. Utilizará esta llamada si desea hacer un seguimiento de las llamadas no captadas por el seguimiento automatizado, o bien si no desea instalar el agente.

Para desactivar el módulo de seguimiento de dependencias estándar, edite [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) y elimine la referencia a `DependencyCollector.DependencyTrackingTelemetryModule`.

### <a name="dependencies-in-analytics"></a>Dependencias en Analytics

En [Analytics de Application Insights](app-insights-analytics.md), las llamadas de trackDependency aparecen en la tabla `dependencies`.

Si el [muestreo](app-insights-sampling.md) está en uso, en la propiedad itemCount se muestra un valor mayor que 1. Por ejemplo, itemCount==10 significa que de cada 10 llamadas a trackDependency(), el proceso de muestreo solo transmite una. Para obtener un recuento correcto de dependencias segmentadas por componente de destino, use código como el siguiente:

```
dependencies | summarize sum(itemCount) by target
```

Para asociar las dependencias a sus respectivas solicitudes, use una combinación:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Datos de vaciado
Normalmente, el SDK envía datos en momentos elegidos para minimizar el impacto en el usuario. Sin embargo, en algunos casos puede que desee vaciar el búfer: por ejemplo, si usa el SDK en una aplicación que se apaga.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);
    
*Node.js*

    telemetry.flush();

Tenga en cuenta que la función es asincrónica para el [canal del servidor de telemetría](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

## <a name="authenticated-users"></a>Usuarios autenticados
En una aplicación web, los usuarios se identifican por cookies (de manera predeterminada). Se puede contar al usuario más de una vez si accede a la aplicación desde un equipo o explorador diferente, o si elimina las cookies.

Si los usuarios inician sesión en su aplicación, puede obtener un recuento más preciso estableciendo el identificador del usuario autenticado en el código del explorador:

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

En una aplicación MVC web de ASP.NET, por ejemplo:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

No es necesario usar el nombre de inicio de sesión real del usuario. Solo tiene que ser un identificador único para ese usuario. No debe incluir espacios ni ninguno de los caracteres `,;=|`.

El identificador de usuario también se establece en una cookie de sesión y se envía al servidor. Si está instalado el SDK del servidor, el identificador de usuario autenticado se envía como parte de las propiedades de contexto tanto de la telemetría del cliente como del servidor. A continuación, puede filtrar y buscar en ella.

Si su aplicación agrupa a los usuarios en cuentas, también puede pasar un identificador de la cuenta (con las mismas restricciones de caracteres).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

En el [Explorador de métricas](app-insights-metrics-explorer.md), puede crear un gráfico que cuente los **Usuarios autenticados** y las **Cuentas de usuario**.

También puede [buscar](app-insights-diagnostic-search.md) puntos de datos de cliente con cuentas y nombres de usuario específicos.

## <a name="properties"></a>Filtrado, búsqueda y segmentación de los datos mediante el uso de propiedades
Puede asociar propiedades y medidas a los eventos (y también a las métricas, vistas de página, excepciones y otros datos de telemetría).

*propiedades* son valores de cadena que se pueden usar para filtrar los datos de telemetría en los informes de uso. Por ejemplo, si su aplicación proporciona varios juegos, puede adjuntar el nombre del juego a cada evento para así poder ver cuáles son los juegos más populares.

Hay un límite de aproximadamente 8192 en la longitud de cadena. (Si quiere enviar fragmentos grandes de datos, use el parámetro de mensaje de [TrackTrace](#track-trace)).

*métricas* son valores numéricos que se pueden presentar de forma gráfica. Por ejemplo, puede que quiera ver si hay un aumento gradual en las puntuaciones que alcanzan sus jugadores. Los gráficos se pueden segmentar por las propiedades enviadas con el evento, así que puede separar o apilar los gráficos para diferentes juegos.

Para que valores de métricas se muestren correctamente, deben ser mayores o iguales que 0.

Hay algunos [límites en el número de propiedades, valores de propiedad y métricas](#limits) que puede usar.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Tenga cuidado de no registrar información de identificación personal en las propiedades.
>
>

*Si utilizó métricas*, abra el Explorador de métricas y seleccione la métrica del grupo **Personalizada**:

![Abra el Explorador de métricas, seleccione el gráfico y seleccione la métrica.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Si no aparece la métrica o el encabezado **Personalizada** no se encuentra allí, cierre la hoja de selección e inténtelo de nuevo más tarde. A veces las métricas pueden tardar una hora en agregarse a través de la canalización.

*Si usó propiedades y métricas*, segmente la métrica por la propiedad:

![Establezca la opción de agrupación y seleccione la propiedad en Agrupar por.](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*En Búsqueda de diagnóstico*, puede ver las propiedades y las métricas de repeticiones individuales de un evento.

![Seleccione una instancia y luego seleccione "...".](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Utilice el campo de **Búsqueda** para ver las apariciones del evento con un valor de propiedad concreto.

![Escriba un término en Buscar.](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Más información sobre las expresiones de búsqueda](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Método alternativo para establecer propiedades y métricas
Si le resulta más cómodo, puede recopilar los parámetros de un evento en un objeto independiente:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> No vuelva a usar la misma instancia de elemento de telemetría (`event` en este ejemplo) para llamar a Track*() varias veces. Esto puede hacer que se envíe la telemetría con una configuración incorrecta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Mediciones y propiedades personalizadas en Analytics

En [Analytics](app-insights-analytics.md), las métricas y propiedades personalizadas aparecen en los atributos `customMeasurements` y `customDimensions` de cada registro de telemetría.

Por ejemplo, si agregó una propiedad llamada "game" a la telemetría de solicitudes, esta consulta cuenta el número de apariciones de diferentes valores de "game" y muestra la media de la métrica personalizada "score":

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Tenga en lo siguiente:

* Al extraer un valor de los elementos de JSON customDimensions o customMeasurements, es de tipo dinámico, por lo que debe convertirlo a `tostring` o `todouble`.
* Para tener en cuenta la posibilidad de [muestreo](app-insights-sampling.md), debería usar `sum(itemCount)`, no `count()`.



## <a name="timed"></a> Eventos de temporización
Seguro que en ocasiones le gustaría representar el tiempo que se tarda en realizar alguna acción. Por ejemplo, puede que quiera saber cuánto tiempo tardan los usuarios en considerar las opciones de un juego. Puede usar el parámetro de medida para ello.

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>Propiedades predeterminadas para la telemetría personalizada
Si quiere establecer valores de propiedad predeterminados para algunos de los eventos personalizados que escriba, puede hacerlo en una instancia de TelemetryClient. Se adjuntarán a cada elemento de telemetría enviado desde ese cliente.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Las llamadas de telemetría individuales pueden invalidar los valores predeterminados en los diccionarios de propiedad.

*Para los clientes web de JavaScript*, [use los inicializadores de telemetría de JavaScript](#js-initializer).

*Para agregar propiedades a toda la telemetría*, incluidos los datos de los módulos de recopilación estándar, [implemente `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Muestreo, filtrado y procesamiento de telemetría
Puede escribir código para procesar la telemetría antes de que se envíe desde el SDK. El procesamiento incluye los datos enviados desde los módulos de telemetría estándar, como la recopilación de solicitudes HTTP y de dependencias.

[Agregue propiedades](app-insights-api-filtering-sampling.md#add-properties) a la telemetría mediante la implementación de `ITelemetryInitializer`. Por ejemplo, puede agregar números de versión o valores calculados a partir de otras propiedades.

El [filtrado](app-insights-api-filtering-sampling.md#filtering) puede modificar o descartar la telemetría antes de que se envíe desde el SDK, mediante la implementación de `ITelemetryProcesor`. Puede controlar qué se envía y qué se descarta, pero debe tener en cuenta el efecto en las métricas. Según la forma en que se descarten los elementos, podría perder la capacidad de navegar entre elementos relacionados.

El [muestreo](app-insights-api-filtering-sampling.md) es una solución empaquetada para reducir el volumen de datos enviado desde la aplicación al portal. Lo hace sin que las métricas mostradas resulten afectadas. Y sin repercutir tampoco sobre capacidad para diagnosticar problemas navegando entre elementos relacionados, como excepciones, solicitudes y vistas de página.

[Más información](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Deshabilitación de la telemetría
Para *iniciar y detener dinámicamente* la recopilación y la transmisión de telemetría:

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para *deshabilitar los recopiladores estándar seleccionados* (por ejemplo, contadores de rendimiento, solicitudes HTTP o dependencias), elimine o convierta en comentarios las líneas correspondientes en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Puede hacer esto, por ejemplo, si quiere enviar sus propios datos de TrackRequest.

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

Para *deshabilitar los recopiladores estándar seleccionados* (por ejemplo, los contadores de rendimiento, las solicitudes HTTP o las dependencias) en el tiempo de inicialización, encadene métodos de configuración a su código de inicialización de SDK:

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Para deshabilitar estos recopiladores después de la inicialización, utilice el objeto de configuración: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Modo de programador
Durante la depuración, resulta útil enviar los datos de telemetría por la canalización para así poder ver los resultados inmediatamente. También puede recibir mensajes adicionales que le ayuden a realizar el seguimiento de los posibles problemas con la telemetría. Desactívelo en producción, ya que puede ralentizar la aplicación.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Establecimiento de la clave de instrumentación para datos de telemetría personalizados seleccionados
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Copia de la clave de instrumentación
Para evitar la mezcla de telemetría de entornos de desarrollo, pruebas y producción, puede [crear recursos separados de Application Insights](app-insights-create-new-resource.md) y cambiar sus claves en función del entorno.

En lugar de obtener la clave de instrumentación del archivo de configuración, puede establecerla en el código. Establezca la clave en un método de inicialización, como global.aspx.cs en un servicio de ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



En una página web, podría configurarla a partir del estado del servidor web, en lugar de codificarla literalmente en el script. Por ejemplo, en una página web generada en una aplicación ASP.NET:

*JavaScript en Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient tiene una propiedad de Context, que contiene valores que se envían junto con todos los datos de telemetría. Normalmente, se establecen mediante los módulos de telemetría estándar, pero también los puede establecer usted mismo. Por ejemplo: 

    telemetry.Context.Operation.Name = "MyOperationName";

Si establece cualquiera de estos valores manualmente, considere la posibilidad de quitar la línea pertinente de [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), de modo que no se confundan sus valores con los valores estándar.

* **Component**: la aplicación y su versión
* **Device**: datos sobre el dispositivo donde se ejecuta la aplicación. (En aplicaciones web, se trata del servidor o el dispositivo de cliente desde el que se envía la telemetría).
* **InstrumentationKey**: el recurso de Application Insights en Azure donde aparece la telemetría. Normalmente, se selecciona de ApplicationInsights.config.
* **Location**: la ubicación geográfica del dispositivo.
* **Operation**: en aplicaciones web, es la solicitud HTTP actual. En otros tipos de aplicaciones, puede establecer este valor para agrupar los eventos juntos.
  * **Id**: valor generado que correlaciona distintos eventos, de modo que cuando usted inspeccione cualquier evento en Búsqueda de diagnóstico, puede encontrar elementos relacionados.
  * **Name**: un identificador, generalmente la dirección URL de la solicitud HTTP.
  * **SyntheticSource**: si no es un valor nulo ni está vacío, esta cadena indica que el origen de la solicitud se ha identificado como un robot o una prueba web. De forma predeterminada, se excluye de cálculos en el Explorador de métricas.
* **Properties**: propiedades que se envían con todos los datos de telemetría. Se pueden invalidar en llamadas de seguimiento* individuales.
* **Session**: la sesión del usuario. El identificador se establece en un valor generado, que cambia cuando el usuario lleva un tiempo sin estar activo.
* **User**: información del usuario.

## <a name="limits"></a>límites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Para evitar llegar al límite de velocidad de datos, utilice el [muestreo](app-insights-sampling.md).

Para determinar cuánto tiempo se conservan los datos, consulte el artículo sobre [retención de datos y privacidad](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Documentos de referencia
* [Referencia de ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referencia de Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referencia de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK de Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK de iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Código del SDK
* [SDK básico de ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Paquetes de Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK de Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK de Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas las plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Preguntas
* *¿Qué excepciones pueden iniciar las llamadas de seguimiento_()?*

    Ninguno. No es necesario agruparlas en cláusulas try-catch. Si el SDK encuentra problemas, registrará los mensajes en la salida de la consola de depuración, y, si los mensajes pasan, en la Búsqueda de diagnóstico.
* *¿Hay una API de REST para obtener datos desde el portal?*

    Sí, la [API de acceso a datos](https://dev.applicationinsights.io/). Otras maneras de extraer datos son [exportar desde Analytics a Power BI](app-insights-export-power-bi.md) y la [exportación continua](app-insights-export-telemetry.md).

## <a name="next"></a>Pasos siguientes
* [Búsqueda de eventos y registros](app-insights-diagnostic-search.md)

* [Solución de problemas](app-insights-troubleshoot-faq.md)


