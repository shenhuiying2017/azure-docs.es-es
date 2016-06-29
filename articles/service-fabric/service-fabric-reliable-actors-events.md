<properties
   pageTitle="Eventos de Reliable Actors | Microsoft Azure"
   description="Introducción a los eventos de Reliable Actors de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/13/2016"
   ms.author="amanbha"/>


# Eventos de actor
Los eventos de actor ofrecen una manera de enviar notificaciones de mejor esfuerzo del actor a los clientes. Los eventos de actor están diseñados para la comunicación entre actor y cliente, y no deben usarse para una comunicación entre actores.

Los fragmentos de código siguientes muestran cómo usar los eventos de actor en una aplicación.

Defina una interfaz que describa los eventos publicados por el actor. Esta interfaz debe derivarse de la interfaz `IActorEvents`. Los argumentos de los métodos deben ser [serializable de contratos de datos](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Los métodos deben devolver void, ya que las notificaciones de eventos son unidireccionales y de mejor esfuerzo.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Declare los eventos publicados por el actor en la interfaz del actor.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

En el lado cliente, implemente el controlador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

En el cliente, cree un proxy para el actor que publica el evento y suscríbase a sus eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

Si se producen conmutaciones por error, el actor puede realizar una conmutación por error a un nodo o proceso diferentes. El proxy del actor administra las suscripciones activas y las vuelve a suscribir automáticamente. Puede controlar el intervalo de suscribir nuevamente a través de la API `ActorProxyEventExtensions.SubscribeAsync<TEvent>`. Para cancelar la suscripción, use la API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`.

En el actor, simplemente publique los eventos cuando se produzcan. Si hay suscriptores del evento, el tiempo de ejecución de los actores les enviará la notificación.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## Pasos siguientes
 - [Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)
 - [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)
 - [Documentación de referencia de la API de actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de ejemplo](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0615_2016-->