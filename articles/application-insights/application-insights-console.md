---
title: Azure Application Insights para aplicaciones de consola | Microsoft Docs
description: Supervise la disponibilidad, el rendimiento y el uso de las aplicaciones web.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova; mbullwin
ms.openlocfilehash: 679a5d82fbede4d9c464e137d615fc1367522878
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights para aplicaciones de consola .NET
[Application Insights](app-insights-overview.md) permite supervisar la disponibilidad, el rendimiento y el uso de una aplicación web.

Necesita una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, Xbox Live u otros servicios en la nube de Microsoft. Si su equipo tiene una suscripción organizativa a Azure, el propietario puede agregarle a esta con la cuenta de Microsoft.

## <a name="getting-started"></a>Introducción

* En [Azure Portal](https://portal.azure.com), [cree un recurso de Application Insights](app-insights-create-new-resource.md). Para el tipo de aplicación, elija **General**.
* Realice una copia de la clave de instrumentación. Busque la clave en la lista desplegable **Essentials** del recurso que creó. 
* Instale el paquete [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) más reciente.
* Establezca la clave de instrumentación en el código antes de hacer el seguimiento de cualquier telemetría (o establezca la variable de entorno APPINSIGHTS_INSTRUMENTATIONKEY). Después de eso, debe ser capaz de hacer un seguimiento manual de la telemetría y verla en Azure Portal

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* Instale la versión más reciente de [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector), que hace automáticamente un seguimiento de HTTP, SQL o algunas llamadas de dependencias externas.

Puede inicializar y configurar Application Insights desde el código o con el archivo `ApplicationInsights.config`. Asegúrese de que la inicialización se realice tan pronto como sea posible. 

> [!NOTE]
> Las instrucciones referentes a **ApplicationInsights.config** solo son aplicables a las aplicaciones que tienen como destino .NET Standard, y no se aplican a las aplicaciones de .NET Core. 

### <a name="using-config-file"></a>Uso del archivo de configuración

De manera predeterminada, el SDK de Application Insights busca el archivo `ApplicationInsights.config` en el directorio de trabajo cuando se crea `TelemetryConfiguration`

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

También puede especificar la ruta de acceso al archivo de configuración.

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

Para más información, consulte la [referencia del archivo de configuración](app-insights-configuration-with-applicationinsights-config.md).

Puede obtener un ejemplo completo del archivo de configuración mediante la instalación de la versión más reciente del paquete [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). Esta es la configuración **mínima** para la colección de dependencias que es equivalente al ejemplo de código.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configuración de la colección de telemetría a partir del código

* Durante el inicio de la aplicación, cree y configure la instancia `DependencyTrackingTelemetryModule`, que debe ser singleton y se debe conservar por toda la duración de la aplicación.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Agregue inicializadores de telemetría comunes

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* Para la aplicación Windows de .NET Framework, también puede instalar e inicializar el módulo de recopilador Contador de rendimiento tal como se describe[aquí](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)

#### <a name="full-example"></a>Ejemplo completo

```csharp
static void Main(string[] args)
{
    TelemetryConfiguration configuration = TelemetryConfiguration.Active;

    configuration.InstrumentationKey = "removed";
    configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
    configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

    var telemetryClient = new TelemetryClient();
    using (InitializeDependencyTracking(configuration))
    {
        // run app...
        
        telemetryClient.TrackTrace("Hello World!");

        using (var httpClient = new HttpClient())
        {
            // Http dependency is automatically tracked!
            httpClient.GetAsync("https://microsoft.com").Wait();
        }

    }

    // before exit, flush the remaining data
    telemetryClient.Flush();
    
    // flush is not blocking so wait a bit
    Task.Delay(5000).Wait();

}

static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
{
    var module = new DependencyTrackingTelemetryModule();
    
    // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
    module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

    // enable known dependency tracking, note that in future versions, we will extend this list. 
    // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
    module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

    // initialize the module
    module.Initialize(configuration);

    return module;
}
```

## <a name="next-steps"></a>Pasos siguientes
* [Supervise dependencias](app-insights-asp-net-dependencies.md) para ver si REST, SQL u otros recursos externos se están ralentizando.
* [Use la API](app-insights-api-custom-events-metrics.md) para enviar sus propios eventos y métricas para obtener una vista más detallada del rendimiento y el uso de la aplicación.
