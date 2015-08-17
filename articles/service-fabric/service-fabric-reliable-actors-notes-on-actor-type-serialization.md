<properties
   pageTitle="Notas de Actores confiables de Service Fabric sobre la serialización del tipo de actor"
   description="Examina los requisitos básicos para la definición de clases serializables que se pueden usar para definir interfaces y estados de Actores confiable de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="claudioc"/>


# Notas sobre la serialización del tipo de Actores confiables de Service Fabric

Hay algunos aspectos importantes que deben tenerse en cuenta al definir las interfaces y los estados del actor: los tipos deben ser serializable de contratos de datos. Puede encontrar más información sobre los contratos de datos en [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Tipos usados en las interfaces de actor

Los argumentos de todos los métodos y el tipo de resultado de la tarea que devuelve cada método definido en la [interfaz de actor](service-fabric-reliable-actors-introduction.md#actors) debe ser serializable de contratos de datos. Esto también se aplica a los argumentos de los métodos definidos en [interfaces de eventos de actor](service-fabric-reliable-actors-events.md#actor-events). (Los métodos de interfaz de eventos de actor siempre devuelven void). Por ejemplo, si la interfaz `IVoiceMail` define un método como:

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` es un tipo de .NET estándar que ya es serializable de contratos de datos. El tipo `Voicemail` debe ser serializable de contratos de datos.

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

## Clase de estado Actor

El estado de actor debe ser serializable de contratos de datos. Por ejemplo, si hay una definición de clase Actor como la siguiente:

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

La clase de estado se definirá con la clase y sus miembros anotados con los atributos DataContract y DataMember, respectivamente.

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

<!---HONumber=August15_HO6-->