<properties
   pageTitle="Información general sobre la comunicación de Reliable Services | Microsoft Azure"
   description="Información general sobre el modelo de comunicación de Reliable Services, incluidos los agentes de escucha de apertura en los servicios, la resolución de puntos de conexión y la comunicación entre servicios."
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
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Uso de las API de comunicación de Reliable Services

Azure Service Fabric como una plataforma es completamente independiente de la comunicación entre los servicios. Todos los protocolos y las pilas son aceptables, desde UDP hasta HTTP. El desarrollador del servicio es quien debe elegir cómo deberían comunicarse los servicios. El marco de trabajo de aplicaciones de Reliable Services ofrece pilas de comunicación integradas, además de varias API que puede usar para compilar los componentes de comunicación personalizados.

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

Luego puede agregar su implementación del agente de escucha de comunicación devolviéndolo en una invalidación del método de clase basado en el servicio.

Para servicios sin estado:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Para servicios con estado:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

En ambos casos, devuelve una colección de agentes de escucha. Esto permite que el servicio escuche en varios puntos de conexión, que posiblemente usen distintos protocolos, mediante el uso de varios agentes de escucha. Por ejemplo, puede tener un agente de escucha HTTP y un agente de escucha de WebSocket independiente. Cada agente de escucha recibe un nombre y la colección resultante del *nombre: los pares de direcciones* se representan como un objeto JSON cuando un cliente solicita las direcciones de escucha para una partición o instancia de servicio.

En un servicio sin estado, la invalidación devuelve una colección de ServiceInstanceListeners. Un ServiceInstanceListener contiene una función para crear un ICommunicationListener y le da un nombre. Para servicios con estado, la invalidación devuelve una colección de ServiceReplicaListeners. Difiere ligeramente de su homólogo sin estado, porque ServiceReplicaListener tiene una opción de abrir un ICommunicationListener en las réplicas secundarias. No solo puede usar varios agentes de escucha de comunicación en un servicio, sino también especificar cuáles aceptan solicitudes en réplicas secundarias y cuáles escuchan solo en las réplicas principales.

Por ejemplo, puede tener una clase ServiceRemotingListener que realice llamadas RPC solo en las réplicas principales, y un segundo agente de escucha personalizado que lea solicitudes en réplicas secundarias a través de HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Cuando se crean varios agentes de escucha para un servicio, se **debe** asignar a cada uno un nombre único.

Por último, describa los puntos de conexión que se requieren para el servicio en el [manifiesto de servicio](service-fabric-application-model.md) en la sección que trata sobre los puntos de conexión.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

El agente de escucha de comunicación puede acceder a los recursos de puntos de conexión asignados a él desde `CodePackageActivationContext` en `ServiceContext`. El agente de escucha luego puede empezar a escuchar las solicitudes cuando se abre.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Los recursos de los puntos de conexión son comunes para el paquete de servicio completo y los asigna Service Fabric cuando se activa el paquete de servicio. Es posible que varias réplicas hospedadas en el mismo ServiceHost compartan el mismo puerto. Esto significa que el agente de escucha de comunicación debe admitir el uso compartido de puertos. La manera recomendada de hacerlo es que el agente de escucha de comunicación utilice el identificador de partición y el identificador de instancia o de réplica cuando genera la dirección de escucha.

### Registro de dirección de servicio

Un servicio del sistema denominado *servicio de nomenclatura* se ejecuta en clústeres de Service Fabric. El servicio de nomenclatura es un registrador de los servicios y sus direcciones que cada instancia o réplica del servicio escucha. Cuando el método `OpenAsync` de una interfaz `ICommunicationListener` se completa, el valor devuelto se registra en el servicio de nomenclatura. Este valor devuelto que se publica en el servicio de nomenclatura es una cadena que puede no tener ningún valor. Este valor de cadena es lo que verán los clientes cuando soliciten una dirección para el servicio desde el servicio de nomenclatura.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Service Fabric ofrece varias API que permiten que los clientes y otros servicios soliciten esta dirección por nombre de servicio. Esto es importante porque la dirección del servicio no es estática. Los servicios se mueven en el clúster para fines de disponibilidad y equilibrio de recursos. Este es el mecanismo que permite a los clientes resolver la dirección de escucha de un servicio.

> [AZURE.NOTE] Para obtener un tutorial completo sobre cómo escribir una interfaz `ICommunicationListener`, consulte [Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN](service-fabric-reliable-services-communication-webapi.md).

## Comunicación con un servicio
La API de Reliable Services proporciona las bibliotecas siguientes para la escritura de clientes que se comunican con los servicios.

### Resolución del punto de conexión de servicio
El primer paso para la comunicación con un servicio consiste en resolver una dirección de punto de conexión de la partición o la instancia del servicio con el que quiere comunicarse. La clase de utilidad `ServicePartitionResolver` es un tipo primitivo básico que ayuda a los clientes a determinar el punto de conexión de un servicio en tiempo de ejecución. En la terminología de Service Fabric, el proceso de determinar el punto de conexión de un servicio se denomina *resolución de punto de conexión de servicio*.

Para conectarse a servicios en un mismo clúster, se puede crear `ServicePartitionResolver` con la configuración predeterminada. Este es el uso recomendado para la mayoría de las situaciones:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Para conectarse a servicios en otro clúster, se puede crear `ServicePartitionResolver` con un conjunto de puntos de conexión de puerta de enlace del clúster. Tenga en cuenta que los puntos de conexión de puerta de enlace son solo distintos puntos de conexión para conectarse al mismo clúster. Por ejemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Como alternativa, puede asignarse una función a `ServicePartitionResolver` para crear un objeto `FabricClient` para uso interno:
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient` es el objeto que se utiliza para comunicarse con el clúster de Service Fabric para realizar diversas operaciones de administración en el clúster. Esto resulta útil cuando se desea tener más control sobre cómo interactúa `ServicePartitionResolver` con el clúster. `FabricClient` realiza el almacenamiento en caché internamente y, por lo general, su creación es más costosa, por lo que es importante reutilizar instancias de `FabricClient` tantas veces como sea posible.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Luego se usa un método de resolución para recuperar la dirección de un servicio o una partición de servicio en el caso de los servicios con particiones.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Una dirección de servicio se puede resolver fácilmente con una clase `ServicePartitionResolver`, pero se requiere más trabajo para garantizar que la dirección resuelta puede usarse correctamente. El cliente tendrá que detectar si el intento de conexión no se realizó debido a un error transitorio y se puede volver a intentar (por ejemplo, el servicio se movió o no está disponible temporalmente) o se debe a un error permanente (por ejemplo, el servicio se eliminó o el recurso solicitado ya no existe). Las instancias o réplicas de servicio pueden moverse siempre de un nodo a otro por varios motivos. La dirección de servicio que se resuelve a través de `ServicePartitionResolver` puede estar obsoleta en el momento en que el código de cliente intenta conectarse. En ese caso, el cliente tendrá que volver a resolver nuevamente la dirección. Al proporcionar la clase `ResolvedServicePartition` anterior, se indica que es necesario volver a intentar la resolución en lugar de simplemente recuperar una dirección en caché.

Normalmente, el código de cliente no tiene que trabajar directamente con `ServicePartitionResolver`. Se crea y se pasa a otras fábricas de cliente de comunicación en la API de Reliable Services. Las fábricas usan la resolución internamente para generar un objeto de cliente que puede utilizarse para comunicarse con servicios.

### Fábricas y clientes de comunicación

La biblioteca de fábrica de comunicación implementa un patrón de reintento típico de control de errores que simplifica el reintento de conexiones en puntos de conexión de servicio resueltos. La biblioteca de fábrica proporciona el mecanismo de reintento, mientras que usted proporciona los controladores de errores.

`ICommunicationClientFactory` define la interfaz base que implementa una fábrica de cliente de comunicación que genera clientes que pueden comunicarse con un servicio de Service Fabric. La implementación de CommunicationClientFactory depende de la pila de comunicación que el servicio de Service Fabric utiliza donde el cliente desea comunicarse. La API de Reliable Services ofrece una instancia de `CommunicationClientFactoryBase<TCommunicationClient>`. Esto proporciona una implementación base de la interfaz `ICommunicationClientFactory` y realiza tareas comunes a todas las pilas de comunicación. (Estas tareas incluyen el uso de `ServicePartitionResolver` para determinar el punto de conexión de servicio). Normalmente, los clientes implementan la clase CommunicationClientFactoryBase abstracta para controlar la lógica específica de la pila de comunicación.

El cliente de comunicación solo recibe una dirección y la usa para conectarse a un servicio. El cliente puede utilizar el protocolo que quiera.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

La fábrica de cliente es la principal responsable de crear clientes de comunicación. En el caso de los clientes que no mantienen una conexión persistente, como un cliente HTTP, la fábrica solo tiene que crear y devolver el cliente. La fábrica también debe validar otros protocolos que mantienen una conexión persistente, como algunos de los protocolos binarios, para determinar si la conexión debe volver a crearse.

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Por último, un controlador de excepciones es responsable de determinar la acción que hay que realizar cuando se produce una excepción. Las excepciones se dividen en dos categorías, las que **pueden reintentarse** y las que **no pueden reintentarse**.

 - Las excepciones que **no pueden reintentarse** simplemente se reenvían al autor de la llamada.
 - Las excepciones que **pueden reintentarse** se clasifican a su vez en **transitorias** y **no transitorias**.
  - Las excepciones **transitorias** son las que pueden reintentarse sin volver a resolver la dirección del punto de conexión de servicio. Entre estas últimas se incluyen los problemas transitorios de red o las respuestas de error de servicio que no sean las que indican que la dirección de punto de conexión de servicio no existe.
  - Las excepciones **no transitorias** son las que requieren que se vuelva a resolver la dirección de punto de conexión de servicio. Entre estas se incluyen las excepciones que indican que no se pudo llegar al punto de conexión de servicio, lo que indica que el servicio se movió a otro nodo.

El método `TryHandleException` toma una decisión sobre una excepción determinada. Si **no sabe** qué decisiones tomar sobre una excepción, debe devolver **false**. Cuando **sí sabe** qué decisión tomar, debe establecer el resultado según corresponda y devolver **true**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### Resumen
Con `ICommunicationClient`, `ICommunicationClientFactory` e `IExceptionHandler` creadas en torno a un protocolo de comunicaciones, `ServicePartitionClient` lo encapsula todo y proporciona el bucle de resolución de direcciones de partición de servicio y control de errores en torno a estos componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## Pasos siguientes
 - Vea un ejemplo de comunicación HTTP entre los servicios de un [proyecto de ejemplo en GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Llamadas a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)

 - [API web que usa OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicación WCF con la utilización de Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0713_2016-->