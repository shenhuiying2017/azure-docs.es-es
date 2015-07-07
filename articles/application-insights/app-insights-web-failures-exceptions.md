<properties 
	pageTitle="Detección y diagnóstico de errores y excepciones en aplicaciones web" 
	description="Detección y diagnóstico de errores y excepciones en aplicaciones web" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="awills"/>
 
# Diagnóstico de errores y excepciones en aplicaciones web con Application Insights

[Visual Studio Application Insights][start] es una herramienta de gran eficacia para detectar y diagnosticar errores en las aplicaciones. Aquí nos centraremos en las aplicaciones web (aunque Application Insights también puede aplicarse a una gran [variedad de otras plataformas][platforms]).

## Configuración de la aplicación con Application Insights

Agregue el SDK para el proyecto de aplicación. Al lanzar y ejecutar la aplicación, el SDK enviará datos de telemetría sobre el rendimiento de esta al portal de Application Insights.

* [Agregar Application Insights a aplicaciones ASP.NET][greenbrown]
* [Agregar Application Insights a aplicaciones Java][java]

Si las páginas web tienen scripts importantes, puede que también quiera * [Agregar Application Insights a páginas web][track].


## Búsqueda de diagnóstico



Abra Búsqueda de diagnóstico para ver los datos de telemetría que el SDK envía automáticamente.

![](./media/app-insights-web-failures-exceptions/appinsights-45diagnostic.png)

![](./media/app-insights-web-failures-exceptions/appinsights-31search.png)

Los detalles varían de un tipo de aplicación a otro. Puede hacer clic en cualquier parte de un evento individual para obtener más detalles.

##<a name="events"></a>Eventos personalizados

Los eventos personalizados se muestran tanto en la [Búsqueda de diagnóstico][diagnostic] como en el [Explorador de métricas][metrics]. Puede enviarlos desde dispositivos, páginas web y aplicaciones de servidor. Se pueden usar con fines de diagnóstico y para [entender los patrones de uso][track].

Un evento personalizado tiene un nombre y también puede incluir propiedades por las que se puede filtrar, junto con medidas numéricas.

JavaScript en el cliente

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# en el servidor

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


VB en el servidor

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### Ejecución de la aplicación y visualización de los resultados

Abra Búsqueda de diagnóstico.

Seleccione Evento personalizado y elija un nombre de evento concreto.

![](./media/app-insights-web-failures-exceptions/appinsights-332filterCustom.png)


Especifique un término de búsqueda en un valor de propiedad para filtrar los datos aún más.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-5.png)

Profundice en un evento individual para ver sus propiedades detalladas.

![](./media/app-insights-web-failures-exceptions/appinsights-23-customevents-4.png)



##<a name="trace"></a> Telemetría de seguimiento

La telemetría de seguimiento es código que el usuario inserta de forma específica para crear registros de diagnóstico.

Por ejemplo, puede insertar llamadas como esta:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Instalación de un adaptador para el marco de registro

También puede buscar los registros generados con un marco de registro: log4Net, NLog o System.Diagnostics.Trace.

1. Si planea usar log4Net o NLog, instálelo en su proyecto. 
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Seleccione En línea > Todo, seleccione **Incluir versión preliminar** y busque "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/app-insights-web-failures-exceptions/appinsights-36nuget.png)

4. Seleccione el paquete adecuado entre los siguientes:
  + Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

#### <a name="pepper"></a>Inserción de llamadas de registro de diagnóstico

Si usa System.Diagnostics.Trace, una llamada típica sería:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");

Ejecute la aplicación en modo de depuración o impleméntela.

Los mensajes aparecerán en Búsqueda de diagnóstico cuando se selecciona el filtro de seguimiento.

### <a name="exceptions"></a>Excepciones

La obtención de informes de excepciones en Application Insights supone una experiencia de gran eficacia, sobre todo porque permite navegar entre las solicitudes con error y las excepciones y leer la pila de excepciones.

Puede escribir código para enviar datos de telemetría de las excepciones:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Los parámetros de las propiedades y las medidas son opcionales, pero son útiles para filtrar y agregar información adicional. Por ejemplo, si tiene una aplicación que se puede ejecutar varios juegos, podría buscar todos los informes de excepción relacionados con un juego en particular. Puede agregar tantos elementos como desee para cada diccionario.

#### Visualización de excepciones

En la hoja de información general se muestra un resumen de las excepciones y puede hacer clic en cualquier parte de este para ver más detalles. Por ejemplo:


![](./media/app-insights-web-failures-exceptions/appinsights-039-1exceptions.png)

Haga clic en cualquier tipo de excepción para ver instancias específicas:

![](./media/app-insights-web-failures-exceptions/appinsights-333facets.png)

También puede abrir la Búsqueda de diagnóstico directamente, filtrar por las excepciones y elegir el tipo de excepción que desea ver.

### Notificación de excepciones no controladas

Application Insights notifica las excepciones no controladas siempre que sea posible, ya sea de los dispositivos, los [exploradores web][usage] o los servidores web e independientemente de que estén instrumentadas por el [Monitor de estado][redfield] o el [SDK de Application Insights][greenbrown].

> [AZURE.NOTE]En un explorador web, si incluye archivos de script de CDN o de otros dominios, asegúrese de que la etiqueta de script tiene el atributo ```crossorigin="anonymous"``` y el servidor envía encabezados CORS para obtener un seguimiento de la pila y los detalles de las excepciones de javascript no controladas de estos recursos.

Sin embargo, no siempre puede realizar esta acción, ya que .NET Framework captura las excepciones. Por lo tanto, para asegurarse de ver todas las excepciones, tendrá que escribir un pequeño controlador de excepciones. El procedimiento más adecuado en cada caso varía en función de la tecnología. Consulte [este blog](http://blogs.msdn.com/b/visualstudioalm/archive/2014/12/12/application-insights-exception-telemetry.aspx) para obtener más información.

### Correlación con una compilación

Cuando se leen registros de diagnóstico, es probable que el código fuente haya cambiado desde que se implementó el código activo.

Por lo tanto, resulta útil incluir información de la compilación (como la dirección URL de la versión actual) en una propiedad junto con cada excepción o seguimiento.

En lugar de agregar la propiedad por separado a cada llamada de excepción, puede establecer la información en el contexto predeterminado.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

En el inicializador de la aplicación como Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> Solicitudes de servidor web

La telemetría de las solicitudes se envía automáticamente al [instalar el monitor de estado en el servidor web][redfield] o al [agregar Application Insights al proyecto web][greenbrown]. También se inserta automáticamente en los gráficos de tiempo de solicitud y respuesta del explorador de métricas y en la página de información general.

Si desea enviar eventos adicionales, puede usar la API de TrackRequest().

## <a name="questions"></a>Preguntas y respuestas

### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".

Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente. Esto debería solucionarlo.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

## <a name="add"></a>Pasos siguientes

* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[platforms]: app-insights-platforms.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=62-->