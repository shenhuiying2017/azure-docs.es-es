
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
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
# <a name="service-remoting-with-reliable-services"></a>Comunicación remota de servicio con Reliable Services
Para los servicios que no están vinculados a una pila o un protocolo de comunicación concretos, como WebAPI, Windows Communication Foundation (WCF) u otros, el marco de trabajo de Reliable Services ofrece un mecanismo de comunicación remota para configurar de manera rápida y sencilla una llamada a procedimiento remoto para servicios.

## <a name="set-up-remoting-on-a-service"></a>Configurar la comunicación remota en un servicio
La configuración de la comunicación remota para un servicio se realiza en dos sencillos pasos:

1. Cree una interfaz para que la implemente su servicio. Esta interfaz define los métodos que estarán disponibles para la llamada a procedimiento remoto en su servicio. Los métodos deben ser métodos asincrónicos que devuelven tareas. La interfaz debe implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para indicar que el servicio tiene una interfaz de comunicación remota.
2. Utilice un agente de escucha de comunicación remota en su servicio. Se trata de una implementación de `ICommunicationListener` que ofrece capacidades de comunicación remota. El espacio de nombres `Microsoft.ServiceFabric.Services.Remoting.Runtime` contiene un método de extensión, `CreateServiceRemotingListener`, para los servicios con y sin estado que pueden utilizarse para crear un agente de escucha de comunicación remota mediante el protocolo de transporte de comunicación remota predeterminado.

Nota: El espacio de nombres `Remoting` está disponible como un paquete NuGet independiente denominado `Microsoft.ServiceFabric.Services.Remoting` 

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

## <a name="call-remote-service-methods"></a>Llamar a métodos de servicio remoto
La llamada a métodos en un servicio con la pila de comunicación remota se realiza mediante un proxy local al servicio a través de la clase `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . El método `ServiceProxy` crea un proxy local con la misma interfaz que implementa el servicio. Con ese proxy, puede llamar simplemente a los métodos en la interfaz de forma remota.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

El marco de trabajo de comunicación remota propaga las excepciones generadas en el servicio al cliente. Por lo tanto, la lógica de control de excepciones en el cliente mediante `ServiceProxy` puede controlar excepciones directamente que el servicio genera.

## <a name="service-proxy-lifetime"></a>Duración del proxy de servicio
La creación de ServiceProxy es una operación ligera, por lo que el usuario puede crearla todas las veces que lo necesite. El proxy de servicio puede volver a usarse siempre que el usuario lo necesite. El usuario puede volver a usar el mismo proxy en caso de excepción. Cada ServiceProxy contiene un cliente de comunicación que se usa para enviar mensajes a través de la conexión. Al invocar la API, se establece una comprobación interna para ver si el cliente de comunicación usado es válido. En función de ese resultado, volvemos a crear el cliente de comunicación. Por lo tanto, el usuario no tiene que volver a crear serviceproxy en caso de excepción.

### <a name="serviceproxyfactory-lifetime"></a>Duración de ServiceProxyFactory
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) es una fábrica que crea un proxy para interfaces remotas diferentes. Si utiliza ServiceProxy.Create de la API para crear un proxy, el marco de trabajo crea el singelton ServiceProxyFactory.
Es útil crear uno manualmente cuando necesite invalidar las propiedades [IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory).
Factory es una operación costosa. ServiceProxyFactory mantiene la memoria caché del cliente de comunicación.
El procedimiento recomendado consiste en almacenar en caché ServiceProxyFactory tanto como sea posible.

## <a name="remoting-exception-handling"></a>Control de excepciones remota
Toda la excepción remota generada por la API de servicio se vuelve a enviar al cliente como AggregateException. RemoteExceptions debe ser DataContract Serializable, en caso contrario, se genera [ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) en la API del proxy con el error de serialización en ella.

ServiceProxy controla todas las excepciones de conmutación por error para la partición de servicio para la que se crea. Vuelve a resolver los puntos de conexión si existen excepciones de conmutación por error (excepciones no transitorias) y recupera la llamada con el punto de conexión correcto. El número de reintentos para la excepción de conmutación por error es indefinido.
En el caso de TransientExceptions, solo recupera la llamada.

[OperationRetrySettings] proporciona los parámetros de reintento predeterminados. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) El usuario puede configurar estos valores pasando el objeto OperationRetrySettings al constructor ServiceProxyFactory.

## <a name="next-steps"></a>Pasos siguientes
* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicación WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Protección de la comunicación para Reliable Services](service-fabric-reliable-services-secure-communication.md)

