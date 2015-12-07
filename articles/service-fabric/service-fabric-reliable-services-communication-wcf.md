<properties
   pageTitle="Pila comunicación de WCF de Reliable Services | Microsoft Azure"
   description="La pila de comunicación de WCF integrada en Services Fabric ofrece comunicación de WCF del servicio de cliente para Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# Pila de comunicación basada en WCF de servicios fiables
El marco de servicios de confianza permite a los autores de servicio decidir la pila de comunicación que desean usar para su servicio. Pueden conectar la pila de comunicaciones que deseen mediante el `ICommunicationListener` devuelto desde los métodos [CreateServiceReplicaListeners o CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md). El marco de trabajo proporciona una implementación basada en WCF de la pila de comunicación para los autores de servicio que desean utilizar la comunicación basada en WCF.

## Agente de escucha de comunicación WCF
La implementación específica de WCF de `ICommunicationListener` es proporcionada por la clase `Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener`.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## Escritura de clientes para la pila de comunicación de WCF
Para que los clientes de escritura se comuniquen con los servicios mediante WCF, el marco de trabajo proporciona `WcfClientCommunicationFactory`, que es la implementación específica de WCF de [`ClientCommunicationFactoryBase`](service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Es posible acceder al canal de comunicación de WCF desde el `WcfCommunicationClient` creado por el `WcfCommunicationClientFactory`

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

El código de cliente puede utilizar el `WcfCommunicationClientFactory` junto con el `ServicePartitionClient` para determinar el extremo de servicio y comunicarse con el servicio.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```
 
## Pasos siguientes
* [Llamada a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_1125_2015-->