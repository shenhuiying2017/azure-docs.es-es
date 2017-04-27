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
ms.date: 03/09/2017
ms.author: pakunapa
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 11e300b3b1d0433bd4790332593ada2d3eede883
ms.lasthandoff: 04/03/2017


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

## <a name="next-steps"></a>Pasos siguientes
* [Protección de la comunicación para Reliable Services](service-fabric-reliable-services-secure-communication.md)

