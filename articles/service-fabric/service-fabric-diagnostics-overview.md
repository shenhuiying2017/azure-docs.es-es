---
title: "Información general sobre supervisión y diagnóstico de Azure Service Fabric | Microsoft Docs"
description: Aprenda a supervisar y diagnosticar las aplicaciones de Microsoft Azure Service Fabric hospedadas en Azure, en desarrollo o de manera local.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: dekapur
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 9c1a3bb6de8756c37903e5f1b9dcf3fdc1ef6a11
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="monitoring-and-diagnostics-in-azure-service-fabric"></a>Supervisión y diagnóstico en Azure Service Fabric

La supervisión y el diagnóstico son fundamentales para la realización correcta de desarrollos, pruebas e implementaciones en entornos de producción. Las soluciones de Service Fabric funcionan mejor cuando se implementan supervisión y diagnóstico desde el panel get go, lo que ayuda a garantizar que los servicios funcionan sin problemas en los entornos de desarrollo local y en configuraciones de clúster de producción reales.

Los objetivos principales de la supervisión y diagnóstico son:
* Detectar y diagnosticar problemas de hardware y de infraestructura
* Detectar problemas de software y aplicaciones, y reducir el tiempo de inactividad del servicio
* Conocer el consumo de recursos y facilitar la toma de decisiones de operaciones
* Optimizar el rendimiento de la aplicación, el servicio y la infraestructura
* Generar información de la empresa e identificar áreas de mejora


La supervisión y el diagnóstico son importantes para garantizar que todo funciona según lo previsto y permiten responder a distintas situaciones con una interrupción mínima del servicio. 

El flujo de trabajo general de la supervisión y el diagnóstico consta de tres pasos: 
1. **Generación de eventos**: incluye eventos (registros, seguimientos y eventos personalizados) tanto a nivel de infraestructura (clúster) como de aplicación/servicio 
2. **Agregación de eventos**: es preciso recopilar y agregar los eventos generados antes de que se puedan mostrar. Esto puede realizarse mediante tablas de almacenamiento, tal como lo configura Windows Azure Diagnostics o mediante la creación de una canalización de EventFlow
3. **Análisis**: los eventos se deben visualizar y acceder en algún formato, para permitir su análisis y visualización cuando sea necesario. Puede elegir utilizar herramientas como ApplicationInsights, Operations Management Suite, Kibana, etc.

Aunque hay varios productos disponibles que abarcan las tres áreas, muchos clientes eligen tecnologías diferentes para cada uno de estos aspectos. Es importante que las piezas trabajen conjuntamente para ofrecer una solución de supervisión de un extremo a otro para la aplicación.

En función de sus necesidades, puede ampliar los diagnósticos y la supervisión para incluir alertas y mitigación automatizadas, que a menudo están integradas en las herramientas de análisis que puede elegir usar. 

## <a name="event-generation"></a>Generación de eventos

Se pueden generar eventos, registros y seguimientos desde la capa de infraestructura (cualquier cosa desde el clúster, los equipos o las acciones de Service Fabric) o de la capa de la aplicación (toda la instrumentación que se agrega a las aplicaciones y servicios que se implementan en el clúster).

### <a name="infrastructure-monitoring-the-cluster"></a>Infraestructura: supervisión del clúster

Service Fabric puede ayudar a mantener una aplicación en ejecución durante los errores de infraestructura, pero debe saber si el error se produce en la aplicación o en la infraestructura subyacente. También debe supervisar la infraestructura para planear la capacidad y saber cuándo se debe agregar o quitar infraestructura. Es importante supervisar tanto la infraestructura como la aplicación que componen una implementación de Service Fabric, así como solucionar sus problemas. Aunque la aplicación esté disponible para los clientes, la infraestructura puede seguir siendo una fuente potencial de problemas. Para asegurarse de que el clúster se comporta según lo previsto, Service Fabric configura cinco canales distintos de registro de manera predeterminada:

1. Canal operativo: operaciones de alto nivel realizadas por Service Fabric y el clúster, incluye eventos para un nodo próximo, una nueva aplicación que se implementa o la reversión de una actualización de SF, etc. 
2. Canal de información a cliente: informes de estado y decisiones para el equilibrio de carga
3. Eventos de Reliable Services: eventos específicos del modelo de programación
4. Eventos de Reliable Actors: contadores de rendimiento y eventos específicos del modelo de programación
5. Registros de soporte técnico: registros del sistema que genera Service Fabric únicamente para que los usemos al proporcionar soporte técnico

Se recomienda encarecidamente habilitar "Diagnósticos" al crear el clúster. Esto se puede realizar, como se muestra a continuación, o mediante una plantilla de Azure Resource Manager que incluye diagnósticos. 

![Creación de clústeres en Azure Portal con diagnósticos habilitados](./media/service-fabric-diagnostics-overview/azure-enable-diagnostics.png)

Como ya se ha visto, también hay un campo opcional para agregar una clave de instrumentación de Application Insights (AppInsights). Si elige usar AppInsights para los análisis de eventos (AppInsights es una de las soluciones recomendadas), incluya la clave de instrumentación de recursos de AppInsights (GUID).

#### <a name="service-fabric-support-logs"></a>Registros de soporte técnico de Service Fabric

Si tiene que ponerse en contacto con el soporte técnico de Microsoft para que le ayuden con el clúster de Azure Service Fabric, casi siempre necesitará los registros de soporte técnico. Si el clúster se hospeda en Azure, estos registros de soporte técnico se configuran y recopilan automáticamente en el proceso de creación del clúster. Los registros se almacenan en una cuenta de almacenamiento específica del grupo de recursos del clúster. La cuenta de almacenamiento no tiene nombre fijo, pero en ella verá contenedores de blobs y tablas cuyos nombres empiezan por *fabric*. Para obtener información acerca de cómo configurar las colecciones de registros para un clúster independiente, consulte el artículo sobre la [creación y la administración de un clúster independiente de Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) y [Opciones de configuración clústeres de Windows independientes](service-fabric-cluster-manifest.md). Para instancias independientes de Service Fabric, los registros se deben enviar a un archivo local compartido. Es **necesario** que tenga estos registros para el soporte técnico, pero no se prevé que los utilice nadie que no pertenezca al equipo de soporte técnico al cliente de Microsoft.

#### <a name="azure-service-fabric-health-and-load-reporting"></a>Informes de carga y mantenimiento de Azure Service Fabric

Service Fabric tiene su propio modelo de mantenimiento, que se describe en detalle en estos artículos:
- [Introducción a la supervisión del mantenimiento de Service Fabric](service-fabric-health-introduction.md)
- [Notificación y comprobación del estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Incorporación de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)
- [Vista de los informes de estado de Service Fabric](service-fabric-view-entities-aggregated-health.md)

La supervisión del mantenimiento es fundamental para diversos aspectos operativos de un servicio. La supervisión del mantenimiento es especialmente importante cuando Service Fabric lleva a cabo una actualización de la aplicación con nombre. Cuando estén actualizados y disponibles para los clientes los dominios del servicio, deben pasar las comprobaciones de mantenimiento para que la implementación pase al siguiente dominio de actualización. Si no se puede alcanzar un mantenimiento correcto, la implementación se revierte y deja la aplicación en un estado correcto conocido. Aunque algunos clientes resultaran afectados antes de que los servicios se pudieran revertir, la mayoría no experimenta ningún problema. Además, la resolución se produce de forma relativamente rápida y sin necesidad de esperar a la acción de un operador humano. Cuantas más comprobaciones de mantenimiento se integren en el código, más resistente será el servicio a los problemas de implementación.

Otro aspecto del estado del servicio es la notificación de métricas desde el servicio. Las métricas son importantes en Service Fabric, porque se usan para equilibrar el uso de los recursos. Además, son un indicador del estado del sistema. Por ejemplo, supongamos que tiene una aplicación con muchos servicios y que cada instancia informa sobre una métrica de solicitudes por segundo (RPS). Si uno de los servicios usa más recursos que otro, Service Fabric mueve instancias de servicio en el clúster para intentar mantener un uso uniforme de los recursos. Para una explicación más detallada sobre cómo funciona el uso de los recursos, consulte [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md).

Las métricas también pueden ayudarle con una visión general de rendimiento del servicio. Con el tiempo, las métricas se pueden usar para comprobar que el servicio funciona con los parámetros previstos. Por ejemplo, si las tendencias muestran que a las 9 de la mañana del lunes, el promedio de solicitudes por segundo es de 1000, podría configurar un informe de mantenimiento que emita una alerta si descienden de 500 o aumentan por encima de las 1500. Todo puede estar funcionando correctamente, pero quizá valga la pena echar un vistazo para asegurarse de que los clientes disfrutan de una gran experiencia. El servicio puede definir un conjunto de métricas que se pueden notificar para controlar el mantenimiento, pero que no afecten al equilibrio de los recursos del clúster. Para ello, establezca la ponderación métrica en cero. Se recomienda iniciar toda la métrica con una ponderación de cero y no aumentarla hasta que se sepa con certeza la repercusión que tiene en el equilibrio de los recursos para el clúster.

> [!TIP]
> No use demasiadas métricas ponderadas. Puede ser difícil de entender por qué se mueven instancias de servicio para el equilibrio. Algunas pueden moverse muchísimo.

Cualquier información que pueda indicar el estado y el rendimiento de la aplicación es apta para los informes de mantenimiento y métricas. Un contador de rendimiento de la CPU puede indicar cómo se está usando el nodo, pero no indica si un servicio determinado está en buen estado, ya que pueden estar ejecutándose varios servicios en un solo nodo. Pero las métricas como RPS, los elementos procesados y la latencia de solicitud, pueden indicar el estado de un servicio específico.

Para notificar el estado, use código similar al siguiente:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Para notificar una métrica, use código similar al siguiente:

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```


### <a name="application-instrumenting-code-for-custom-events"></a>Aplicación: código de instrumentación para eventos personalizados

La instrumentación del código es la base para la mayoría de los demás aspectos de la supervisión de los servicios. La instrumentación es la única manera de saber que algo no funciona correctamente y diagnosticar lo que debe solucionarse. Aunque técnicamente es posible que conectar un depurador a un servicio de producción, no es un procedimiento habitual. Por lo tanto, es importante disponer de datos de instrumentación detallados. 

Algunos productos instrumentan el código automáticamente. Aunque estas soluciones pueden funcionar bien, casi siempre se necesita instrumentación manual. Al final, debe tener suficiente información para depurar desde la aplicación de manera forense. En las siguientes secciones se describen los distintos enfoques para instrumentar el código y se indica cuándo elegir uno u otro.

#### <a name="eventsource"></a>EventSource

Cuando se crea una solución de Service Fabric a partir de una plantilla en Visual Studio, se genera una clase derivada de **EventSource** (**ServiceEventSource** o **ActorEventSource**). Se crea una plantilla en la que podrá agregar eventos para la aplicación o el servicio. El nombre de **EventSource** **debe** ser único y debe cambiarse a partir de la cadena de plantilla predeterminada de MyCompany-&lt;solución&gt;-&lt;proyecto&gt;. El hecho de tener varias definiciones de **EventSource** con el mismo nombre genera un problema en tiempo de ejecución. Cada evento definido debe tener un identificador único. Si el identificador no es único, se produce un error en tiempo de ejecución. En algunas organizaciones se asignan previamente rangos de valores para los identificadores, lo cual evita conflictos entre los equipos de desarrollo independientes. Para más información, consulte el [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentación de MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

##### <a name="using-structured-eventsource-events"></a>Uso de eventos EventSource estructurados

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

##### <a name="using-eventsource-generically"></a>Uso de EventSource genéricamente

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

#### <a name="aspnet-core-logging"></a>Registro de ASP.NET Core

Es importante planear minuciosamente la instrumentación del código. Un plan de instrumentación correcto puede ayudarle a evitar que se desestabilice el código base y sea necesario volver a instrumentarlo. Para reducir el riesgo, puede elegir una biblioteca de instrumentación como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), componente de Microsoft ASP.NET Core. ASP.NET Core tiene una interfaz [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que puede usar con su proveedor preferido al tiempo que reduce al mínimo el efecto sobre el código existente. Puede utilizar el código de ASP.NET Core en Windows y Linux, y en .NET Framework completo, por lo que el código de instrumentación es estándar.

##### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Uso de Microsoft.Extensions.Logging en Service Fabric

1. Agregue el paquete de NuGet Microsoft.Extensions.Logging al proyecto que desee instrumentar. Además, agregue los paquetes del proveedor (vea el ejemplo siguiente para paquetes de terceros). Consulte el artículo sobre el [registro de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) para más información.
2. Agregue una directiva **using** para Microsoft.Extensions.Logging al archivo de servicio.
3. Defina una variable privada dentro de la clase de servicio.

  ```csharp
  private ILogger _logger = null;

  ```
4. En el constructor de la clase de servicio, agregue este código.

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

#### <a name="using-other-logging-providers"></a>Uso de otros proveedores de registro

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

4. En el constructor del servicio, agregue el código siguiente. El código crea la propiedad enrichers para las propiedades **ServiceTypeName**, **ServiceName**, **PartitionId** e **InstanceId** del servicio. Se agrega también una propiedad enricher a la fábrica de registro de ASP.NET Core, por lo que podrá usar Microsoft.Extensions.Logging.ILogger en el código.

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

  > [!NOTE]
  > No es recomendable usar el Log.Logger estático con el ejemplo anterior. Service Fabric puede hospedar varias instancias del mismo tipo de servicio dentro de un único proceso. Si usa el Log.Logger estático, la última versión de la propiedad enrichers mostrará los valores para todas las instancias en ejecución. Este es uno de los motivos por los cuales la variable _logger es una variable de miembro privada de la clase de servicio. Además, debe poner _logger a disposición del código común, que podría utilizarse en diferentes servicios.

#### <a name="choosing-a-logging-provider"></a>Elección de un proveedor de registro

Si la aplicación depende del alto rendimiento, **EventSource** es el mejor enfoque. *Por lo general*, **EventSource** utiliza menos recursos y su rendimiento es mejor que el del registro de ASP.NET Core o de cualquiera de las soluciones de terceros disponibles.  Esto no supone un problema para muchos servicios, pero si está orientado al rendimiento, **EventSource** sería una opción mejor. Para obtener los mismos beneficios que el registro estructurado, **EventSource** requiere una mayor inversión por parte del equipo de ingeniería. Para determinar el enfoque para el proyecto, realice un prototipo rápido de lo que conlleva cada opción y elija la que mejor se adapte a sus necesidades.

## <a name="event-aggregation-and-collection"></a>Recopilación y agregación de eventos

### <a name="azure-diagnostics"></a>Diagnóstico de Azure

Además de la información que proporciona Azure Monitor, Azure recopila eventos de los servicios en una ubicación central. Para más información, aprenda a configurar la recopilación de eventos para [Windows](service-fabric-diagnostics-how-to-setup-wad.md) y [Linux](service-fabric-diagnostics-how-to-setup-lad.md). En estos artículos se muestra la recopilación de datos de eventos y su envío a Azure Storage. Para hacer esto en Azure Portal o en la plantilla de Azure Resource Manager, habilite el diagnóstico. Diagnósticos de Azure recopila algunos orígenes de eventos que Service Fabric genera automáticamente:

- Eventos de **ETW** emitidos como parte del canal operativo
- Eventos de **EventSource** y contadores de rendimiento cuando se usa el modelo de programación de Reliable Actor. Los eventos se enumeran en [Diagnósticos y supervisión del rendimiento de Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- Eventos de **EventSource** cuando se usa el modelo de programación de Reliable Services. Los eventos se enumeran en [Funcionalidad de diagnóstico para Reliable Services con estado](service-fabric-reliable-services-diagnostics.md).


Al configurarlos, los eventos aparecen en una cuenta de Azure Storage que se generó al crear el clúster, suponiendo que habilitara el diagnóstico. Las tablas se denominan WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable y WADServiceFabricSystemEventTable. Los eventos de mantenimiento no se agregan de forma predeterminada, tiene que modificar la plantilla de Resource Manager para agregarlos. Para más información, consulte [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md).

En los artículos que se enumeran en esta sección también se muestra cómo obtener eventos personalizados en Azure Storage. A los clústeres de Service Fabric también se les aplican otros artículos de Diagnósticos de Azure acerca de la configuración de los contadores de rendimiento o artículos con otros datos de supervisión de máquinas virtuales para Diagnósticos de Azure. Por ejemplo, si no desea que el destino sea Azure Table Storage, hay un artículo sobre la [Transmisión de datos de Diagnósticos de Azure en la ruta de acceso activa mediante Event Hubs](../event-hubs/event-hubs-streaming-azure-diags-data.md). Cuando los eventos se encuentran en Azure Event Hubs, puede leerlos y enviarlos a la ubicación que prefiera. Más información sobre la integración en [Azure Diagnostics integration with Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) (Integración de Diagnósticos de Azure con Application Insights).

Una desventaja de Diagnósticos de Azure es que se configura con una plantilla de plantilla de Resource Manager. Por lo tanto, el diagnóstico se produce solo en el nivel de conjunto de escalado de máquinas virtuales. Un conjunto de escalado de máquinas virtuales corresponde a un tipo de nodo de Service Fabric. Cada tipo de nodo se configura para todas las aplicaciones y los servicios que se puedan ejecutar en ese tipo de nodo. Esto podría suponer numerosos eventos **EventSource**, en función del número de aplicaciones y servicios configurados. También es necesaria la implementación en Resource Manager siempre que se cambie la configuración de una aplicación. Lo ideal sería que la configuración de la supervisión se sincronizara con la del servicio.

Diagnósticos de Azure solo funciona para los clústeres de Service Fabric implementados en Azure. Funciona para los clústeres tanto Windows como Linux.

### <a name="eventflow"></a>EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) puede enrutar los eventos de un nodo a uno o varios destinos de supervisión. Dado que se incluye como un paquete de NuGet en el proyecto de servicio, el código y la configuración de EventFlow van con el servicio, por lo que se elimina el problema de la configuración por nodo que se mencionó antes sobre Diagnósticos de Azure. EventFlow se ejecuta dentro del proceso de servicio y se conecta directamente con las salidas configuradas. Gracias a esta conexión directa, EventFlow funciona en implementaciones de servicios de Azure, de contenedor o locales. Tenga cuidado si ejecuta EventFlow en escenarios de alta densidad, como un contenedor, ya que cada canalización EventFlow genera una conexión externa. Si hospeda gran cantidad de procesos, obtendrá numerosas conexiones salientes. Esto no supone un problema para las aplicaciones de Service Fabric, ya que todas las réplicas de `ServiceType` se ejecutan en el mismo proceso, lo que limita el número de conexiones salientes. EventFlow también proporciona filtrado de eventos, de manera que solo se envían los eventos que coinciden con el filtro especificado. Para información detallada sobre el uso de EventFlow con Service Fabric, consulte [Recopilación de registros directamente desde un proceso de servicio de Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

Para usar EventFlow:

1. Agregue el paquete de NuGet al proyecto del servicio.
2. En la función **Main** del servicio, cree la canalización de EventFlow y configure las salidas. En el siguiente ejemplo se usa Serilog como salida.

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. Cree un archivo denominado eventFlowConfig.json en la carpeta \\PackageRoot\\Config del servicio. Por dentro, el archivo de configuración es similar a esto:

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    En la configuración se definen dos entradas: los dos orígenes basados en **EventSource** creados por Service Fabric y el **EventSource** para el servicio. Los eventos de mantenimiento y nivel de sistema que usan ETW no están disponibles para EventFlow. Esto es porque se requiere un alto nivel de privilegios para realizar escuchas de un origen ETW y los servicios nunca se ejecutan con privilegios altos. La otra entrada es Serilog, la configuración se realizó en el método **Main**.  Se aplican algunos filtros. El primer filtro indica a EventFlow que quite todos los eventos de nivel detallado. Se configuran dos salidas: una estándar, que escribirá en la ventana de salida de Visual Studio y Application Insights. Asegúrese de que agregar la clave de instrumentación.

4. Instrumente el código. En el ejemplo siguiente, se instrumenta `RunAsync` de varias maneras a modo de ejemplo. En el código siguiente, seguimos usando Serilog. Parte de la sintaxis que se utiliza es específica de Serilog. Tenga en cuenta las funcionalidades específicas para la solución de registro que elija. Se generan tres eventos: uno de nivel de depuración y dos informativos. El segundo evento informativo realiza el seguimiento de la duración de la solicitud. Con la configuración de EventFlow que se describió anteriormente, el evento de nivel de depuración no debería ir hasta la salida.

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Para ver los eventos en Azure Application Insights, abra Azure Portal y vaya al recurso Application Insights. Para ver los eventos, seleccione el cuadro **Buscar**.

![Vista de los eventos en Buscar de Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

Puede ver el seguimiento en la parte inferior de la captura de pantalla anterior. Se muestran solo dos eventos y el de nivel de depuración lo quitó EventFlow. La entrada de la solicitud anterior del seguimiento es la tercera línea de instrumentación `_logger`. La línea muestra que el evento se tradujo en una métrica de solicitud en Application Insights.

En la definición del filtro, el tipo es **metadatos**. Esto indica que un evento con una propiedad de `RequestName` cuyo valor sea `MyRequest` y otra propiedad `Duration` contienen la duración de la solicitud, en milisegundos. Esto es lo que se ve en el evento de solicitud en Application Insights. El mismo enfoque sirve para cualquiera de las entradas de EventFlow compatibles, como **EventSource**.

Si tiene un clúster independiente que no se puede conectar a una solución basada en la nube a causa de las directivas, puede utilizar Elasticsearch como salida. Sin embargo, se pueden escribir otras salidas y se aconsejan las solicitudes de extracción. Algunos de los proveedores de terceros para el registro de ASP.NET Core también tienen soluciones que admiten instalaciones locales.

## <a name="visualization-analysis-and-alerts"></a>Visualización, análisis y alertas

La última parte de la supervisión es la visualización del flujo de eventos, la generación de informes sobre el rendimiento del servicio y la emisión de alertas cuando se detecta un problema. Puede usar diferentes soluciones para este aspecto de la supervisión. AppInsights y Operations Management Suite (OMS) para generar alertas basadas en el flujo de eventos. Puede usar Microsoft Power BI o una solución de terceros, como [Kibana](https://www.elastic.co/products/kibana) o [Splunk](https://www.splunk.com/) para visualizar los datos.

### <a name="appinsights"></a>AppInsights

AppInsights es una de las herramientas recomendadas para la supervisión de aplicaciones y servicios. El SDK de AI actualizado funciona estupendamente con los eventos de Service Fabric y, además, proporciona buenas visualizaciones de datos y una herramienta de consulta (a través de AppInsights Analytics); también puede crear una instancia de AppMap precisa que puede ayudarle a realizar un seguimiento de las dependencias entre procesos en una aplicación o un clúster.

Configure un recurso de AppInsights mediante la búsqueda de "Application Insights" en Azure Marketplace. Tras crearlo, vaya a *Propiedades* para buscar la clave de instrumentación de AI (en forma de GUID). Dicha clave se utiliza para:
* Integrar AppInsights para recibir eventos de nivel de infraestructura de un clúster de Service Fabric directamente a través de una plantilla de Azure Resource Manager o a través de Azure Portal al crear un clúster, siempre que Diagnostics se haya habilitado
* Configurar EventFlow (eventFlowConfig.json) para generar datos para Application Insights, como se ha mostrado muestra en la sección anterior

### <a name="oms"></a>OMS

OMS es otra herramienta recomendada para el diagnóstico y la supervisión de clústeres de Service Fabric. La solución Service Fabric puede agregarse a cualquier área de trabajo y tiene un panel que muestra los diferentes tipos de eventos de Service Fabric. Las áreas de trabajo de OMS también tienen una eficaz herramienta de consulta de registros en Log Analytics.

Para configurar un área de trabajo de OMS, confirme Diagnostics se habilitó para el clúster. Agregue "Service Fabric Analytics" desde Azure Marketplace a un área de trabajo de OMS existente o cree uno nuevo. Configure los orígenes de datos del área de trabajo para conectarse a las tablas de Azure Storage en las que el clúster escribe eventos. 

Para que OMS seleccione eventos personalizados, también hay que asegurarse de que la instrumentación que se agrega a las aplicaciones también debe escribir en las mismas tablas de Storage, o en cualesquiera otras que estén configuradas para ser orígenes del área de trabajo. 

OMS también es actualmente la ruta de acceso recomendada para el análisis y visualización de datos si se necesita supervisión y diagnóstico de los contenedores, ya que puede agregar una solución Containers al área de trabajo que funcione bien con los contenedores organizados de Service Fabric. Para ver una breve guía sobre su configuración, vaya [aquí](service-fabric-diagnostics-containers-windowsserver.md).

### <a name="azure-monitor"></a>Azure Monitor

Puede usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para supervisar muchos de los recursos de Azure que incorporan un clúster de Service Fabric. En Azure Portal, se recopila y se muestra automáticamente un conjunto de métricas para el [conjunto de escalado de máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) y las [máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuales. Para ver la información recopilada, en Azure Portal, seleccione el grupo de recursos que contenga el clúster de Service Fabric y seleccione el conjunto de escalado de máquinas virtuales que desee ver. En la sección **Supervisión**, seleccione **Métricas** para ver un gráfico con los valores.

![Vista de Azure Portal con la información de métrica recopilada](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Para personalizar los gráficos, siga las instrucciones del artículo de [introducción a las métricas en Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). También puede crear alertas basadas en estas métricas, como se describe en [Creación de alertas en Azure Monitor para servicios de Azure - Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Puede enviar alertas a un servicio de notificación mediante webhooks, tal y como se describe en [Configuración de un webhook en una alerta de métrica de Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure Monitor es compatible con una sola suscripción. Si quiere supervisar varias suscripciones o necesita características adicionales, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte de Microsoft Operations Management Suite, proporciona una solución integral de administración de TI para infraestructura tanto local como basada en la nube. Los datos de Azure Monitor se pueden enrutar directamente a Log Analytics para ver los registros y las métricas de todo el entorno desde un único lugar.

Operations Management Suite es el método de supervisión de la infraestructura local recomendado, pero también puede utilizar cualquier solución existente que use la organización para la supervisión de la infraestructura.

## <a name="additional-steps"></a>Pasos adicionales

### <a name="watchdogs"></a>Guardianes

Un guardián es un servicio independiente que vigila el estado y la carga en los servicios e informa sobre el estado de cualquier componente en la jerarquía del modelo de mantenimiento. Esto puede ayudar a evitar errores que se pasan por alto con la vista de un solo servicio. Los guardianes sirven también para hospedar el código que lleva a cabo acciones correctoras para condiciones conocidas sin necesidad de intervención humana. [Aquí](https://github.com/Azure-Samples/service-fabric-watchdog-service) puede encontrar la implementación de un servicio guardián de ejemplo.


## <a name="next-steps"></a>Pasos siguientes

* [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Recopilación de registros directamente desde un proceso de servicio de Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md)

