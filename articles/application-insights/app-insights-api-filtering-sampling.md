<properties 
	pageTitle="Filtro y preprocesamiento en el SDK de Application Insights | Microsoft Azure" 
	description="Escriba procesadores e inicializadores de telemetría para que el SDK filtre o agregue propiedades a los datos antes de enviar la telemetría al portal de Application Insights." 
	services="application-insights"
    documentationCenter="" 
	authors="beckylino" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="borooji"/>

# Filtro y preprocesamiento de la telemetría en el SDK de Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Puede escribir y configurar complementos para el SDK de Application Insights con el fin de personalizar cómo se captura y se procesa la telemetría antes de enviarla al servicio de Application Insights.

Actualmente, estas características están disponibles para el SDK de ASP.NET.

* El [muestreo](app-insights-sampling.md) reduce el volumen de la telemetría sin que ello influya en las estadísticas. Mantiene juntos los puntos de datos relacionados para que pueda navegar entre ellos a la hora de diagnosticar un problema. En el portal, se multiplican los recuentos totales para compensar el muestreo.
* El [filtro con procesadores de telemetría](#filtering) permite seleccionar o modificar la telemetría en el SDK antes de enviarla al servidor. Por ejemplo, para reducir el volumen de la telemetría puede excluir las solicitudes de robots. Sin embargo, el filtro constituye un enfoque más básico que el muestreo para reducir el tráfico. Permite ejercer más control sobre lo que se transmite, pero debe tener en cuenta que se verán afectadas las estadísticas: por ejemplo, si filtra todas las solicitudes correctas.
* Los [inicializadores de telemetría agregan propiedades](#add-properties) a cualquier telemetría enviada desde la aplicación, incluida la de los módulos estándar. Por ejemplo, puede agregar valores calculados o números de versión para filtrar los datos en el portal.
* La [API del SDK](app-insights-api-custom-events-metrics.md) se usa para enviar métricas y eventos personalizados.


Antes de comenzar:

* Instale el [SDK de Application Insights para la versión 2 de ASP.NET](app-insights-asp-net.md) en su aplicación.


<a name="filtering"></a>
## Filtrado: ITelemetryProcessor

Esta técnica le ofrece un control más directo sobre lo que se incluirá en la transmisión de telemetría o lo que se excluirá de ella. Se puede utilizar junto con el muestreo o por separado.

Para filtrar la telemetría, escriba un procesador de telemetría y regístrelo con el SDK. Toda la telemetría pasa por el procesador. Puede optar por no incluirlo en la transmisión o por agregar propiedades. Esto incluye la telemetría de los módulos estándar como el recopilador de solicitudes HTTP y el recopilador de dependencia, así como la telemetría que haya escrito. Por ejemplo, puede filtrar para dejar fuera la telemetría acerca de las solicitudes de robots o las llamadas de dependencia correctas.

> [AZURE.WARNING] El filtrado de la telemetría enviada desde el SDK usando procesadores puede sesgar las estadísticas que se ven en el portal, y dificultar el seguimiento de elementos relacionados.
> 
> En su lugar, puede efectuar un [muestreo](app-insights-sampling.md).

### Crear un procesador de telemetría

1. Compruebe que la versión del SDK de Application Insights de su proyecto es la versión 2.0.0 o posterior. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones de Visual Studio y elija Administrar paquetes de NuGet. En el Administrador de paquetes NuGet, compruebe Microsoft.ApplicationInsights.Web.

1. Para crear un filtro, implemente ITelemetryProcessor. Se trata de otro punto de extensibilidad como el módulo de telemetría, el inicializador de telemetría y el canal de telemetría.

    Observe que los procesadores de telemetría forman una cadena de procesamiento. Al crear instancias de un procesador de telemetría, se pasa un vínculo al procesador siguiente en la cadena. Cuando un punto de datos de telemetría se pasa al método de proceso, realiza su trabajo y, a continuación, llama al procesador de telemetría siguiente de la cadena.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Inserte esto en ApplicationInsights.config:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Se trata de la misma sección donde inicializa un filtro de muestreo).

Puede pasar valores de cadena desde el archivo .config proporcionando propiedades con nombre públicas en la clase.

> [AZURE.WARNING] Tenga cuidado para que el nombre de tipo y los nombres de propiedad del archivo .config coincidan con los nombres de clase y propiedad del código. Si el archivo .config hace referencia a un tipo o propiedad que no existe, el SDK puede producir un error de forma silenciosa al enviar cualquier telemetría.

 
**Alternativamente,** se puede inicializar el filtro en el código. En una clase de inicialización adecuada (por ejemplo AppStart de Global.asax.cs) inserte el procesador en la cadena:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Los TelemetryClients creados a partir de este punto usarán sus procesadores.

### Filtros de ejemplo

#### Solicitudes sintéticas

Filtrar las pruebas web y robots. Aunque el Explorador de métricas proporciona la opción para filtrar y dejar fuera los orígenes sintéticos, esta opción reduce el tráfico filtrándolos en el SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Error de autenticación

Filtrar solicitudes con una respuesta "401".

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### Filtrar las llamadas de dependencia rápida y remota

Si solo desea diagnosticar las llamadas que son lentas, descarte las rápidas.

> [AZURE.NOTE] Esto sesgará las estadísticas que se ve en el portal. El gráfico de dependencias será como si las llamadas de dependencia fuesen todos errores.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### Diagnóstico de problemas de dependencia

En [este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) se describe un proyecto para diagnosticar problemas de dependencia enviando automáticamente pings regulares a las dependencias.


<a name="add-properties"></a>
## Agregar propiedades: ITelemetryInitializer

Use inicializadores de telemetría para definir las propiedades globales que se envían con toda la telemetría; y para invalidar el comportamiento seleccionado de los módulos de telemetría estándar.

Por ejemplo, para el paquete Application Insights para web recopila telemetría acerca de las solicitudes HTTP. De forma predeterminada, marca como errónea cualquier solicitud con un código de respuesta >= 400. Pero si desea tratar 400 como correcto, puede proporcionar un inicializador de telemetría que establezca la propiedad Éxito.

Si se proporciona un inicializador de telemetría, se llama cada vez que se llama a cualquiera de los métodos Track*(). Esto incluye los métodos llamados por los módulos de telemetría estándar. Por convención, estos módulos no establecen ninguna propiedad que ya haya sido establecida por un inicializador.

**Defina su inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Cargue su inicializador**

En ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Alternativamente,* se pueden crear instancias del inicializador en el código, por ejemplo en Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Obtenga más información de este ejemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### Inicializadores de telemetría de JavaScript

*JavaScript*

Inserte un inicializador de telemetría inmediatamente después del código de inicialización que obtuvo del portal:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obtener un resumen de las propiedades no personalizadas disponibles en telemetryItem, consulte el [modelo de datos](app-insights-export-data-model.md#lttelemetrytypegt).

Puede agregar tantos inicializadores como desee.


## ITelemetryProcessor e ITelemetryInitializer

¿Cuál es la diferencia entre los procesadores de telemetría y los inicializadores de telemetría?

* Coinciden en algunas funciones: ambos se pueden utilizar para agregar propiedades a una telemetría.
* Siempre se ejecutan los objetos TelemetryInitializer antes que los objetos TelemetryProcessor.
* Los objetos TelemetryProcessor permiten reemplazar o descartar por completo un elemento de telemetría.
* Los objetos TelemetryProcessor no procesan telemetría de contador de rendimiento.



## Canal de persistencia 

Si la aplicación se ejecuta en un lugar en el que la conexión a Internet no está siempre disponible o es lenta, considere el uso del canal de persistencia en lugar del canal en memoria predeterminado.

El canal en memoria predeterminado pierde cualquier telemetría no enviada antes de cerrar la aplicación. Aunque puede usar `Flush()` para intentar enviar los datos que quedan en el búfer, seguirá perdiendo datos si no hay una ninguna conexión a Internet, o si la aplicación se cierra antes de completarse la transmisión.

Por el contrario, el canal de persistencia almacena en búfer la telemetría en un archivo, antes de enviarla al portal. `Flush()` garantiza que los datos se almacenan en el archivo. Si no se envía ningún dato en el momento en que se cierra la aplicación, permanecerá en el archivo. Cuando se reinicia la aplicación, se enviarán los datos si hay una conexión a Internet. Los datos se acumularán en el archivo el tiempo que sea necesario hasta que esté disponible una conexión.

### Uso del canal de persistencia

1. Importe el paquete de NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Incluya este código en la aplicación, en una ubicación de inicialización adecuada:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Use `telemetryClient.Flush()` antes de que se cierre la aplicación, para asegurarse de que se envíen los datos al portal o se guarden en el archivo.

    Tenga en cuenta que Flush() es sincrónico para el canal de persistencia, pero asincrónico para otros canales.

 
El canal de persistencia está optimizado para escenarios de dispositivos, en los que el número de eventos generados por la aplicación es relativamente pequeño y la conexión es a menudo poco confiable. Este canal escribirá eventos en el disco en un almacenamiento confiable en primer lugar y, a continuación, intentará enviarlo.

#### Ejemplo

Supongamos que desea supervisar las excepciones no controladas. Se suscribe al evento `UnhandledException`. En la devolución de llamada, incluye una llamada a Flush para asegurarse de que la telemetría será persistente.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Cuando se cierre la aplicación, verá un archivo en `%LocalAppData%\Microsoft\ApplicationInsights`, que contiene los eventos comprimidos.
 
La próxima vez que inicie esta aplicación, el canal seleccionará este archivo y proporcionará la telemetría a Application Insights si es posible.

#### Ejemplo de prueba

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


El código del canal de persistencia se encuentra en [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel).


## Documentos de referencia

* [Información general acerca de la API](app-insights-api-custom-events-metrics.md)

* [Referencia de ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Código del SDK

* [SDK básico de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Pasos siguientes


* [Búsqueda de eventos y registros][diagnostic]
* [Muestreo](app-insights-sampling.md)
* [Solución de problemas][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0907_2016-->