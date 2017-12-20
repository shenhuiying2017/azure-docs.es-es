---
title: "Comunicación remota en Azure Service Fabric | Microsoft Docs"
description: "La comunicación remota de Service Fabric permite a los clientes y servicios comunicarse con los servicios mediante la llamada a procedimiento remoto."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51a9c8bd628ef9e65d04a3a4ddbdc127d84d4b54
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="service-remoting-with-reliable-services"></a>Comunicación remota de servicio con Reliable Services
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java en Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

La plataforma Reliable Services proporciona un mecanismo de comunicación remota para configurar de un modo rápido y sencillo la llamada a procedimiento remoto para los servicios.

## <a name="set-up-remoting-on-a-service"></a>Configurar la comunicación remota en un servicio
La configuración de la comunicación remota para un servicio se realiza en dos sencillos pasos:

1. Cree una interfaz para que la implemente su servicio. Esta interfaz define los métodos que están disponibles para la llamada a procedimiento remoto en el servicio. Los métodos deben ser métodos asincrónicos que devuelven tareas. La interfaz debe implementar `microsoft.serviceFabric.services.remoting.Service` para indicar que el servicio tiene una interfaz de comunicación remota.
2. Utilice un agente de escucha de comunicación remota en su servicio. Se trata de una implementación de `CommunicationListener` que ofrece capacidades de comunicación remota. `FabricTransportServiceRemotingListener` puede utilizarse para crear un agente de escucha de comunicación remota con el protocolo de transporte de comunicación remota predeterminado.

Por ejemplo, el siguiente servicio sin estado expone un método único para obtener "Hello World" a través de la llamada a procedimiento remoto:

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Los argumentos y los tipos devueltos en la interfaz de servicio pueden ser cualquier tipo simple, complejo o personalizado, pero deben ser serializables.
>
>

## <a name="call-remote-service-methods"></a>Llamar a métodos de servicio remoto
La llamada a métodos en un servicio con la pila de comunicación remota se realiza mediante un proxy local al servicio a través de la clase `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` . El método `ServiceProxyBase` crea un proxy local con la misma interfaz que implementa el servicio. Con ese proxy, puede llamar simplemente a los métodos en la interfaz de forma remota.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

El marco de trabajo de comunicación remota propaga las excepciones generadas en el servicio al cliente. Por lo tanto, la lógica de control de excepciones en el cliente mediante `ServiceProxyBase` puede controlar excepciones directamente que el servicio genera.

## <a name="service-proxy-lifetime"></a>Duración del proxy de servicio
La creación de ServiceProxy es una operación ligera, por lo que el usuario puede crearla todas las veces que lo necesite. El proxy de servicio puede volver a usarse siempre que el usuario lo necesite. El usuario puede volver a usar el mismo proxy en caso de excepción. Cada ServiceProxy contiene un cliente de comunicación que se usa para enviar mensajes a través de la conexión. Al invocar la API, se establece una comprobación interna para ver si el cliente de comunicación usado es válido. En función de ese resultado, volvemos a crear el cliente de comunicación. Por lo tanto, el usuario no tiene que volver a crear serviceproxy en caso de excepción.

### <a name="serviceproxyfactory-lifetime"></a>Duración de ServiceProxyFactory
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) es un tejido que crea un proxy para distintas interfaces remotas. Si usa la API `ServiceProxyBase.create` para crear el proxy, el marco crea un `FabricServiceProxyFactory`.
Resulta útil crear uno manualmente cuando necesite invalidar las propiedades [IServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory).
Factory es una operación costosa. `FabricServiceProxyFactory` mantiene la memoria caché de los clientes de comunicación.
El procedimiento recomendado consiste en almacenar `FabricServiceProxyFactory` en caché lo más posible.

## <a name="remoting-exception-handling"></a>Control de excepciones remota
Toda la excepción remota generada por la API de servicio se vuelve a enviar al cliente como RuntimeException o como FabricException.

ServiceProxy controla todas las excepciones de conmutación por error para la partición de servicio para la que se crea. Vuelve a resolver los puntos de conexión si existen excepciones de conmutación por error (excepciones no transitorias) y recupera la llamada con el punto de conexión correcto. El número de reintentos para la excepción de conmutación por error es indefinido.
En el caso de TransientExceptions, solo recupera la llamada.

[OperationRetrySettings] proporciona los parámetros de reintento predeterminados. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) El usuario puede configurar estos valores pasando el objeto OperationRetrySettings al constructor ServiceProxyFactory.

## <a name="next-steps"></a>Pasos siguientes
* [Protección de la comunicación para Reliable Services](service-fabric-reliable-services-secure-communication.md)
