---
title: "API de Application Insights para eventos y métricas personalizados | Microsoft Docs"
description: "Inserte unas cuantas líneas de código en su aplicación de dispositivo o de escritorio, página o servicio web, para realizar el seguimiento del uso y diagnosticar problemas."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 64632e58330b8212be24b98f861a3a4f358e72df
ms.lasthandoff: 04/12/2017


---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API de Application Insights para eventos y métricas personalizados


Inserte unas cuantas líneas de código en la aplicación para averiguar qué uso hacen de ella los usuarios o para ayudarle a diagnosticar problemas. Puede enviar datos de telemetría desde aplicaciones de escritorio y de dispositivo y desde clientes y servidores web. Use la API de telemetría principal de [Azure Application Insights](app-insights-overview.md) para enviar métricas y eventos personalizados, así como sus propias versiones de telemetría estándar. Esta API es la misma que usan los recopiladores de datos estándar de Application Insights.

## <a name="api-summary"></a>API summary
La API es uniforme en todas las plataformas, excepto por algunas pequeñas variaciones.

| Método | Usado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, pantallas, hojas o formularios. |
| [`TrackEvent`](#trackevent) |Acciones de usuario y otros eventos. Se usa para realizar el seguimiento del comportamiento de los usuarios o para supervisar el rendimiento. |
| [`TrackMetric`](#send-metrics) |Las medidas de rendimiento, como las longitudes de cola, no están relacionadas con eventos específicos. |
| [`TrackException`](#trackexception) |Excepciones de registro para diagnóstico. Permite realizar el seguimiento del lugar donde se producen en relación con otros eventos y examinar los seguimientos de pila. |
| [`TrackRequest`](#trackrequest) |Registro de la frecuencia y duración de las solicitudes de servidor para el análisis de rendimiento. |
| [`TrackTrace`](#tracktrace) |Mensajes de registro de diagnóstico. También puede capturar registros de terceros. |
| [`TrackDependency`](#trackdependency) |Registro de la duración y frecuencia de las llamadas a componentes externos de los que depende la aplicación. |

Puede [adjuntar propiedades y métricas](#properties) a la mayoría de estas llamadas de telemetría.

## <a name="prep"></a>Antes de comenzar
Si no ha hecho esto aún:

* Agregue el SDK de Application Insights a su proyecto:

  * [Proyecto de ASP.NET](app-insights-asp-net.md)
  * [Proyecto de Java](app-insights-java-get-started.md)
  * [JavaScript en cada página web](app-insights-javascript.md) 
* En el código de servidor web o de dispositivo, incluya:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="constructing-a-telemetryclient-instance"></a>Construcción de una instancia de TelemetryClient
Construcción de una instancia de TelemetryClient (excepto en JavaScript en páginas web):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient es seguro para subprocesos.

Se recomienda que use una instancia de TelemetryClient para cada módulo de la aplicación. Por ejemplo, puede tener una instancia de TelemetryClient en el servicio web para informar de las solicitudes HTTP entrantes y otra en una clase de middleware para notificar eventos de la lógica de negocios. Puede establecer propiedades tales como `TelemetryClient.Context.User.Id` para realizar el seguimiento de los usuarios y de las sesiones, o bien `TelemetryClient.Context.Device.Id` para identificar el equipo. Esta información se adjunta a todos los eventos enviados por la instancia.

## <a name="trackevent"></a>TrackEvent
En Application Insights, un *evento personalizado* es un punto de datos que se puede mostrar en el [Explorador de métricas](app-insights-metrics-explorer.md) como recuento agregado, y como repeticiones individuales en [Búsqueda de diagnóstico](app-insights-diagnostic-search.md). (No está relacionado con MVC ni con "eventos" de otro marco).

Inserte llamadas a TrackEvent en el código para contabilizar la frecuencia con la que los usuarios utilizan una determinada característica, la frecuencia con la que logran unos determinados objetivos o la frecuencia con la que cometen determinados tipos de errores.

Por ejemplo, en una aplicación de juego, envíe un evento cada vez que un usuario gane el juego:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Visualización de eventos en Azure Portal
Para ver un recuento de los eventos, abra una hoja [Explorador de métricas](app-insights-metrics-explorer.md), agregue un nuevo gráfico y seleccione **Eventos**.  

![Visualización de un recuento de eventos personalizados](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar los recuentos de eventos diferentes, establezca el tipo de gráfico en **Cuadrícula** y el grupo por nombre de evento:

![Establecimiento del tipo de gráfico y agrupación](./media/app-insights-api-custom-events-metrics/07-grid.png)

En la cuadrícula, haga clic en un nombre de evento para ver todas las repeticiones individuales de ese evento. Haga clic en cualquier repetición para ver más información.

![Obtenga detalles de los eventos.](./media/app-insights-api-custom-events-metrics/03-instances.png)

Para centrarse en eventos concretos de la Búsqueda o el Explorador de métricas, establezca el filtro de la hoja en los nombres de eventos que le interesan:

![Abre filtros, expanda el nombre del evento y seleccione uno o varios valores.](./media/app-insights-api-custom-events-metrics/06-filter.png)


## <a name="send-metrics"></a>Envío de métricas

Application Insights puede crear gráficos de métricas que no estén conectadas a eventos concretos. Por ejemplo, puede supervisar una longitud de cola a intervalos regulares. En el caso de las métricas, las mediciones individuales tienen menos interés que las variaciones y tendencias; por tanto, los gráficos estadísticos resultan útiles.

Existen dos formas de enviar métricas:

* **MetricManager** se recomienda como forma cómoda de enviar métricas al mismo tiempo que se reduce el ancho de banda. Agrega las métricas en la aplicación y envía las estadísticas agregadas al portal a intervalos de un minuto. MetricManager está disponible en la versión 2.4 del SDK de Application Insights para ASP.NET.
* **TrackMetric** envía estadísticas de las métricas al portal. Puede enviar valores de métrica individuales o realizar su propia agregación y usar TrackMetric para enviar las estadísticas.

### <a name="metricmanager"></a>MetricManager

(Application Insights para ASP.NET v2.4.0+)

Cree una instancia de MetricManager y úsela como factoría de métricas:

*C#*
```C#
    // Initially:
    var manager = new Microsoft.ApplicationInsights.Extensibility.MetricManager(telemetryClient);

    // For each metric that you want to use:
    var metric1 = mgr.CreateMetric("m1", dimensions);

    // Each time you want to record a measurement:
    metric1.Track(value);

```

`dimensions` es un diccionario de cadenas opcional. Úselo si desea asociar [propiedades](#properties) a la métrica para que también puede segmentar por otros valores de propiedad. 

### <a name="trackmetric"></a>TrackMetric

TrackMetric es el método básico para el envío de métricas agregadas. 

Para enviar un único valor de métrica:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#, Java*

```C#
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

Sin embargo, se recomienda agregar las métricas antes de enviarlas desde su aplicación, para reducir el ancho de banda.
Si está usando la versión más reciente del SDK para ASP.NET, puede usar [`MetricManager`](#metricmanager) para hacerlo. De lo contrario, este es un ejemplo de código de agregación:

*C#*

```C#
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    class MetricAggregator
    {
        private List<double> measurements = new List<double>();
        private string name;
        private TelemetryClient telemetryClient;
        private BackgroundWorker thread;
        private Boolean stop = false;
        public void TrackMetric (double value)
        {
            lock (this)
            {
                measurements.Add(value);
            }
        }
        public MetricTelemetry Aggregate()
        {
            lock (this)
            {
                var sample = new MetricTelemetry();
                sample.Name = "metric name";
                sample.Count = measurements.Count;
                sample.Max = measurements.Max();
                sample.Min = measurements.Min();
                sample.Sum = measurements.Sum();
                var mean = sample.Sum / measurements.Count;
                sample.StandardDeviation = Math.Sqrt(measurements.Sum(v => { var diff = v - mean; return diff * diff; }) / measurements.Count);
                sample.Timestamp = DateTime.Now;
                measurements.Clear();
                return sample;
            }
        }
        public MetricAggregator(string Name)
        {
            name = Name;
            thread = new BackgroundWorker();
            thread.DoWork += async (o, e) => {
                while (!stop)
                {
                    await Task.Delay(60000);
                    telemetryClient.TrackMetric(this.Aggregate());
                }
            };
            thread.RunWorkerAsync();
        }
    }
```
### <a name="custom-metrics-in-metrics-explorer"></a>Métricas personalizadas en el Explorador de métricas

Para ver los resultados, abra el Explorador de métricas y agregue un gráfico nuevo. Edite el gráfico para que muestre la métrica.

> [!NOTE]
> La métrica personalizada puede tardar varios minutos en aparecer en la lista de métricas disponibles.
>

![Agregue un gráfico nuevo o seleccione un gráfico y, en Personalizada, seleccione su métrica.](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas en Analytics

La telemetría está disponible en la tabla customMetrics. Cada fila representa una llamada a trackMetric() en la aplicación. Por lo tanto, si ha usado MetricManager o su propio código de agregación, cada fila no representará una sola medida. 

* `valueSum`: es la suma de las medidas. Para obtener el valor medio, divídalo por `valueCount`.
* `valueCount`: el número de medidas que se agregaron en esta llamada a trackMetric.



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

## <a name="trackrequest"></a>TrackRequest
El SDK del servidor usa TrackRequest para registrar las solicitudes de HTTP.

También puede llamarlo usted mismo si quiere simular solicitudes en un contexto en el que no se está ejecutando el módulo de servicio web.

Sin embargo, lo que se recomienda para enviar telemetría de solicitudes es que la solicitud actúe como <a href="#operation-context">contexto de operación</a>.

## <a name="operation-context"></a>Contexto de operación
Se pueden asociar elementos de telemetría adjuntándoles un identificador de operación común. El módulo de seguimiento de solicitud estándar realiza esta operación para excepciones y otros eventos enviados al procesar una solicitud HTTP. En [Búsqueda](app-insights-diagnostic-search.md) y [Análisis](app-insights-analytics.md), puede utilizar el identificador para encontrar fácilmente los eventos asociados a la solicitud.

La manera más fácil de establecer el identificador es definir un contexto de operación mediante este patrón:

*C#*

```C#

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item--for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.
```

Junto con la configuración de un contexto de la operación, `StartOperation` crea un elemento de telemetría del tipo que especifique. Envía el elemento de telemetría al eliminar la operación, o si llama explícitamente a `StopOperation`. Si usa `RequestTelemetry` como tipo de telemetría, su duración se establece en el intervalo cronometrado entre el inicio y la detención.

Los contextos de operación no pueden estar anidados. Si ya existe un contexto de operación, su identificador está asociado a todos los elementos contenidos, incluido el elemento que se ha creado con `StartOperation`.

En la Búsqueda, el contexto de la operación se utiliza para crear la lista de **Elementos relacionados**:

![Elementos relacionados](./media/app-insights-api-custom-events-metrics/21.png)

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

## <a name="tracktrace"></a>TrackTrace
Use TrackTrace para ayudar a diagnosticar problemas mediante el envío de una ''ruta de exploración'' a Application Insights. Puede enviar fragmentos de datos de diagnóstico e inspeccionarlos en [Búsqueda de diagnóstico](app-insights-diagnostic-search.md).

Los [adaptadores de registro](app-insights-asp-net-trace-logs.md) usan esta API para enviar registros de terceros al portal.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


Puede buscar en el contenido del mensaje, pero (a diferencia de los valores de propiedad) no puede filtrar por él.

El límite de tamaño en `message` es mucho mayor que el límite en propiedades.
Una ventaja de TrackTrace es que puede colocar datos relativamente largos en el mensaje. Por ejemplo, aquí puede codificar datos POST.  

Además, puede agregar un nivel de gravedad al mensaje. Y, al igual que con otra telemetría, puede agregar valores de propiedad para ayudar a filtrar o buscar distintos conjuntos de seguimientos. Por ejemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

En [Búsqueda](app-insights-diagnostic-search.md), puede filtrar fácilmente todos los mensajes de un determinado nivel de gravedad relativos a una determinada base de datos.

## <a name="trackdependency"></a>TrackDependency
Utilice la llamada de TrackDependency para realizar un seguimiento de los tiempos de respuesta y las tasas de éxito de las llamadas a un fragmento de código externo. Los resultados se muestran en los gráficos de dependencia del portal.

```C#

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

Recuerde que los SDK del servidor incluyen un [módulo de dependencia](app-insights-asp-net-dependencies.md) que detecta y realiza automáticamente el seguimiento de ciertas llamadas de dependencia; por ejemplo, a bases de datos y API de REST. Debe instalar un agente en el servidor para que el módulo funcione. Utilizará esta llamada si desea hacer un seguimiento de las llamadas no captadas por el seguimiento automatizado, o bien si no desea instalar el agente.

Para desactivar el módulo de seguimiento de dependencias estándar, edite [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) y elimine la referencia a `DependencyCollector.DependencyTrackingTelemetryModule`.

## <a name="flushing-data"></a>Datos de vaciado
Normalmente, el SDK envía datos en momentos elegidos para minimizar el impacto en el usuario. Sin embargo, en algunos casos puede que desee vaciar el búfer: por ejemplo, si usa el SDK en una aplicación que se apaga.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

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

El identificador de usuario también se establece en una cookie de sesión y se envía al servidor. Si está instalado el SDK del servidor, el identificador de usuario autenticado se enviará como parte de las propiedades de contexto tanto de la telemetría del cliente como del servidor, para que pueda filtrar y buscar en ella. A continuación, puede filtrar y buscar en ella.

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

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para *deshabilitar los recopiladores estándar seleccionados* (por ejemplo, contadores de rendimiento, solicitudes HTTP o dependencias), elimine o convierta en comentarios las líneas correspondientes en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Puede hacer esto, por ejemplo, si quiere enviar sus propios datos de TrackRequest.

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
* **InstrumentationKey**: el recurso de Application Insights en Azure donde aparecerá la telemetría. Normalmente, se selecciona de ApplicationInsights.config.
* **Location**: la ubicación geográfica del dispositivo.
* **Operation**: en aplicaciones web, es la solicitud HTTP actual. En otros tipos de aplicaciones, puede establecer este valor para agrupar los eventos juntos.
  * **Id**: valor generado que correlaciona distintos eventos, de modo que cuando usted inspeccione cualquier evento en Búsqueda de diagnóstico, puede encontrar elementos relacionados.
  * **Name**: un identificador, generalmente la dirección URL de la solicitud HTTP.
  * **SyntheticSource**: si no es un valor nulo ni está vacío, esta cadena indica que el origen de la solicitud se ha identificado como un robot o una prueba web. De forma predeterminada se excluirá de cálculos en el Explorador de métricas.
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
* [SDK básico de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Paquetes de Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [SDK de Java](https://github.com/Microsoft/ApplicationInsights-Java)
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




