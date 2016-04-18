<properties
   pageTitle="Notas de Reliable Actors sobre la serialización del tipo de actor | Microsoft Azure"
   description="Examina los requisitos básicos para la definición de clases serializables que se pueden usar para definir interfaces y estados de Reliable Actors de Service Fabric"
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
   ms.date="03/25/2015"
   ms.author="vturecek"/>

# Notas sobre la serialización del tipo de Reliable Actors de Service Fabric


Los argumentos de todos los métodos, los tipos de resultados de las tareas que devuelve cada método de una interfaz de actor y los objetos almacenados en el administrador de estados de un actor deben ser [serializables de contratos de datos](https://msdn.microsoft.com/library/ms731923.aspx). Esto también se aplica a los argumentos de los métodos definidos en [interfaces de eventos de actor](service-fabric-reliable-actors-events.md#actor-events). (Los métodos de interfaz de eventos de actor siempre devuelven void).

## Tipos de datos personalizados

En este ejemplo, la siguiente interfaz de actor define un método que devuelve un tipo de datos personalizado denominado "`VoicemailBox`".

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

La interfaz se implementa mediante un actor, que utiliza el administrador de estados para almacenar un objeto `VoicemailBox`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

En este ejemplo, el objeto `VoicemailBox` se serializa en los siguientes casos:
 - El objeto se transmite entre una instancia de actor y un llamador.
 - El objeto se guarda en el administrador de estados, donde se almacena en un disco y se replica en otros nodos.
 
El marco de Reliable Actors usa la serialización DataContract. Por lo tanto, los objetos de datos personalizados y sus miembros se deben anotar con los atributos **DataContract** y **DataMember** respectivamente.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## Pasos siguientes
 - [Ciclo de vida de un actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
 - [Recordatorios y temporizadores de los actores](service-fabric-reliable-actors-timers-reminders.md)
 - [Eventos de actor](service-fabric-reliable-actors-events.md)
 - [Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)
 - [Polimorfismo de actores y patrones de diseño orientado a objetos](service-fabric-reliable-actors-polymorphism.md)
 - [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)

<!---HONumber=AcomDC_0406_2016-->