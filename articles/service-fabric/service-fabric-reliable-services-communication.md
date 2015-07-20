<properties
   pageTitle="Información general del modelo de comunicación de servicio"
   description="Este artículo describe los aspectos básicos del modelo de comunicación compatibles con la api del servicio fiable."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Modelo de comunicación de servicio

El modelo de programación de servicios de confianza permite a los autores de servicio especificar el mecanismo de comunicación que desean utilizar para exponer los extremos de servicio y también proporciona abstracciones que los clientes pueden utilizar para detectar y comunicarse con el extremo de servicio.

## Configuración de la pila de comunicaciones de servicio

La API de servicios confiables permite a los autores de servicio conectar la pila de comunicaciones de su elección en el servicio implementando el método siguiente en su servicio,

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

La interfaz `ICommunicationListener` define la interfaz que se debe ser implementada por el agente de escucha de comunicación para un servicio.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
En la sección Extremos se describen los extremos que son necesarios para el servicio a través del [Manifiesto del servicio](service-fabric-application-model.md).

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

> [AZURE.NOTE]Los recursos de los extremos son comunes para el paquete de servicio completo y son asignados por Service Fabric cuando se activa el paquete de servicio. (Consulte [Service Fabric ServiceModel](../service-fabric-service-model.md) para obtener más detalles). Por ello, las réplicas alojadas en el mismo ServiceHost comparten el mismo puerto. Esto significa que el agente de escucha de comunicación debe admitir el uso compartido de puertos. La manera recomendada de hacerlo es que el agente de escucha de comunicación utilice el Id. de partición y el Id. de instancia o de réplica cuando se genera la dirección de escucha.

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

## Comunicación entre cliente y servicio
La API de servicios fiables proporciona las abstracciones siguientes que permiten facilitar la escritura de clientes para comunicarse con servicios.

## ServicePartitionResolver
La clase ServicePartitionResolver ayuda al cliente a determinar el extremo de un Service Fabric en tiempo de ejecución.

> [AZURE.TIP]El proceso de determinar el extremo de un servicio se denomina Resolución de extremos de servicio en la terminología de Service Fabric.

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

## CommunicationClientFactory
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
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
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

Un patrón de uso típico tendría este aspecto,

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
* [Pila de comunicaciones predeterminada proporcionada por el marco de servicios de confianza](service-fabric-reliable-services-communication-default.md)

* [Pila de comunicaciones basada en WCF proporcionada por el marco de servicios de confianza](service-fabric-reliable-services-communication-wcf.md)

* [Escribir un servicio mediante la API de servicios fiables que usa la pila de comunicación de WebAPI](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=July15_HO2-->