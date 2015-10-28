<properties
   pageTitle="Avisos y temporizadores de Actores confiables"
   description="Introducción a los temporizadores y avisos de Actores confiables de Service Fabric."
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
   ms.date="08/05/2015"
   ms.author="amanbha"/>


# Temporizadores de actor
Los temporizadores de actor ofrecen un contenedor simple alrededor de los temporizadores de .NET para que los métodos de devolución de llamada respeten las garantías de simultaneidad basadas en turnos que proporciona el tiempo de ejecución de los actores.

Los actores pueden usar los métodos `RegisterTimer` y `UnregisterTimer` en su clase base para registrar y anular el registro de los temporizadores. En el ejemplo siguiente se muestra el uso de las API de temporizador. Las API son muy similares al temporizador de .NET. En el ejemplo siguiente, cuando el temporizador vence, el tiempo de ejecución de los actores llama al método `MoveObject` y se garantiza que se respeta la simultaneidad basada en turnos, lo que significa que no habrá otros métodos de actor o devoluciones de llamada de temporizadores o recordatorios en curso hasta que esta devolución de llamada complete su ejecución.

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
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
        return TaskDone.Done;
    }
}
```

El siguiente período del temporizador comienza cuando la devolución de llamada completa la ejecución. Esto implica que el temporizador se detiene mientras la devolución de llamada se ejecuta y se inicia cuando la devolución de llamada se completa.

El tiempo de ejecución de los actores guarda el estado del actor cuando se completa la devolución de llamada si el actor es un actor con estado, como en el ejemplo anterior. Si se produce un error al guardar el estado, se desactivará dicho objeto de actor y se activará una nueva instancia. Un método de devolución de llamada que no modifica el estado del actor puede registrarse como una devolución de llamada de temporizador de solo lectura si se especifica el atributo Readonly en la devolución de llamada del temporizador, como se describe en la sección [métodos readonly](service-fabric-reliable-actors-introduction.md#readonly-methods).

Todos los temporizadores se detienen cuando se desactiva el actor, como parte de la recolección de elementos no utilizados y no se invoca ninguna devolución de llamada de temporizador después de eso. Además, el tiempo de ejecución de los actores no conserva ninguna información sobre los temporizadores que se estaban ejecutando antes de la desactivación. Es el actor el que decide si se registran los temporizadores que necesita cuando se reactive en el futuro. Para obtener más información, consulte la sección sobre [recolección de actores no utilizados](service-fabric-reliable-actors-lifecycle.md).

## Recordatorios de actor
Los recordatorios son un mecanismo para activar devoluciones de llamada persistentes en un actor en los momentos especificados. Su funcionalidad es similar a los temporizadores, pero a diferencia de estos, los recordatorios se activan en todas las circunstancias, hasta que el actor anula explícitamente el registro del recordatorio. En concreto, los recordatorios se activan en conmutaciones por error y desactivaciones de actores, ya que el tiempo de ejecución de los actores conserva la información sobre los recordatorios de actor.

Los recordatorios solo se admiten para los agentes con estado. Actores sin estado no pueden usar recordatorios. Los proveedores de estado de los actores son los responsables de almacenar información sobre los recordatorios que los actores han registrado.

Para registrar un recordatorio, un actor llama al método `RegisterReminder` que se ofrece en la clase base, tal y como se muestra en el ejemplo siguiente.

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

En el ejemplo anterior, `"Pay cell phone bill"` es el nombre del recordatorio, que es una cadena que el actor usa para identificar de forma única un recordatorio. `BitConverter.GetBytes(amountInDollars)` es el contexto asociado con el recordatorio. Se pasará al actor como un argumento en la devolución de llamada del recordatorio, es decir, `IRemindable.ReceiveReminderAsync`.

Los actores que usan recordatorios deben implementar la interfaz `IRemindable`, como se muestra en el ejemplo siguiente.

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
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

Cuando se activa un recordatorio, el tiempo de ejecución de los actores de Fabric invocará al método `ReceiveReminderAsync` en el actor. Un actor puede registrar varios recordatorios y el método `ReceiveReminderAsync` se invoca cada vez que se activa uno de ellos. El actor puede usar el nombre del recordatorio que se pasa al método `ReceiveReminderAsync` para averiguar el recordatorio que se activó.

El tiempo de ejecución de los actores guarda el estado del actor cuando la llamada `ReceiveReminderAsync` se completa. Si se produce un error al guardar el estado, se desactivará dicho objeto de actor y se activará una nueva instancia. Para especificar que el estado no debe guardarse tras finalizar la devolución de llamada del recordatorio, la marca `ActorReminderAttributes.ReadOnly` puede establecerse en el parámetro `attributes` cuando se llama al método `RegisterReminder` para crear el recordatorio.

Para anular el registro de un recordatorio, es necesario llamar al método `UnregisterReminder`, tal y como se muestra a continuación.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Como se indicó anteriormente, el método `UnregisterReminder` acepta una interfaz `IActorReminder`. La clase base del actor admite un método `GetReminder` que puede usarse para recuperar la interfaz `IActorReminder` pasándole el nombre del recordatorio. Esto resulta útil porque el actor no necesita conservar la interfaz `IActorReminder` que devuelve la llamada al método `RegisterReminder`.

<!---HONumber=Oct15_HO3-->