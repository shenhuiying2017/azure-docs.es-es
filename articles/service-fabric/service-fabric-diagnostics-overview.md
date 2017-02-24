---
title: "Información general sobre supervisión y diagnóstico de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información acerca de cómo supervisar y diagnosticar las aplicaciones de Microsoft Azure Service Fabric hospedadas en Azure, en desarrollo o de manera local."
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: a83fe451ec8b77b8b84575c5fd46f3661fc261c0
ms.openlocfilehash: 77e5872654aa6f50b5a6564fb8ee8c7a6a4c29a5


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Supervisión y diagnóstico de aplicaciones de Azure Service Fabric

La supervisión, la detección, el diagnóstico y la solución de problemas permiten que los servicios continúen con una interrupción mínima para la experiencia del usuario, ofreciendo información empresarial, supervisando el uso de recursos, detectando errores de hardware y software o problemas de rendimiento y diagnosticando posibles problemas de comportamiento del servicio. Aunque la supervisión y el diagnóstico resultan fundamentales en un entorno de producción implementado real, la eficacia depende de la adopción de un modelo similar durante el desarrollo de servicios para garantizar que funcionan cuando pase a una configuración del mundo real. Service Fabric facilita a los desarrolladores de servicio la implementación de diagnósticos que puede funcionar sin problemas en configuraciones de desarrollo local de máquina única y en configuraciones de clúster de producción del mundo real. 

La supervisión es un término amplio que abarca el proceso de instrumentación del código, la recopilación de registros de instrumentación, el análisis de los registros, la visualización de información basada en los datos del registro, la creación de alertas basadas en los valores y las informaciones de los registros, la supervisión de la infraestructura y la facilitación a los ingenieros de la detección y el diagnóstico de los problemas que afectan a sus clientes. El objetivo de este artículo es proporcionar una visión general de la supervisión para los clústeres de Service Fabric hospedados en Azure o de forma local, implementados en Windows o Linux con. NET. Empecemos por dividir el problema en tres partes:
- Código o infraestructura de instrumentación
- Recopilación de eventos generados
- Almacenamiento, agregación, visualización y análisis

Aunque hay algunos productos que abarcan los tres aspectos, muchos clientes prefieren tecnologías diferentes para cada área. Es importante conectarlos entre sí para ofrecer una solución de supervisión integral para la aplicación. 

## <a name="monitoring-infrastructure"></a>Supervisión de la infraestructura

Mientras que Service Fabric contribuye a mantener una aplicación ejecutándose durante los errores de infraestructura, los operadores de la aplicación necesitan saber si se está produciendo un error en la aplicación o en la infraestructura subyacente. La supervisión de la infraestructura también es necesaria para planear la capacidad a fin de conocer cuándo se debe agregar o quitar la infraestructura. Tanto la infraestructura como la aplicación que componen una implementación de Service Fabric son importantes para supervisar y solucionar problemas. Siempre que la aplicación esté disponible para los clientes, parte de la infraestructura puede estar sufriendo problemas.

### <a name="azure-monitoring"></a>Supervisión de Azure

En el caso de los clústeres implementados de Azure, la [Supervisión de Azure](../monitoring-and-diagnostics/monitoring-overview.md) ofrece la posibilidad de controlar muchos de los recursos de Azure en los que se crea un clúster de Service Fabric. Automáticamente se recopila y se muestra un conjunto de métricas en Azure Portal para el [conjunto de escalado de máquinas virtuales (VMSS)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) y las [máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuales. Esta información puede verse en Azure Portal seleccionando el grupo de recursos en el que está el clúster de Service Fabric y luego eligiendo el VMSS para ver. A continuación, elija Métricas en la sección de exploración de Supervisión para ver un gráfico de los valores.

![Vista de Azure Portal con la información de métrica recopilada](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Los gráficos se pueden personalizar siguiendo las instrucciones que aparecen en el artículo [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). También se pueden crear alertas en función de estas métricas como se describe en el artículo [Uso de Azure Portal para crear alertas de los servicios de Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Las alertas se pueden enviar a un servicio de notificaciones mediante webhooks tal como se describe en el artículo [Configuración de un webhook en una alerta de métrica de Azure (../monitoring-and-diagnostics/insights-webhooks-alerts.md). Supervisión de Azure es compatible con una sola suscripción. Si se necesita compatibilidad con varias suscripciones o características adicionales, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) parte de Operations Management Suite proporciona una solución integral de administración de TI tanto para infraestructura local como basada en la nube. Los datos de Supervisión de Azure se pueden enrutar directamente a Log Analytics para poder ver los registros y las métricas de todo el entorno en un único lugar.

Operations Management Suite es el método recomendado para la supervisión de la infraestructura local, pero también puede utilizarse cualquier solución existente que use la empresa para la supervisión de la infraestructura.

### <a name="service-fabric-support-logs"></a>Registros de soporte técnico de Service Fabric

Si tuviera que ponerse en contacto con el soporte técnico de Microsoft para obtener ayuda con el clúster de Azure Service Fabric, casi siempre necesitará registros de soporte técnico. Si el clúster está hospedado dentro de Azure, estos registros se configuran y recopilan automáticamente en el proceso de creación de un clúster. Los registros se almacenan en una cuenta de almacenamiento dedicada que se puede ver en el grupo de recursos de su clúster. No hay ningún nombre fijo para la cuenta de almacenamiento, pero dentro de la cuenta verá contenedores de blobs y tablas que empiezan por "fabric". Si el clúster es un clúster independiente, debe configurar la recopilación de estos registros siguiendo las instrucciones de [Creación y administración de un clúster que se ejecute en Windows Server](service-fabric-cluster-creation-for-windows-server.md) y [Opciones de configuración de clústeres de Windows independientes](service-fabric-cluster-manifest.md). Para Service Fabric independiente, se deben enviar los registros a un recurso compartido de archivos local. Es **necesario** que tenga estos registros para el soporte técnico, pero no se prevé que los utilice nadie que no pertenezca al equipo de soporte técnico al cliente de Microsoft. Usando mis mejores dotes de Obi Wan Kenobi, puedo deducir que estos no son los registros que le interesan...

## <a name="instrument-your-code"></a>Instrumentación del código

La instrumentación del código es la base para la mayoría de los demás aspectos de la supervisión de los servicios. La gente a menudo se sorprende de la cantidad de instrumentación que se necesita, pero teniendo en cuenta que esta instrumentación es la única manera que tiene de saber que algo no funciona correctamente y de diagnosticar qué debe solucionarse. Aunque es técnicamente posible hacerlo, es poco habitual conectar un depurador a un servicio de producción, por lo que es importante disponer de datos de instrumentación detallados. La producción de este volumen de información que envía todos los eventos fuera del nodo local puede ser costosa. Muchos servicios usan una estrategia de dos partes para tratar el volumen de los datos de instrumentación:
* Todos los eventos se conservan en el archivo de registro gradual local durante un breve intervalo de días y solo se recopilan cuando sea necesario para la depuración. Normalmente, los eventos necesarios para el diagnóstico detallado se dejan en el nodo para reducir los costos y la utilización de recursos.
* Cualquier evento que indique el estado del servicio, como los eventos de error, los eventos de latidos o los eventos de rendimiento, se envían a un repositorio central, donde pueden usarse para generar alertas de un servicio en mal estado.

Hay soluciones que instrumentan el código automáticamente. Aunque estos productos pueden funcionar bien, casi siempre se necesita la instrumentación manual. Al final, debe tener suficiente información para depurar desde la aplicación de manera forense. Las siguientes secciones describen los distintos enfoques para instrumentar el código y cuándo se debe elegir uno u otro.

### <a name="eventsource"></a>EventSource

Cuando se crea una solución de Azure Service Fabric desde una plantilla en Visual Studio, se genera una clase derivada de EventSource (*ServiceEventSource* o *ActorEventSource*). Esto proporciona una plantilla a la que puede agregar eventos adicionales adecuados para su aplicación o servicio. El nombre de EventSource **debe** ser único y debe cambiarse a partir de la cadena de inicio de "MiEmpresa-&lt;solución&gt;-&lt;proyecto&gt;". El hecho de tener varias definiciones de EventSource con el mismo nombre genera un problema en tiempo de ejecución. Cada evento definido debe tener un identificador único. Si un identificador no es único, se produce un error en tiempo de ejecución. A menudo, se asignan previamente rangos de valores para identificadores a fin de evitar conflictos entre los equipos de desarrollo independientes. Para obtener información adicional sobre EventSource, consulte el [blog de Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o [la documentación de MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

#### <a name="using-structured-eventsource-events"></a>Uso de eventos EventSource estructurados

Cada uno de los siguientes eventos se define para un caso de uso concreto, se ha registrado como ejemplo un tipo de servicio. Al tener los mensajes definidos en este modo, los datos pueden empaquetarse junto con el texto del error. Esto permite una mejor búsqueda y filtrado basados en los nombres o los valores de las propiedades especificadas. La estructuración del resultado de la instrumentación facilita su uso, pero se requiere mayor tiempo y reflexión para definir un nuevo evento para cada caso de uso. Algunas definiciones de eventos se pueden compartir en toda la aplicación; por ejemplo, el evento de inicio o detención de un método se podría reutilizar en muchos servicios dentro de una aplicación. Un servicio específico de dominio, como un sistema de pedidos, puede tener un evento CreateOrder, que tendrá su propio evento único. Este enfoque genera a menudo una gran cantidad de eventos y requiere potencialmente la coordinación de los identificadores en los equipos de proyecto. Para obtener un ejemplo completo de la estructura EventSources en Service Fabric, vea [PartyCluster.ApplicationDeployService.ServiceEventSource](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster/blob/master/src/PartyCluster.ApplicationDeployService/ServiceEventSource.cs) en el ejemplo de clúster de parte.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>Uso de EventSource genéricamente

Dado que la definición de eventos específicos puede resultar difícil, muchas personas definen algunos de los eventos con un conjunto común de parámetros que por lo general generan su información como una cadena. Gran parte del aspecto estructurado se pierde, lo que dificulta la búsqueda y el filtrado de los resultados. Con este enfoque, se definen algunos eventos que normalmente corresponden a los niveles de registro. El fragmento de código siguiente define un mensaje de error y de depuración.
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
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
El uso de un enfoque híbrido de instrumentación estructurada y genérica también puede funcionar. En este caso, la instrumentación estructurada se usa para informar sobre errores y métricas, mientras que los eventos genéricos se pueden utilizar para el registro detallado que requieren los ingenieros al solucionar problemas.

### <a name="aspnet-core-logging"></a>Registro de ASP.NET Core

La elección del modo de instrumentar el código puede ser difícil; si toma una decisión que no le satisfaga y tiene que volver a instrumentar, tendrá que volver a visitar el código base, corriendo el riesgo de que se desestabilice. Para reducir el riesgo, los desarrolladores pueden elegir una biblioteca de instrumentación como [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), que proporciona ASP.NET Core. De este modo se puede disponer de una interfaz [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) que permite el uso del proveedor de su elección al tiempo que se minimiza el impacto en el código existente. Otro aspecto interesante derivado es que el código puede utilizarse no solo en .NET Core en Windows y Linux, sino también en toda la plataforma .NET, con lo que es posible estandarizar el código de instrumentación en .NET y .NET Core.

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Uso de Microsoft.Extensions.Logging dentro de Service Fabric

1. Agregue el paquete NuGet **Microsoft.Extensions.Logging** al proyecto que va a instrumentar. También conviene agregar los paquetes de proveedor; lo haremos a continuación para un paquete de terceros. Vea [Registro de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) para obtener más información.

2. Agregue una directiva **using** para "Microsoft.Extensions.Logging" al archivo de servicio.

3. Defina una variable privada dentro de la clase de servicio.

    ```csharp
        private ILogger _logger = null;
    ```

4. En el constructor de la clase de servicio, agregue
    
    ```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
    ```

5. Inicie la instrumentación del código en los métodos. Estos son algunos ejemplos.
    
    ```csharp

        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

    ```

#### <a name="using-other-logging-providers"></a>Uso de otros proveedores de registro

Hay algunos proveedores de terceros que funcionan con este enfoque: [SeriLog](https://serilog.net/), [NLog](http://nlog-project.org/) y [loggr](https://github.com/imobile3/Loggr.Extensions.Logging) son tres ejemplos. Cada uno de ellos puede incluirse en el registro de ASP.Net Core y también puede utilizarse por separado. SeriLog tiene una característica con la que es posible enriquecer todos los mensajes enviados desde un registrador, lo que puede resultar útil para generar el nombre del servicio, el tipo y la información de la partición. Para utilizar esta funcionalidad en la infraestructura de ASP.NET Core, haga lo siguiente:

1. Agregue los paquetes de NuGet **Serilog**, **Serilog.Extensions.Logging** y **Serilog.Sinks.Observable** al proyecto. Agregue también **SeriLog.Sinks.Literate** en este ejemplo; más adelante en este mismo artículo se muestra un enfoque más adecuado.
2. Cree LoggerConfiguration y la instancia del registrador en SeriLog.

    ```csharp

        Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();

    ```

3. Agregue un argumento SeriLog.ILogger al constructor del servicio y pase el registrador recién creado.
    
    ```csharp

        ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();

    ```

4. En el constructor del servicio, agregue lo siguiente, con lo que se crean la propiedad enrichers para las propiedades ServiceTypeName, ServiceName, PartitionId y InstanceId del servicio. Se agrega también a la fábrica de registro de ASP.NET Core, por lo que Microsoft.Extensions.Logging.ILogger se puede utilizar en el código.
    
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

5. Instrumente el código igual que cuando se usa ASP.NET Core sin SeriLog.

> [!NOTE] 
> No se recomienda que el Log.Logger estático se use con este enfoque porque Service Fabric puede hospedar varias instancias del mismo tipo de servicio dentro de un único proceso. Esto significa que se mostraría el último escritor de la propiedad enrichers para todas las instancias en ejecución. Este es uno de los motivos por los cuales la variable _logger es una variable de miembro privada de la clase de servicio. Esto también significa que _logger debe estar disponible para el código común que puede utilizarse entre servicios. 

### <a name="which-one-should-i-use"></a>¿Cuál debo usar?

Si para la aplicación el rendimiento es importante, EventSource es el mejor enfoque para utilizar porque **generalmente** utiliza menos recursos y tiene un mejor rendimiento que el uso del registro de ASP.NET Core o cualquiera de las soluciones de terceros.  Esto no supone un problema para muchos servicios, pero si el servicio está orientado al rendimiento, EventSource puede ser una mejor elección. Para obtener los mismos beneficios del registro estructurado, EventSource requiere una gran inversión por parte del equipo de ingeniería. La mejor manera de determinar la elección para su proyecto es esbozar un prototipo rápido de lo que podría hacer con cada enfoque y, a continuación, elegir el enfoque que mejor se adapte a sus necesidades.

## <a name="event-and-log-collection"></a>Recopilación de registros y eventos

### <a name="azure-diagnostics"></a>Diagnóstico de Azure

Además de lo que ya ofrece la Supervisión de Azure, Azure proporciona también un medio de recopilar eventos de cada uno de los servicios en una ubicación central. Existen dos artículos que muestran cómo configurar la recopilación de eventos para [Windows](service-fabric-diagnostics-how-to-setup-wad.md) y [Linux](service-fabric-diagnostics-how-to-setup-lad.md). Estos artículos muestran la recopilación de datos de eventos y su envío a Azure Storage. Es fácil de hacer en el portal o la plantilla de Resource Manager mediante la habilitación de los diagnósticos. De este modo, se recopilan algunos orígenes de eventos generados por Service Fabric automáticamente:

- Eventos de EventSource y contadores de rendimiento cuando se usa el modelo de programación de Reliable Actor. Los eventos se enumeran en el artículo sobre [Diagnósticos y supervisión del rendimiento de Reliable Actors](service-fabric-reliable-actors-diagnostics.md).
- Eventos de EventSource cuando se usa el modelo de programación de Reliable Services. Los eventos se enumeran en el artículo sobre [Funcionalidad de diagnóstico para Reliable Services con estado](service-fabric-reliable-services-diagnostics.md)
- Los eventos del sistema se emiten como eventos ETW. Hay muchos eventos que se emiten desde Service Fabric como parte de esta categoría, e incluyen la selección de ubicación de servicio y los eventos de inicio y detención. La mejor manera de ver los eventos emitidos es usar el [visor de diagnóstico de Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) cuando se ejecuta en el equipo local. Dado que estos eventos son eventos ETW nativos, existen algunas limitaciones relativas al modo en que se pueden recopilar.
- A partir de la versión 5.4 de Service Fabric, se exponen eventos de métrica de mantenimiento y carga. Esto permite el uso de la recopilación de estos eventos para la generación de informes y alertas. Estos eventos también son eventos ETW nativos y tienen algunas limitaciones relativas al modo en que se pueden recopilar.

Cuando se configuran, estos eventos aparecen en una de las cuentas de Azure Storage creadas cuando se creó el clúster, suponiendo que el diagnóstico estuviera habilitado. Las tablas se denominan *WADServiceFabricReliableActorEventTable*, *WADServiceFabricReliableServiceEventTable* y *WADServiceFabricSystemEventTable*. Los eventos de estado no se agregan de forma predeterminada y requieren que se modifique la plantilla de Resource Manager. Consulte [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md) para obtener más información.

Estos artículos también muestran cómo obtener eventos personalizados en Azure Storage. Cualquiera de los artículos de Diagnósticos de Azure existentes sobre cómo configurar los contadores de rendimiento u otra información de supervisión desde una máquina virtual a Diagnósticos de Azure también sirven con un clúster de Service Fabric. Por ejemplo, si el almacenamiento de tablas de Azure Table Storage como destino no es lo que desea, hay un artículo sobre la [Transmisión de datos de Diagnósticos de Azure en la ruta de acceso activa mediante Event Hubs](../event-hubs/event-hubs-streaming-azure-diags-data.md). Una vez que los eventos se encuentran en la instancia de Event Hub, se pueden leer y enviar a la ubicación de su elección. También hay un artículo sobre la integración de [información de Diagnóstico de Azure con Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/).

Una de las desventajas del uso de Diagnósticos de Azure es que la configuración se lleva a cabo mediante una plantilla de Resource Manager y, por tanto, se produce solo en el nivel VMSS. Un VMSS corresponde a un tipo de nodo de Service Fabric. Es decir, debe configurar cada tipo de nodo para todas las aplicaciones y servicios que se pueden ejecutar en un nodo de ese tipo. Podría tratarse de un gran número de EventSources dependiendo del número de aplicaciones y servicios configurados. Además, debe realizarse una implementación de Resource Manager siempre que haya cambiado la configuración de cualquier aplicación. Idealmente, la configuración de supervisión irá de la mano de la configuración del servicio.

Diagnósticos de Azure solo funciona para los clústeres de Service Fabric implementados en Azure, pero sirve para los clústeres de Windows y Linux.

### <a name="eventflow"></a>EventFlow

[EventFlow se publicó por el equipo de Visual Studio](https://github.com/Azure/diagnostics-eventflow) y ofrece un mecanismo para eventos de enrutamiento desde un nodo a uno o varios destinos de supervisión. Dado que se incluye como un paquete de NuGet en el proyecto de servicio, el código y la configuración de EventFlow van junto al servicio, lo que elimina el problema de la configuración por nodo que se mencionó sobre Diagnósticos de Azure. EventFlow se ejecuta dentro de su proceso de servicio y se conecta directamente con las salidas configuradas. Debido a esta conexión directa, EventFlow funciona en Azure, en implementaciones de contenedor o locales de un servicio. Debe tener cuidado al ejecutar muchas réplicas en el mismo nodo, porque cada canalización EventFlow establece una conexión externa. Si está hospedando una gran cantidad de procesos, acabará teniendo una gran cantidad de conexiones salientes. Esto no supone un problema para las aplicaciones de Service Fabric, porque todas las réplicas de un ServiceType se ejecutan dentro del mismo proceso, limitando el número de conexiones salientes. EventFlow también proporciona el filtrado de eventos, de manera que solo se envían los eventos que coinciden con el filtro especificado. Para obtener información detallada sobre el uso de EventFlow con Service Fabric, vea [Recopilación de registros directamente desde un proceso de servicio de Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md).

> [!NOTE]
> En una futura versión de Service Fabric, habrá disponible una aplicación de host de EventSource, lo que hará posible la escucha a las entradas basadas en ETW, la recopilación de métricas a nivel de nodo y la compatibilidad con los archivos de registro graduales.

El uso de EventFlow es bastante sencillo.
1. Agregue el paquete de NuGet al proyecto de servicio.
2. En la función **Main** del servicio, cree la canalización de EventFlow y configure las salidas. En este caso, estamos mostrando el uso de SeriLog como salida.
    ```csharp

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

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }

    ```
3. Cree un archivo en la carpeta PackageRoot | Config del servicio con el nombre *eventFlowConfig.json*. Por dentro, el archivo de la configuración es similar a:
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
En la configuración hay dos entradas definidas, los dos orígenes basados en EventSource creados por Service Fabric y el EventSource para el servicio. Tenga en cuenta que los eventos de mantenimiento y nivel de sistema que usan ETW no están disponibles para EventFlow. Esto es porque se requiere un alto nivel de privilegios para realizar escuchas de un origen ETW y los servicios nunca deben ejecutarse con privilegios altos. La otra entrada es SeriLog, su configuración se realizó en el método **Main**.  También hay algunos filtros aplicados: el primero indica a EventFlow que quite todos los eventos que son de un nivel detallado. Volveremos a la otra definición de filtro en un minuto. Hay también dos salidas configuradas: una es la estándar, que escribirá en la ventana de salida dentro de Visual Studio. La otra salida es ApplicationInsights, no olvide agregar la clave de instrumentación.

4. El último paso es instrumentar el código. En este ejemplo, vamos a instrumentar RunAsync de distintas maneras como ejemplo. En el código siguiente, todavía estamos usando SeriLog y parte de la sintaxis que se utiliza es específica de SeriLog. Tenga en cuenta las capacidades específicas para la solución de registro que elija. Hay tres eventos generados, un evento de nivel de depuración y dos informativos; el segundo es el que hace el seguimiento de la duración de la solicitud. Con la configuración de EventFlow anterior, el evento del nivel de depuración no debería ir hasta la salida.

    ```csharp
        Stopwatch sw = Stopwatch.StartNew();

        while (true)
        {
            cancellationToken.ThrowIfCancellationRequested();

            sw.Restart();

            // Delay a random interval to provide a more interesting request duration.
            await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

            ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
            _logger.LogDebug("Debug level event from Microsoft.Logging");
            _logger.LogInformation("Informational level event from Microsoft.Logging");
            _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
        }
    ```

Para ver los eventos en Application Insights, abra Azure Portal y navegue al recurso ApplicationInsights. A continuación, haga clic en "Buscar" en la parte superior izquierda para que se muestren los eventos.

![Vista de los eventos en Buscar de Application Insights](./media/service-fabric-diagnostics-overview/ai-search-events.png)

Los seguimientos están en la parte inferior de la imagen. Puede ver que contamos solo con dos eventos, el evento de nivel de depuración se quitó por EventFlow. Entonces, ¿qué es la entrada de solicitud sobre el seguimiento? Es la tercera línea de instrumentación de "_logger" que muestra que el evento se tradujo en una métrica de solicitud en Application Insights. Volvamos a la definición de filtro, donde el tipo es "metadata". Esto declara que un evento tiene una propiedad de "RequestName" con el valor "MyRequest" y que otra propiedad, "Duration", contiene la duración de la solicitud en milisegundos. Esto es lo que se ve en el evento de solicitud en Application Insights. El mismo enfoque sirve con cualquiera de las entradas de EventFlow compatibles, como EventSource.

Si el clúster es independiente y no se puede conectar a una solución basada en la nube por razones de directiva, EventFlow admite la Búsqueda elástica como salida, pero se pueden escribir otras salidas y se aconsejan las solicitudes de incorporación de cambios. Algunos de los proveedores de terceros para el registro de ASP.NET Core también tienen soluciones que admiten instalaciones locales.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Informes de carga y mantenimiento de Azure Service Fabric

Service Fabric tiene su propio modelo de mantenimiento que se describe en detalle en algunos artículos.
- [Introducción a la supervisión del mantenimiento de Service Fabric](service-fabric-health-introduction.md)
- [Notificación y comprobación del estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Incorporación de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)
- [Vista de los informes de estado de Service Fabric](service-fabric-view-entities-aggregated-health.md)

La supervisión de mantenimiento es fundamental para diversos aspectos de la operación de un servicio. Lo más importante es que cuando Service Fabric está realizando una actualización de la aplicación con nombre, después de que cada dominio de actualización del servicio se haya actualizado y esté disponible para los clientes, deben pasarse comprobaciones de estado antes de seguir con el dominio de actualización posterior. Si no se puede alcanzar un estado correcto, la implementación se revierte dejando la aplicación en un estado correcto conocido. Aunque algunos clientes resultaron afectados antes de que los servicios se pudieran revertir, la mayoría de los clientes no experimentaron ningún problema. Además, la resolución se produjo de forma relativamente rápida y sin necesidad de esperar a la acción de un operador humano. Cuantas más comprobaciones de mantenimiento se integren en el código, más resistente será el servicio a los problemas de implementación.

Otro aspecto del estado del servicio es la notificación de métricas desde el servicio. Las métricas son importantes en Service Fabric porque se usan para equilibrar el uso de recursos y se pueden usar como un indicador del estado del sistema. Supongamos que la aplicación contiene muchos servicios y que cada instancia informa sobre una métrica de solicitudes por segundo. Si uno de los servicios está usando más recursos que otro, Service Fabric mueve instancias de servicio en el clúster intentando mantener una utilización de recursos uniforme. El artículo [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md) proporciona una explicación detallada sobre cómo funciona este proceso.

Las métricas también proporcionan una visión general del rendimiento del servicio y se pueden usar con el tiempo para comprobar que el servicio está funcionando en los parámetros esperados. Por ejemplo, si nos basamos en las tendencias, a las 9 de la mañana del lunes, el promedio de solicitudes por segundo es de 1000, así que puede configurar un informe de mantenimiento que emita una alerta si las solicitudes por segundo están por debajo de 500 o por encima de 1500. Todo puede estar funcionando correctamente, pero quizá valga la pena echar un vistazo para asegurarse de que los clientes están disfrutando de una gran experiencia. El servicio puede definir un conjunto de métricas que se pueden notificar para fines de mantenimiento, pero para que el equilibrio de recursos del clúster no resulte afectado, establezca la ponderación de métricas en cero. Se recomienda que inicie toda la métrica con una ponderación de cero y que no aumente tal ponderación hasta que sepa con certeza la repercusión que tiene en el equilibrio de recursos para el clúster.

> [!TIP]
> No es necesario que pondere demasiadas métricas, puede ser difícil entender por qué las instancias de servicio se están moviendo, y con solo algunas métricas ya puede conseguir buenos resultados.

Los candidatos para informes de mantenimiento y métricas pueden ser cualquiera que indique el estado y el rendimiento de la aplicación. Un contador de rendimiento de la CPU puede indicar cómo se está usando el nodo, pero no indica realmente si un servicio determinado está o no en buen estado porque pueden estar ejecutándose varios servicios en un solo nodo. Por otro lado, métricas como las solicitudes por segundo, los elementos procesados o la latencia de solicitud pueden indicar el estado de un servicio determinado.

Para notificar el estado, agregue código como el siguiente:
```csharp
        if (!result.HasValue)
        {
            HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
            this.Partition.ReportInstanceHealth(healthInformation);
        }
```

Para notificar una métrica, agregue código como el siguiente a su servicio:
```csharp
        this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>Guardianes

Un guardián es un servicio independiente que puede vigilar el estado y la carga entre los servicios e informar sobre el estado de cualquier componente en la jerarquía de modelo de mantenimiento. Esto puede ayudar a evitar errores que se pasan por alto con la vista de un solo servicio. Los guardianes sirven también para hospedar el código que puede llevar a cabo acciones correctoras para condiciones conocidas sin necesidad de intervención humana.

## <a name="visualization-analysis-and-alerting"></a>Visualización, análisis y alertas

La última parte de la supervisión es la visualización del flujo de eventos, la generación de informes sobre el rendimiento del servicio y la emisión de alertas cuando se detecta un problema. Hay numerosas soluciones que se usan para este aspecto de supervisión, Application Insights y OMS pueden utilizarse para emitir alertas basadas en el flujo de eventos. Power BI o una solución de terceros, como [Kibana](https://www.elastic.co/products/kibana) o [Splunk](https://www.splunk.com/), pueden utilizarse para visualizar los datos.

## <a name="next-steps"></a>Pasos siguientes

* [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Recopilación de registros directamente desde un proceso de servicio de Azure Service Fabric](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO3-->


