---
title: Filtrado y preprocesamiento en el SDK de Azure Application Insights | Microsoft Docs
description: "Escriba procesadores e inicializadores de telemetría para que el SDK filtre o agregue propiedades a los datos antes de enviar la telemetría al portal de Application Insights."
services: application-insights
documentationcenter: 
author: beckylino
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: borooji;mbullwin
ms.openlocfilehash: 3f621010c1c36445ad35d81d96a2e5aefc46b10c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtro y preprocesamiento de la telemetría en el SDK de Application Insights


Puede escribir y configurar complementos para el SDK de Application Insights con el fin de personalizar cómo se captura y se procesa la telemetría antes de enviarla al servicio de Application Insights.

* [muestreo](app-insights-sampling.md) reduce el volumen de la telemetría sin que ello influya en las estadísticas. Mantiene juntos los puntos de datos relacionados para que pueda navegar entre ellos a la hora de diagnosticar un problema. En el portal, se multiplican los recuentos totales para compensar el muestreo.
* El filtrado con procesadores de telemetría [para ASP.NET](#filtering) o [Java](app-insights-java-filter-telemetry.md) permite seleccionar o modificar la telemetría en el SDK antes de enviarla al servidor. Por ejemplo, para reducir el volumen de la telemetría puede excluir las solicitudes de robots. Sin embargo, el filtro constituye un enfoque más básico que el muestreo para reducir el tráfico. Permite ejercer más control sobre lo que se transmite, pero debe tener en cuenta que se verán afectadas las estadísticas: por ejemplo, si filtra todas las solicitudes correctas.
* [inicializadores de telemetría agregan propiedades](#add-properties) a cualquier telemetría enviada desde la aplicación, incluida la de los módulos estándar. Por ejemplo, puede agregar valores calculados o números de versión para filtrar los datos en el portal.
* [API del SDK](app-insights-api-custom-events-metrics.md) se usa para enviar métricas y eventos personalizados.

Antes de comenzar:

* Instale el [SDK de Application Insights para ASP.NET](app-insights-asp-net.md) o [SDK para Java](app-insights-java-get-started.md) en su aplicación.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrado: ITelemetryProcessor
Esta técnica le ofrece un control más directo sobre lo que se incluirá en la transmisión de telemetría o lo que se excluirá de ella. Se puede utilizar junto con el muestreo o por separado.

Para filtrar la telemetría, escriba un procesador de telemetría y regístrelo con el SDK. Toda la telemetría pasa por el procesador. Puede optar por no incluirlo en la transmisión o por agregar propiedades. Esto incluye la telemetría de los módulos estándar como el recopilador de solicitudes HTTP y el recopilador de dependencia, así como la telemetría que haya escrito. Por ejemplo, puede filtrar para dejar fuera la telemetría acerca de las solicitudes de robots o las llamadas de dependencia correctas.

> [!WARNING]
> El filtrado de la telemetría enviada desde el SDK usando procesadores puede sesgar las estadísticas que se ven en el portal, y dificultar el seguimiento de elementos relacionados.
>
> En su lugar, puede efectuar un [muestreo](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Creación de un procesador de telemetría (C#)
1. Compruebe que la versión del SDK de Application Insights de su proyecto es la versión 2.0.0 o posterior. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones de Visual Studio y elija Administrar paquetes de NuGet. En el Administrador de paquetes NuGet, compruebe Microsoft.ApplicationInsights.Web.
2. Para crear un filtro, implemente ITelemetryProcessor. Se trata de otro punto de extensibilidad como el módulo de telemetría, el inicializador de telemetría y el canal de telemetría.

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
1. Inserte esto en ApplicationInsights.config:

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

> [!WARNING]
> Tenga cuidado para que el nombre de tipo y los nombres de propiedad del archivo .config coincidan con los nombres de clase y propiedad del código. Si el archivo .config hace referencia a un tipo o propiedad que no existe, el SDK puede producir un error de forma silenciosa al enviar cualquier telemetría.
>
>

**Alternativamente,** se puede inicializar el filtro en el código. En una clase de inicialización adecuada (por ejemplo AppStart de Global.asax.cs) inserte el procesador en la cadena:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Los TelemetryClients creados a partir de este punto usarán sus procesadores.

El siguiente código indica cómo agregar un inicializador de telemetría en ASP.NET Core.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var initializer = new SuccessfulDependencyFilter();
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();
    configuration.TelemetryInitializers.Add(initializer);
}
```

### <a name="example-filters"></a>Filtros de ejemplo
#### <a name="synthetic-requests"></a>Solicitudes sintéticas
Filtrar las pruebas web y robots. Aunque el Explorador de métricas proporciona la opción para filtrar y dejar fuera los orígenes sintéticos, esta opción reduce el tráfico filtrándolos en el SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Error de autenticación
Filtrar solicitudes con una respuesta "401".

```csharp

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar las llamadas de dependencia rápida y remota
Si solo desea diagnosticar las llamadas que son lentas, descarte las rápidas.

> [!NOTE]
> Esto sesgará las estadísticas que se ve en el portal. El gráfico de dependencias será como si las llamadas de dependencia fuesen todos errores.
>
>

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnóstico de problemas de dependencia
[este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) se describe un proyecto para diagnosticar problemas de dependencia enviando automáticamente pings regulares a las dependencias.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Agregar propiedades: ITelemetryInitializer
Use inicializadores de telemetría para definir las propiedades globales que se envían con toda la telemetría; y para invalidar el comportamiento seleccionado de los módulos de telemetría estándar.

Por ejemplo, para el paquete Application Insights para web recopila telemetría acerca de las solicitudes HTTP. De forma predeterminada, marca como errónea cualquier solicitud con un código de respuesta >= 400. Pero si desea tratar 400 como correcto, puede proporcionar un inicializador de telemetría que establezca la propiedad Éxito.

Si se proporciona un inicializador de telemetría, se llama cada vez que se llama a cualquiera de los métodos Track*(). Esto incluye los métodos llamados por los módulos de telemetría estándar. Por convención, estos módulos no establecen ninguna propiedad que ya haya sido establecida por un inicializador.

**Defina su inicializador**

*C#*

```csharp

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

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Obtenga más información de este ejemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetría de JavaScript
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

Para obtener un resumen de las propiedades no personalizadas disponibles en telemetryItem, consulte [Modelo de exportación de datos de Application Insights](app-insights-export-data-model.md).

Puede agregar tantos inicializadores como desee.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer
¿Cuál es la diferencia entre los procesadores de telemetría y los inicializadores de telemetría?

* Coinciden en algunas funciones: ambos se pueden utilizar para agregar propiedades a una telemetría.
* Siempre se ejecutan los objetos TelemetryInitializer antes que los objetos TelemetryProcessor.
* Los objetos TelemetryProcessor permiten reemplazar o descartar por completo un elemento de telemetría.
* Los objetos TelemetryProcessor no procesan telemetría de contador de rendimiento.

## <a name="troubleshooting-applicationinsightsconfig"></a>Solución de problemas de ApplicationInsights.config.
* Compruebe que el nombre del tipo completo y el nombre del ensamblado sean correctos.
* Compruebe que el archivo applicationinsights.config esté en el directorio de salida y que contenga todos los cambios recientes.

## <a name="reference-docs"></a>Documentos de referencia
* [Información general acerca de la API](app-insights-api-custom-events-metrics.md)
* [Referencia de ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código del SDK
* [SDK básico de ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK de ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Pasos siguientes
* [Búsqueda de eventos y registros](app-insights-diagnostic-search.md)
* [Muestreo](app-insights-sampling.md)
* [Solución de problemas](app-insights-troubleshoot-faq.md)
