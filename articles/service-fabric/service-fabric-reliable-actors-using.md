---
title: Implementar características en actores de Azure Service Fabric | Microsoft Docs
description: Describe cómo escribir su propio servicio de actor que implemente características de nivel de servicio de la misma manera que lo haría al heredar StatefulService.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212898"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementación de características de nivel de servicio en el servicio de actor
Como se describe en las [capas de servicio](service-fabric-reliable-actors-platform.md#service-layering), el propio servicio de actor es un servicio confiable.  Puede escribir su propio servicio que deriva de `ActorService` e implementar características de nivel de servicio del mismo modo que lo haría al heredar StatefulService; por ejemplo:

- Copia de seguridad y restauración del servicio.
- Funcionalidad compartida para todos los actores, por ejemplo, un interruptor.
- Llamadas de procedimiento remoto en el propio servicio de actor y en cada actor individual.

## <a name="using-the-actor-service"></a>Uso del servicio de actor
Las instancias de actor tienen acceso al servicio de actor en que se ejecutan. A través del servicio de actor, las instancias de actor pueden obtener el contexto de servicio mediante programación. El contexto de servicio tiene el identificador de partición, el nombre del servicio, el nombre de la aplicación y otra información específica de la plataforma de Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Del mismo modo que ocurre con todos los Reliable Services, el servicio de actor se debe registrar con un tipo de servicio en el tiempo de ejecución de Service Fabric. Con el fin de que el servicio de actor ejecute las instancias de actor, el tipo de actor también se debe registrar con el servicio de actor. El método de registro `ActorRuntime` realiza este trabajo para los actores. En el caso más simple, puede registrar el tipo de actor y se usará implícitamente el servicio de actor con configuración predeterminada:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Como alternativa, puede usar una expresión lambda para construir el servicio de actor. Después puede configurar el servicio de actor, así como también construir de manera explícita las instancias de actor, donde puede insertar dependencias en su actor a través de su constructor:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Métodos de servicio de actor
El servicio de actor implementa `IActorService` (C#) o `ActorService` (Java), que, a su vez, implementa `IService` (C#) o `Service` (Java). Esta es la interfaz que usa el servicio remoto de Reliable Services, que permite llamadas de procedimiento remoto en los métodos de servicio. Contiene métodos de nivel de servicio a los que se puede llamar de manera remota y le permite [enumerar](service-fabric-reliable-actors-enumerate.md) y [eliminar](service-fabric-reliable-actors-delete-actors.md) actores.


## <a name="custom-actor-service"></a>Servicio de actor personalizado
Mediante el uso de la expresión lambda del registro de actor, puede registrar su propio servicio de actor personalizado que deriva de `ActorService` (C#) y `FabricActorService` (Java). En este servicio de actor personalizado, puede implementar su propia funcionalidad de nivel de servicio mediante la escritura de una clase de servicio que hereda `ActorService` (C#) o `FabricActorService` (Java). Un servicio de actor personalizado hereda toda la funcionalidad de tiempo de ejecución de los actores de `ActorService` (C#) o `FabricActorService` (Java) y se puede usar para implementar sus propios métodos de servicio.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>Implementación de la copia de seguridad y restauración de los actores
Un servicio de actor personalizado puede exponer un método para crear una copia de seguridad de los datos de actor mediante el uso del agente de escucha remoto que ya se encuentra presente en `ActorService`:  Para obtener un ejemplo, vea [Actores de copia de seguridad y restauración](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2-stack"></a>Actor que utiliza la pila de comunicación remota V2
Con el paquete de NuGet 2.8, los usuarios ahora pueden utilizar la pila de comunicación remota V2, que tiene un mejor rendimiento y proporciona características como la serialización personalizada. La comunicación remota V2 no es compatible con la pila de comunicación remota existente (en adelante, la llamaremos la pila de comunicación remota V1).

Los cambios siguientes son necesarios para usar la pila de comunicación remota V2.
 1. Agregue el siguiente atributo de ensamblado en las interfaces de actor.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Compile y actualice los proyectos ActorService y Actor Client para empezar a usar la pila V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Actualización del servicio de actor a la pila de comunicación remota V2 sin afectar a la disponibilidad del servicio
Este cambio consiste en una actualización en dos pasos. Siga los pasos en la misma secuencia que se muestra.

1.  Agregue el siguiente atributo de ensamblado en las interfaces de actor. Este atributo iniciará dos agentes de escucha para ActorService, el agente de escucha V1 (existente) y V2. Actualice ActorService con este cambio.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Actualice ActorClients después de completar la actualización anterior.
Este paso se asegura de que el proxy del actor use la pila de comunicación remota V2.

3. Este paso es opcional. Cambie el atributo anterior para quitar el agente de escucha V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Administración de estados de los actores](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida de un actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
* [Documentación de referencia de la API de actores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de ejemplo de .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de ejemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
