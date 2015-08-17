<properties
   pageTitle="Pila de comunicaciones predeterminada proporcionada por Service Fabric"
   description="Este artículo describe la pila de comunicaciones predeterminada proporcionada por el marco del servicio fiable para que se comuniquen los servicios y clientes."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Pila de comunicaciones predeterminada proporcionada por el marco de servicios de confianza
Los autores de servicio que no están vinculados a una implementación concreta de la pila de comunicaciones (WebAPI, WCF, etc.), el marco de trabajo proporciona comunicaciones de cliente y servicio que pueden utilizarse para configurar la comunicación entre el servicio y el cliente.

> [AZURE.NOTE]Actualice a los últimos paquetes de nuget para obtener las características que se mencionan a continuación.

## Agente de escucha de comunicación de servicio
El agente de escucha de comunicaciones predeterminado del servicio se implementa en la clase `ServiceCommunicationListener`

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
Los métodos que implementa el servicio y desea exponer a sus clientes se definen como métodos asincrónicos en una interfaz que hereda de la interfaz `IService`. A continuación, el servicio puede simplemente crear una instancia del objeto `ServiceCommunicationListener` y devolverla en el [`CreateCommunicationListener` método](service-fabric-reliable-services-communication.md). Por ejemplo, el código del servicio HelloWorld para configurar esta pila de comunicación puede definirse del modo siguiente.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]Los argumentos y los tipos de valor devueltos en la interfaz de servicio, por ejemplo, la clase de mensaje anterior, se espera que sean serializables por .net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).


## Escritura de los clientes para comunicarse con ServiceCommunicationListener
Para que los clientes se comuniquen con los servicios mediante `ServiceCommunicationListener`, el marco de trabajo proporciona una clase `ServiceProxy`.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

Los clientes pueden crear instancias de un objeto de proxy de servicio que implementa la interfaz de servicio correspondiente e invoca métodos en el objeto proxy.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]El marco de trabajo de comunicación se encarga de propagar las excepciones producidas en el servicio al cliente. Por lo tanto, la lógica de control en el cliente mediante ServiceProxy puede controlar excepciones directamente que potencialmente puede iniciar el servicio.
 

<!---HONumber=August15_HO6-->