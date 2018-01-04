---
title: "Comunicación remota de servicio en Service Fabric | Microsoft Docs"
description: "La comunicación remota de Service Fabric permite a los clientes y servicios comunicarse con los servicios mediante la llamada a procedimiento remoto."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: df4a86e3de87daad22646672f278c7f3226660c6
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicación remota de servicio con Reliable Services
Para los servicios que no están vinculados a una pila o un protocolo de comunicación concretos, como WebAPI, Windows Communication Foundation (WCF) u otros, el marco de trabajo de Reliable Services ofrece un mecanismo de comunicación remota para configurar de manera rápida y sencilla una llamada a procedimiento remoto para servicios.

## <a name="set-up-remoting-on-a-service"></a>Configuración de la comunicación remota en un servicio
La configuración de la comunicación remota para un servicio se realiza en dos sencillos pasos:

1. Cree una interfaz para que la implemente su servicio. Esta interfaz define los métodos que están disponibles para la llamada a procedimiento remoto en el servicio. Los métodos deben ser métodos asincrónicos que devuelven tareas. La interfaz debe implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para indicar que el servicio tiene una interfaz de comunicación remota.
2. Utilice un agente de escucha de comunicación remota en su servicio. RemotingListener es una implementación de `ICommunicationListener` que ofrece funcionalidades de comunicación remota. El espacio de nombres `Microsoft.ServiceFabric.Services.Remoting.Runtime` contiene un método de extensión, `CreateServiceRemotingListener`, para los servicios con y sin estado que pueden utilizarse para crear un agente de escucha de comunicación remota mediante el protocolo de transporte de comunicación remota predeterminado.

>[!NOTE]
>El espacio de nombres `Remoting` está disponible como paquete NuGet independiente denominado `Microsoft.ServiceFabric.Services.Remoting`

Por ejemplo, el siguiente servicio sin estado expone un método único para obtener "Hello World" a través de la llamada a procedimiento remoto:

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> Los argumentos y los tipos devueltos en la interfaz de servicio pueden ser cualquier tipo simple, complejo o personalizado, pero deben ser serializables por .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).
>
>

## <a name="call-remote-service-methods"></a>Llamadas a métodos de servicio remoto
La llamada a métodos en un servicio con la pila de comunicación remota se realiza mediante un proxy local al servicio a través de la clase `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . El método `ServiceProxy` crea un proxy local con la misma interfaz que implementa el servicio. Con ese proxy, puede llamar a los métodos en la interfaz de forma remota.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

El marco de trabajo de comunicación remota propaga las excepciones generadas por el servicio al cliente. Como resultado, al usar `ServiceProxy`, el cliente es responsable de controlar las excepciones generadas por el servicio.

## <a name="service-proxy-lifetime"></a>Duración del proxy de servicio
La creación de ServiceProxy es una operación ligera, por lo que los usuarios pueden crearla todas las veces que lo necesiten. Las instancias de ServiceProxy pueden volver a usarse siempre que los usuarios lo necesiten. Si una llamada a procedimiento remoto inicia una excepción, los usuarios pueden reutilizar la misma instancia de proxy. Cada ServiceProxy contiene un cliente de comunicación que se usa para enviar mensajes a través de la conexión. Mientras se invocan llamadas remotas, internamente se comprueba si el cliente de comunicación es válido. En función de ese resultado, volvemos a crear el cliente de comunicación si se necesita. Por lo tanto, si se produce la excepción, los usuarios no tienen que volver a crear `ServiceProxy` porque se realiza de manera transparente.

### <a name="serviceproxyfactory-lifetime"></a>Duración de ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) es una fábrica que crea instancias de proxy para interfaces remotas diferentes. Si usa la API `ServiceProxy.Create` para crear el proxy, el marco crea un singleton ServiceProxy.
Es útil crear uno manualmente cuando necesite invalidar las propiedades [IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
La creación de fábricas es una operación costosa. ServiceProxyFactory mantiene una memoria caché interna del cliente de comunicación.
El procedimiento recomendado consiste en almacenar en caché ServiceProxyFactory tanto como sea posible.

## <a name="remoting-exception-handling"></a>Control de excepciones remota
Todas las excepciones remotas generadas por la API de servicio se vuelven a enviar al cliente como AggregateException. RemoteExceptions debe ser serializable con DataContract; en caso contrario, la API del proxy genera [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) con el error de serialización en ella.

ServiceProxy controla todas las excepciones de conmutación por error para la partición de servicio para la que se crea. Vuelve a resolver los puntos de conexión si existen excepciones de conmutación por error (excepciones no transitorias) y recupera la llamada con el punto de conexión correcto. El número de reintentos para las excepciones de conmutación por error es indefinido.
Si se producen excepciones transitorias, el proxy vuelve a intentar la llamada.

[OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) proporciona los parámetros de reintento predeterminados.
El usuario puede configurar estos valores pasando el objeto OperationRetrySettings al constructor ServiceProxyFactory.
## <a name="how-to-use-remoting-v2-stack"></a>Cómo usar la pila de comunicación remota V2
Con el paquete de comunicación remota de NuGet 2.8, tiene la opción para usar la pila de comunicación remota V2. La pila de comunicación remota V2 tiene un mejor rendimiento y proporciona características como serialización personalizada y más API que puedan conectarse.
De manera predeterminada, si no hace los siguientes cambios, continúa usando la pila de comunicación remota V1.
La comunicación remota V2 no es compatible con V1 (la pila de comunicación remota anterior), por lo que deberá seguir el artículo a continuación para saber cómo actualizar de V1 a V2 sin afectar a la disponibilidad del servicio.

### <a name="using-assembly-attribute-to-use-v2-stack"></a>Uso del atributo de ensamblado para utilizar la pila V2

Estos son los pasos que debe seguir para cambiar a la pila V2.

1. Agregue un recurso de punto de conexión con nombre como "ServiceEndpointV2" en el manifiesto de servicio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2.  Use el método de extensión de comunicación remota para crear el agente de escucha de comunicación remota.

  ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
  ```

3.  Agregue el atributo de ensamblado en las interfaces de comunicación remota.

  ```csharp
  [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
  ```
No se requiere ningún cambio en el proyecto de cliente.
Compile el ensamblado de cliente con el ensamblado de la interfaz, para asegurarse de que el atributo de ensamblado anterior se está usando.

### <a name="using-explicit-v2-classes-to-create-listener-clientfactory"></a>Uso de las clases explícitas de V2 para crear el agente de escucha/ClientFactory
Estos son los pasos que debe seguir.
1.  Agregue un recurso de punto de conexión con nombre como "ServiceEndpointV2" en el manifiesto de servicio.

  ```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />  
    </Endpoints>
  </Resources>
  ```

2. Use el [agente de escucha de comunicación remota V2](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingistener?view=azure-dotnet). El nombre predeterminado del recurso de extremo de servicio que se usa es "ServiceEndpointV2" y debe definirse en el manifiesto de servicio.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
  ```

3. Use [ClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) V2.
  ```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
  ```

## <a name="how-to-upgrade-from-remoting-v1-to-remoting-v2"></a>Cómo actualizar de la comunicación remota V1 a la comunicación remota V2
Con el fin de actualizar de V1 a V2, se requieren actualizaciones de dos pasos. Los pasos siguientes deben ejecutarse en el orden indicado.

1. Actualice el servicio V1 al servicio V2 mediante el uso de atributos CompactListener.
Este cambio se asegura de que el servicio escuche en el agente de escucha V1 y V2.

    a) Agregue un recurso de punto de conexión con nombre como "ServiceEndpointV2" en el manifiesto de servicio.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b) Use el método de extensión para crear el agente de escucha de comunicación remota.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c) Agregue el atributo de ensamblado en las interfaces de comunicación remota para utilizar CompactListener y el cliente V2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.CompatListener, RemotingClient = RemotingClient.V2Client)]

      ```
2. Actualice el cliente V1 al cliente V2 mediante el atributo de cliente V2.
Este paso se asegura de que el cliente use la pila V2.
No se requiere ningún cambio en el proyecto de cliente o el servicio. Alcanza con compilar los proyectos de cliente con el ensamblado de interfaz actualizado.

3. Este paso es opcional. Utilice el atributo V2Listener y, a continuación, actualice el servicio V2.
Este paso se asegura de que el servicio escuche solo en el agente de escucha V2.

```csharp
[assembly: FabricTransportServiceRemotingProvider(RemotingListener = RemotingListener.V2Listener, RemotingClient = RemotingClient.V2Client)]
```

## <a name="how-to-use-custom-serialization-with-remoting-v2"></a>Cómo usar la serialización personalizada con la comunicación remota V2
En el ejemplo siguiente se utiliza la serialización JSON con la comunicación remota V2.
1. Implemente la interfaz IServiceRemotingMessageSerializationProvider para proporcionar la implementación para la serialización personalizada.
    Este es el fragmento de código sobre cómo luce la implementación.

 ```csharp
    public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
    {
        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> requestBodyTypes)
        {
            return new ServiceRemotingRequestJsonMessageBodySerializer(serviceInterfaceType, requestBodyTypes);
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType,
            IEnumerable<Type> responseBodyTypes)
        {
            return new ServiceRemotingResponseJsonMessageBodySerializer(serviceInterfaceType, responseBodyTypes);
        }

        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
            return new JsonMessageFactory();
        }
    }

    class JsonMessageFactory : IServiceRemotingMessageBodyFactory
    {
        public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName,
            int numberOfParameters)
        {
            return new JsonRemotingRequestBody();
        }

        public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName)
        {
            return new JsonRemotingResponseBody();
        }
    }

    class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
    {
        public ServiceRemotingRequestJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
        {
            if (serviceRemotingRequestMessageBody == null)
            {
                return null;
            }

            var writeStream = new MemoryStream();
            var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream));

            var serializer = JsonSerializer.Create(new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);

            jsonWriter.Flush();
            var segment = new ArraySegment<byte>(writeStream.ToArray());
            var segments = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(segments);
        }

        public IServiceRemotingRequestMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (JsonReader reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingRequestBody>(reader);
            }
        }
    }

    class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
    {
        public ServiceRemotingResponseJsonMessageBodySerializer(Type serviceInterfaceType,
            IEnumerable<Type> parameterInfo)
        {
        }

        public OutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
        {
            var json = JsonConvert.SerializeObject(responseMessageBody, new JsonSerializerSettings()
            {
                TypeNameHandling = TypeNameHandling.All
            });
            var bytes = Encoding.UTF8.GetBytes(json);
            var segment = new ArraySegment<byte>(bytes);
            var list = new List<ArraySegment<byte>> { segment };
            return new OutgoingMessageBody(list);
        }

        public IServiceRemotingResponseMessageBody Deserialize(IncomingMessageBody messageBody)
        {
            using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))

            using (var reader = new JsonTextReader(sr))
            {
                var serializer = JsonSerializer.Create(new JsonSerializerSettings()
                {
                    TypeNameHandling = TypeNameHandling.All
                });

                return serializer.Deserialize<JsonRemotingResponseBody>(reader);
            }
        }
    }

    internal class JsonRemotingResponseBody : IServiceRemotingResponseMessageBody
    {
        public object Value;

        public void Set(object response)
        {
            this.Value = response;
        }

        public object Get(Type paramType)
        {
            return this.Value;
        }
    }

    class JsonRemotingRequestBody : IServiceRemotingRequestMessageBody
    {
        public readonly Dictionary<string, object> parameters = new Dictionary<string, object>();        

        public void SetParameter(int position, string parameName, object parameter)
        {
            this.parameters[parameName] = parameter;
        }

        public object GetParameter(int position, string parameName, Type paramType)
        {
            return this.parameters[parameName];
        }
    }
 ```

2.    Invalide el proveedor de serialización predeterminada con JsonSerializationProvider para el agente de escucha de comunicación remota.

  ```csharp
  protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
  ```

3.    Invalide el proveedor de serialización predeterminada con JsonSerializationProvider para ClientFactory de comunicación remota.

```csharp
  var proxyFactory = new ServiceProxyFactory((c) =>
            {
                return new FabricTransportServiceRemotingClientFactory(
                    serializationProvider: new ServiceRemotingJsonSerializationProvider());
            });
  ```

## <a name="next-steps"></a>pasos siguientes
* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicación WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Protección de la comunicación para Reliable Services](service-fabric-reliable-services-secure-communication.md)
