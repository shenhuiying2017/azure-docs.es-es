<properties
   pageTitle="Configuración de Application Insights para su aplicación de Service Fabric"
   description="Reciba eventos de Service Fabric para su aplicación en Application Insights."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2015"
   ms.author="mattrow"/>

# Configuración de Application Insights para su aplicación de Service Fabric
 Este artículo le guiará a través de la habilitación de Application Insights para su aplicación de Service Fabric.

## Requisitos previos

En este artículo se supone que tiene una aplicación de Service Fabric ya creada en Visual Studio. [Haga clic aquí](service-fabric-reliable-services-quick-start.md) para averiguar cómo.

## Instalación del paquete de NuGet
Publicado como parte del SDK de Service Fabric, es una versión preliminar de nuestro paquete NuGet Microsoft.ServiceFabric.Telemetry.ApplicationInsights. Este paquete agrupa los eventos EventSource de Service Fabric con Application Insights para ofrecerle instrumentación automatizada de la aplicación de Service Fabric. Este paquete continuará actualizándose con nuevos eventos que se emitirán automáticamente por la aplicación.

Puede instalar el paquete con los pasos siguientes:

1. Abra el Administrador de paquetes de NuGet para su aplicación de Service Fabric. Para ello, haga clic con el botón secundario en el proyecto en Visual Studio y seleccione 'Administrar paquetes de NuGet...'.
2. Tendrá que seleccionar 'Service Fabric de Microsoft Azure' como el origen del paquete para enumerar los paquetes incluidos en el SDK de Service Fabric. ![Administrador de paquetes NuGet VS2015](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. Seleccione el paquete Microsoft.ServiceFabric.Telemetry.ApplicationInsights a la izquierda.
4. Haga clic en Instalar para comenzar el proceso de instalación.
5. Revise y acepte el CLUF.

## Habilitar eventos de Service Fabric
Para recibir eventos de Service Fabric automáticamente en Application Insights, deberá habilitar nuestro agente de escucha. Para ello, inserte la siguiente línea de código en la aplicación.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### Ejemplo de StatefulActor\\Program.cs:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

Puede aprender sobre los eventos emitidos desde el tiempo de ejecución de actores confiables [aquí](service-fabric-reliable-actors-diagnostics.md) y el motor en tiempo de ejecución de servicios de confianza [aquí](service-fabric-reliable-services-diagnostics.md).

Tenga en cuenta que para obtener llamadas a métodos en tiempo de ejecución de actores confiables, se debe usar EventLevel.Verbose (como se muestra en los ejemplos anteriores).

## Configuración de Application Insights
Una clave de instrumentación es lo que vincula la aplicación de Service Fabric a su recurso de Application Insights. Aprenderá cómo obtener la clave de instrumentación siguiendo la [Guía de Application Insight](../app-insights-create-new-resource.md#create-an-application-insights-resource). Seleccione 'Otra' para el tipo de aplicación al crear recursos.

![Seleccionar Otro para el tipo de aplicación de AI](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

Cuando tenga la clave de instrumentación, puede insertarla en el archivo ApplicationInsights.config de la siguiente manera:

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## Visualización de datos
Puede [personalizar la hoja de App Insights](../app-insights-metrics-explorer.md) para adaptarla a sus necesidades. La mayoría de los eventos de Service Fabric aparecerán como 'Eventos personalizados,' mientras que las llamadas de método del actor de tejido y las llamadas a RunAsync() de servicio aparecerán como solicitudes. El modelado de estos eventos como solicitudes le permite usar la dimensión 'nombre de solicitud' y la métrica 'duración de la solicitud' al crear gráficos. Se seguirán agregando nuevos gráficos, métricas y eventos que podrá aprovechar en el futuro.

## Pasos siguientes
Obtenga más información sobre el uso de Application Insights para instrumentar sus aplicaciones de Service Fabric.

- [Introducción a Application Insights](../app-insights-get-started.md)
- [Aprenda a crear sus propias métricas y eventos personalizados](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=August15_HO6-->