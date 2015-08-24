<properties
   pageTitle="Patrón de diseño de gráficos y redes distribuidas de los actores de Service Fabric de Azure"
   description="Diseñar el patrón sobre la forma en que los actores de Service Fabric pueden usarse para modelar la aplicación como gráficos y redes distribuidas"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="claudioc"/>

# Patrón de diseño de Actores confiables: redes y gráficos distribuidos
Actores confiables de Service Fabric es una solución natural para las complejas soluciones de modelado que implican relaciones y el modelado de dichas relaciones como objetos.

![][1]

Como se muestra en el diagrama, es sencillo modelar un usuario como una instancia de actor (nodo en la red). Por ejemplo, la "Fuente de amigos" (que a veces se denomina el problema de los "seguidores") permite a los usuarios ver las actualizaciones de estado de los usuarios con los que están conectados, similar al funcionamiento de Facebook y Twitter. El modelo de actor ofrece flexibilidad para enfocar el problema de materialización. Es posible rellenar la Fuente de amigos en tiempo de evento, actualizando la Fuente de amigos de todos mis amigos en el momento en que se publica una actualización, como se muestra a continuación:

![][2]


## Ejemplo de código de memoria caché inteligente: Fuente de amigos de una red social (tiempo de evento)

Código de ejemplo que rellena la Fuente de amigos:

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : Actor, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

También es posible modelar los actores para efectuar una distribución ramificada y compilar la Fuente de amigos en el temporizador de la consulta, es decir, cuando el usuario pide su Fuente de amigos. Otro método que se puede usar es materializar la Fuente de amigos en un temporizador, por ejemplo, cada 5 minutos. O bien, se puede optimizar el modelo y combinar el procesamiento del tiempo del evento y del tiempo de la consulta con un modelo basado en temporizadores, según los hábitos del usuario, como la frecuencia con que inicia sesión o publica una actualización. Cuando se modela un actor en una red social, se deben considerar también los "superusuarios" con millones de seguidores. Los desarrolladores deben modelar el estado y el comportamiento de estos usuarios de forma diferente para satisfacer la demanda. De forma similar, si se quiere modelar una actividad que conecte a muchos actores de usuario a un actor de actividad único (concentrador y radio), también se puede conseguir. Los escenarios de chat de grupo o de hospedaje de juego son dos ejemplos. Consideremos el ejemplo del chat de grupo; un conjunto de participantes crea un actor de chat de grupo que puede distribuir mensajes de un participante al grupo como en el ejemplo siguiente:

## Ejemplo de código de memoria caché inteligente: GroupChat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : Actor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

Lo que realmente hace es aprovechar la capacidad de los actores confiables para permitir que cualquier actor se dirija a otro actor del clúster mediante su identificador y se comunique con él sin tener que preocuparse de la ubicación, el direccionamiento, el almacenamiento en memoria caché, la mensajería, la serialización o el enrutamiento.

## Pasos siguientes
[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=August15_HO7-->