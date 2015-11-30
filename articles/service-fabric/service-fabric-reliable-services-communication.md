<properties
   pageTitle="Información general de la comunicación de Reliable Services | Microsoft Azure"
   description="Información general sobre el modelo de comunicación de Reliable Services, incluidos los agentes de escucha de apertura en los servicios, resolución de puntos de conexión y comunicación entre servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Modelo de comunicación de Reliable Services

Service Fabric como una plataforma es completamente independiente de la comunicación entre los servicios. Todas y cada una de las pilas y de los protocolos son válidos, desde UDP hasta HTTP. El desarrollador del servicio es quien debe elegir cómo deberían comunicarse los servicios. El marco de aplicación de Reliable Services ofrece algunas pilas de comunicación pregeneradas así como herramientas para lanzar su pila de comunicación personalizada, como abstracciones que los clientes pueden usar para detectar y comunicarse con puntos de conexión de servicio.

## Configuración de la comunicación del servicio

La API de Reliable Services usa una interfaz simple para la comunicación del servicio. Para abrir un punto de conexión para el servicio, solo tiene que implementar esta interfaz:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Después, para agregar su implementación del agente de escucha de comunicación, devuélvalo en una invalidación del método de clase base de servicio.

Para sin estado:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Para con estado:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

En ambos casos devuelve una colección de agentes de escucha que permite que su servicio use con facilidad varios agentes de escucha: por ejemplo, puede tener un agente de escucha HTTP y un agente de escucha de WebSocket independiente. Cada agente de escucha recibe un nombre y la colección resultante del nombre: los pares de direcciones se representan como objeto JSON cuando un cliente solicita las direcciones de escucha para una partición o instancia de servicio.

En un servicio sin estado, la invalidación devuelve una colección de ServiceInstanceListeners. Un ServiceInstanceListener simplemente contiene una función para crear su ICommunicationListener y le da un nombre. Para servicios con estado, la invalidación devuelve una colección de ServiceReplicaListeners. Esto es algo diferente de su homólogo sin estado, porque ServiceReplicaListener tiene una opción de abrir un ICommunicationListener en las réplicas secundarias. Esto le permite no solo usar varios agentes de escucha de comunicación en un servicio, sino también especificar cuáles aceptan solicitudes en réplicas secundarias y cuáles escuchan solo en las réplicas principales.

Por ejemplo, podemos hacer que un ServiceRemotingListener realice llamadas RPC solo en las principales y un segundo agente de escucha personalizado que lea solicitudes en réplicas secundarias:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Por último, describa los puntos de conexión que son necesarios para el servicio en el [Manifiesto del servicio](service-fabric-application-model.md) en la sección Puntos de conexión.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

El agente de escucha de comunicación puede tener acceso a los recursos de extremo asignados a él desde `CodePackageActivationContext` en `ServiceInitializationParameters` y empezar a escuchar las solicitudes cuando se abre.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Los recursos de los extremos son comunes para el paquete de servicio completo y son asignados por Service Fabric cuando se activa el paquete de servicio. Por ello, las réplicas alojadas en el mismo ServiceHost comparten el mismo puerto. Esto significa que el agente de escucha de comunicación debe admitir el uso compartido de puertos. La manera recomendada de hacerlo es que el agente de escucha de comunicación utilice el Id. de partición y el Id. de instancia o de réplica cuando se genera la dirección de escucha.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

Para un tutorial completo de la creación de `ICommunicationListener`, vea [Introducción a los servicios de la API web de Service Fabric de Microsoft Azure con el autohospedaje de OWIN](service-fabric-reliable-services-communication-webapi.md).

## Comunicación entre cliente y servicio
La API de servicios fiables proporciona las abstracciones siguientes que permiten facilitar la escritura de clientes para comunicarse con servicios.

### ServicePartitionResolver
Esta clase de utilidad ayuda a los clientes a determinar el punto de conexión de un Service Fabric en tiempo de ejecución. El proceso de determinar el extremo de un servicio se denomina Resolución de extremos de servicio en la terminología de Service Fabric.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient es el objeto que se utiliza para comunicarse con el clúster de Service Fabric para realizar diversas operaciones de administración en el clúster de Service Fabric.

Normalmente, el código de cliente no necesita trabajar con `ServicePartitionResolver` directamente. Se crea y se pasa a otras clases auxiliares en la API del servicio fiable, que internamente utiliza el solucionador y ayuda al cliente a comunicarse con el servicio.

### CommunicationClientFactory
`ICommunicationClientFactory` define la interfaz base que implementa un generador de cliente de comunicación que genera clientes que pueden comunicarse con un servicio de ServiceFabric. La implementación de CommunicationClientFactory dependerá de la pila de comunicación utilizada por Service Fabric con el que el cliente desea comunicarse. La API del servicio fiable proporciona un CommunicationClientFactoryBase<TCommunicationClient> que proporciona una implementación base de esta interfaz `ICommunicationClientFactory` y realiza las tareas que son comunes para todas las pilas de comunicación (como usar un `ServicePartitionResolver` para determinar el extremo de servicio). Normalmente, los clientes implementan la clase CommunicationClientFactoryBase abstracta para controlar la lógica específica de la pila de comunicación.

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

### ServicePartitionClient
`ServicePartitionClient` utiliza la CommunicationClientFactory (e internamente el ServicePartitionResolver) y ayuda en la comunicación con el servicio mediante el control de reintentos de comunicación comunes y excepciones transitorias de Service Fabric.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Un patrón de uso típico tendría este aspecto:

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## Pasos siguientes
* [Llamada a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicación WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)

 

<!---HONumber=Nov15_HO4-->