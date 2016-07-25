<properties
   pageTitle="Avisos y temporizadores de Reliable Actors | Microsoft Azure"
   description="Introducción a los temporizadores y avisos de Reliable Actors de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>


# Recordatorios y temporizadores de los actores
Los actores pueden programar el trabajo periódico mediante el registro de temporizadores o recordatorios. En este artículo se muestra cómo utilizar temporizadores y recordatorios. Además, se explican las diferencias entre ellos.

## Temporizadores de actor
Los temporizadores de actor ofrecen un contenedor simple alrededor de los temporizadores de .NET para que los métodos de devolución de llamada respeten las garantías de simultaneidad basadas en turnos que proporciona el tiempo de ejecución de los actores.

Los actores pueden usar los métodos `RegisterTimer` y `UnregisterTimer` en su clase base para registrar y anular el registro de los temporizadores. En el ejemplo siguiente se muestra el uso de las API de temporizador. Las API son muy similares al temporizador de .NET. En este ejemplo, cuando el temporizador vence, el runtime de los actores llamará al método `MoveObject`. Se garantiza que el método respetará la simultaneidad basada en turnos. Esto significa que no habrá otros métodos de actor o devoluciones de llamada de temporizador o recordatorio en curso hasta que esta devolución de llamada complete la ejecución.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```

El siguiente período del temporizador comienza cuando la devolución de llamada completa la ejecución. Esto implica que el temporizador está detenido mientras se ejecuta la devolución de llamada y se inicia cuando esta se completa.

El runtime de los actores guarda los cambios realizados en el administrador de estados del actor cuando finaliza la devolución de llamada. Si se produce un error al guardar el estado, se desactivará dicho objeto de actor y se activará una nueva instancia.

Todos los temporizadores se detienen cuando el actor se desactiva como parte de la recolección de elementos no utilizados. Después de eso, no se invoca ninguna devolución de llamada de temporizador. Además, el tiempo de ejecución de los actores no conserva ninguna información sobre los temporizadores que se estaban ejecutando antes de la desactivación. Es el actor el que decide si se registran los temporizadores que necesita cuando se reactive en el futuro. Para obtener más información, consulte la sección [Recolección de actores no utilizados](service-fabric-reliable-actors-lifecycle.md).

## Recordatorios de actor
Los recordatorios son un mecanismo para desencadenar devoluciones de llamada persistentes en un actor en momentos especificados. Su funcionalidad es similar a la de los temporizadores. Sin embargo, a diferencia de los temporizadores, los recordatorios se desencadenan en todas las circunstancias hasta que el actor las registre o elimine del registro expresamente. En concreto, los recordatorios se activan en conmutaciones por error y desactivaciones de actores, ya que el tiempo de ejecución de los actores conserva la información sobre los recordatorios de actor.

Para registrar un recordatorio, un actor llama al método `RegisterReminderAsync` que se ofrece en la clase base, tal y como se muestra en el ejemplo siguiente:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

En este ejemplo, `"Pay cell phone bill"` es el nombre del recordatorio. Se trata de una cadena que el actor usa para identificar un recordatorio de forma única. `BitConverter.GetBytes(amountInDollars)` es el contexto que está asociado con el recordatorio. Se pasará al actor como un argumento en la devolución de llamada del recordatorio, es decir, `IRemindable.ReceiveReminderAsync`.

Los actores que usan recordatorios deben implementar la interfaz `IRemindable`, tal y como se muestra en el ejemplo siguiente.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

Cuando un recordatorio se desencadena, el runtime de Reliable Actors invocará al método `ReceiveReminderAsync` en el actor. Un actor puede registrar varios recordatorios y el método `ReceiveReminderAsync` se invoca cuando se desencadene uno de ellos. El actor puede usar el nombre del recordatorio que se pasa al método `ReceiveReminderAsync` para averiguar el recordatorio que se activó.

El sistema en tiempo de ejecución de Actors guarda el estado del actor cuando termina la llamada `ReceiveReminderAsync`. Si se produce un error al guardar el estado, se desactivará dicho objeto de actor y se activará una nueva instancia.

Para anular el registro de un recordatorio, un actor llama al método `UnregisterReminder`, tal y como se muestra en el ejemplo siguiente.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Como se indicó anteriormente, el método `UnregisterReminder` acepta una interfaz `IActorReminder`. La clase base del actor admite un método `GetReminder` que puede usarse para recuperar la interfaz `IActorReminder` pasándole el nombre del recordatorio. Esto resulta útil porque el actor no necesita conservar la interfaz `IActorReminder` que devuelve la llamada al método `RegisterReminder`.

## Pasos siguientes
 - [Eventos de actor](service-fabric-reliable-actors-events.md)
 - [Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)
 - [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)
 - [Documentación de referencia de la API de actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de ejemplo](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->