---
title: "Supervisión del nivel de aplicación de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información sobre los eventos y los registros de nivel de servicio y aplicación usados para supervisar y diagnosticar los clústeres de Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 9b07c7cd256cea31c5654f0c3325e9fb675b39f8
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>Generación de eventos y registros de nivel de aplicación y servicio

## <a name="instrumenting-the-code-with-custom-events"></a>Instrumentación del código con eventos personalizados

La instrumentación del código es la base para la mayoría de los demás aspectos de la supervisión de los servicios. La instrumentación es la única manera de saber que algo no funciona correctamente y diagnosticar lo que debe solucionarse. Aunque técnicamente es posible que conectar un depurador a un servicio de producción, no es un procedimiento habitual. Por lo tanto, es importante disponer de datos de instrumentación detallados.

Algunos productos instrumentan el código automáticamente. Aunque estas soluciones pueden funcionar bien, casi siempre se necesita instrumentación manual. Al final, debe tener suficiente información para depurar desde la aplicación de manera forense. Este documento describe los distintos enfoques para instrumentar el código y se indica cuándo elegir uno u otro.

## <a name="eventsource"></a>EventSource
Cuando se crea una solución de Service Fabric a partir de una plantilla en Visual Studio, se genera una clase derivada de **EventSource** (**ServiceEventSource** o **ActorEventSource**). Se crea una plantilla en la que podrá agregar eventos para la aplicación o el servicio. El nombre de **EventSource** **debe** ser único y debe cambiarse a partir de la cadena de plantilla predeterminada de MyCompany-&lt;solución&gt;-&lt;proyecto&gt;. El hecho de tener varias definiciones de **EventSource** con el mismo nombre genera un problema en tiempo de ejecución. Cada evento definido debe tener un identificador único. Si el identificador no es único, se produce un error en tiempo de ejecución. En algunas organizaciones se asignan previamente rangos de valores para los identificadores, lo cual evita conflictos entre los equipos de desarrollo independientes. Para más información, consulte el [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentación de MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Uso de eventos EventSource estructurados

Los eventos de los ejemplos de código de esta sección se definen para un caso concreto, como cuando se registra un tipo de servicio. Al definir mensajes por caso de uso, se pueden empaquetar datos con el texto del error, y se puede buscar y filtrar más fácilmente según el nombre o los valores de las propiedades especificadas. La estructuración del resultado de la instrumentación facilita su uso, pero se requiere mayor tiempo y reflexión para definir un nuevo evento para cada caso de uso. Algunas definiciones de eventos se pueden compartir en toda la aplicación. Por ejemplo, el evento de inicio o detención de un método se podría reutilizar en muchos servicios de una aplicación. Un servicio específico de dominio, como un sistema de pedidos, puede tener un evento **CreateOrder** con su propio evento único. Este enfoque genera a menudo una gran cantidad de eventos y puede necesitar la coordinación de los identificadores entre los equipos de proyecto. 

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

### <a name="using-eventsource-generically"></a>Uso de EventSource genéricamente

Dado que la definición de eventos específicos puede resultar difícil, muchas personas definen algunos con un conjunto común de parámetros que por lo general generan la información saliente como cadena. Gran parte de la estructura se pierde, lo que dificulta la búsqueda y el filtrado de los resultados. Con este enfoque se definen algunos eventos que normalmente corresponden a los niveles de registro. El fragmento de código siguiente define un mensaje de error y de depuración:

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

El uso de instrumentación híbrida (estructurada y genérica) también puede funcionar. La instrumentación estructurada se usa para informar sobre errores y métricas. Los eventos genéricos pueden incluirse en el registro detallado para la solución de problemas por parte de los ingenieros.

## <a name="aspnet-core-logging"></a>Registro de ASP.NET Core

Es importante planear minuciosamente la instrumentación del código. Un plan de instrumentación correcto puede ayudarle a evitar que se desestabilice el código base y sea necesario volver a instrumentarlo. Para reducir el riesgo, puede elegir una biblioteca de instrumentación como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), componente de Microsoft ASP.NET Core. ASP.NET Core tiene una interfaz [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que puede usar con su proveedor preferido al tiempo que reduce al mínimo el efecto sobre el código existente. Puede utilizar el código de ASP.NET Core en Windows y Linux, y en .NET Framework completo, por lo que el código de instrumentación es estándar. Esto se trata con más detalle a continuación:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso de Microsoft.Extensions.Logging en Service Fabric

1. Agregue el paquete de NuGet Microsoft.Extensions.Logging al proyecto que desee instrumentar. Además, agregue los paquetes del proveedor (vea el ejemplo siguiente para paquetes de terceros). Consulte el artículo sobre el [registro de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) para más información.
2. Agregue una directiva **using** para Microsoft.Extensions.Logging al archivo de servicio.
3. Defina una variable privada dentro de la clase de servicio.

  ```csharp
  private ILogger _logger = null;

  ```
4. En el constructor de la clase de servicio, agregue este código:

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. Inicie la instrumentación del código en los métodos. Estos son algunos ejemplos:

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

## <a name="using-other-logging-providers"></a>Uso de otros proveedores de registro

Algunos proveedores de terceros usan el enfoque descrito en la sección anterior, como [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) y [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Puede conectarlos al registro de ASP.NET Core o utilizarlos por separado. Serilog tiene una característica que enriquece todos los mensajes enviados desde un registrador. Esta característica puede ser útil para generar el nombre del servicio, el tipo y la información de la partición. Para utilizar esta funcionalidad en la infraestructura de ASP.NET Core, siga estos pasos:

1. Agregue los paquetes de NuGet Serilog, Serilog.Extensions.Logging y Serilog.Sinks.Observable al proyecto. En el ejemplo siguiente, agregue también Serilog.Sinks.Literate. Más adelante en este artículo se muestra un mejor enfoque.
2. Ebn Serilog, cree LoggerConfiguration y la instancia del registrador.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Agregue un argumento Serilog.ILogger al constructor del servicio y pase el registrador recién creado.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. En el constructor del servicio, agregue el siguiente código, con lo que se crean la propiedad enrichers para las propiedades **ServiceTypeName**, **ServiceName**, **PartitionId** y **InstanceId** del servicio. Se agrega también una propiedad enricher a la fábrica de registro de ASP.NET Core, por lo que podrá usar Microsoft.Extensions.Logging.ILogger en el código.

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. Instrumente el código como su estuviera usando ASP.NET Core sin Serilog.

  >[!NOTE]
  >No es recomendable usar el Log.Logger estático con el ejemplo anterior. Service Fabric puede hospedar varias instancias del mismo tipo de servicio dentro de un único proceso. Si usa el Log.Logger estático, la última versión de la propiedad enrichers mostrará los valores para todas las instancias en ejecución. Este es uno de los motivos por los cuales la variable _logger es una variable de miembro privada de la clase de servicio. Además, debe poner _logger a disposición del código común, que podría utilizarse en diferentes servicios.

## <a name="choosing-a-logging-provider"></a>Elección de un proveedor de registro

Si la aplicación depende del alto rendimiento, **EventSource** suele ser el mejor enfoque. *Por lo general*, **EventSource** utiliza menos recursos y su rendimiento es mejor que el del registro de ASP.NET Core o de cualquiera de las soluciones de terceros disponibles.  Esto no supone un problema para muchos servicios, pero si está orientado al rendimiento, **EventSource** sería una opción mejor. Sin embargo, para obtener las ventajas del registro estructurado, **EventSource** requiere una mayor inversión por parte del equipo de ingeniería. Si es posible, cree un prototipo rápido de algunas opciones de registro y, luego, seleccione la que mejor se adapte a sus necesidades.

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya elegido el proveedor de registro para instrumentar las aplicaciones y los servicios, los registros y los eventos deben agregarse antes de que se puedan enviar a cualquier plataforma de análisis. Obtenga información sobre [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) y [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para entender mejor algunas de las opciones recomendadas.
