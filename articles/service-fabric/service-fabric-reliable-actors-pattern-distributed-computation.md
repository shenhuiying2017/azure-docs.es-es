<properties
   pageTitle="Patrón de cálculo distribuido de Actores confiables"
   description="Actores confiables de Service Fabric es un buena opción para la mensajería asincrónica paralela, el estado distribuido fácilmente administrable y el cálculo en paralelo."
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
   ms.date="09/08/2015"
   ms.author="vturecek"/>

# Patrón de diseño de Actores confiables: cálculo distribuido
Esto lo debemos en parte a un cliente de la vida real que realizó un cálculo financiero en Actores confiables de Service Fabric en un período increíblemente corto, una simulación de Monte Carlo que garantiza la exactitud de cálculos de riesgos.

En un principio, especialmente para aquellos que no tienen un conocimiento específico del dominio, el tratamiento que Service Fabric de Azure hace de este tipo de carga de trabajo, en lugar de otros enfoques que pueden ser más tradicionales como asignación y reducción o MPI, puede no resultar obvio.

Pero resulta que Service Fabric de Azure es un buena opción para la mensajería asincrónica paralela, el estado distribuido fácilmente administrable y el cálculo en paralelo, tal y como se muestra en el diagrama siguiente:

![][1]

En el ejemplo siguiente, calculamos simplemente pi mediante una simulación de Monte Carlo. Tenemos los siguientes actores:

* Processor, responsable de calcular pi con actores PoolTask.

* PoolTask, responsable de la simulación de Monte Carlo que envía los resultados a Aggregator.

* Aggregator, responsable de acumular los resultados y enviarlos a Finaliser.

* Finaliser, responsable de calcular el resultado final e imprimirlo en pantalla.

## Ejemplo de código de cálculo distribuido: simulación de Monte Carlo

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Una manera común de acumular los resultados en Service Fabric de Azure es usar temporizadores. Se usan actores sin estado por dos motivos principales: el tiempo de ejecución decidirá cuántos elementos Aggregator se necesitan de forma dinámica y, por tanto, ofrece escalado según sea necesario; y creará instancias de estos actores "localmente", es decir, en el mismo silo del actor que realiza la llamada, con lo que se reducen los saltos de red. Este es el aspecto de Aggregator y Finaliser:

## Ejemplo de código de cálculo distribuido: aggregator

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : Actor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return TaskDone.Done;
    }
}
```

En este punto, debe quedar claro cómo se podría mejorar potencialmente el ejemplo de tabla de líderes con un elemento Aggregator para el escalado y el rendimiento.

No estamos de ninguna manera afirmando que Service Fabric de Azure sea un sustituto directo de otros marcos de cálculo distribuido de datos de gran tamaño o cálculos de alto rendimiento. Simplemente, hay algunas cosas que están construidas para controlarse mejor que otras. Sin embargo, es posible modelar flujos de trabajo y cálculos paralelos distribuidos en Service Fabric de Azure, mientras se siguen obteniendo las ventajas de simplicidad que ofrece.

## Pasos siguientes
[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=Oct15_HO3-->