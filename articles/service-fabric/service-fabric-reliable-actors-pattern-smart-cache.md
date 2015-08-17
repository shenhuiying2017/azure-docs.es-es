<properties
   pageTitle="Patrón de diseño de la memoria caché inteligente de Actores confiables"
   description="Patrón de diseño sobre cómo usar Actores confiables como infraestructura de almacenamiento en memoria caché de aplicaciones basadas en web"
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
   ms.author="claudioc"/>

# Patrón de diseño de Actores confiables: memoria caché inteligente
La combinación de una capa web, una capa de almacenamiento en memoria caché, una capa de almacenamiento y, ocasionalmente, una capa de trabajador son prácticamente las partes estándares de las aplicaciones actuales. La capa de almacenamiento en memoria caché normalmente es esencial para el rendimiento y puede, de hecho, constar de varias capas ella misma. Muchas de las memorias caché son pares de clave y valor simples, mientras que otros sistemas como [Redis](http://redis.io), que se usan como memorias caché, ofrecen una semántica más enriquecida. Aun así, cualquier capa de almacenamiento en memoria caché especial tendrá una semántica limitada y, lo que es más importante, es otra capa más que se debe administrar. ¿Y si en su lugar los objetos solo mantuvieran el estado en variables locales y a esos objetos se les pudiera hacer una instantánea o se pudieran almacenar de forma persistente en un almacén duradero automáticamente? Además, las colecciones enriquecidas, como las listas, los conjuntos ordenados, las colas y cualquier otro tipo personalizado para este tema se modelan simplemente como métodos y variables de miembro.

![][1]

## El ejemplo de la tabla de líderes
Considere el ejemplo de la tabla de líderes: un objeto Leaderboard debe mantener una lista ordenada de los jugadores y sus puntuaciones, de modo que se puedan realizar consultas. Por ejemplo, para obtener los "100 mejores jugadores" o para buscar la posición de un jugador en el panel de líderes relativa a +-N jugadores por encima y por debajo. Una solución típica con herramientas tradicionales requeriría una operación "GET" en el objeto Leaderboard (colección que admite la inserción de una nueva tupla<Player  Points> llamada Score), su ordenación y finalmente una operación "PUT" en la memoria caché. Probablemente, se bloquearía el objeto Leaderboard con LOCK (GETLOCK, PUTLOCK) para mantener la coherencia. Consideremos una solución basada en actores donde estado y comportamiento estén juntos. Hay dos opciones:

* implementar la colección Leaderboard como parte del actor;
* o usar el actor como una interfaz para la colección que se pueda mantener en una variable miembro. En primer lugar, echemos un vistazo al aspecto que puede tener una interfaz:

## Ejemplo de código de memoria caché inteligente: interfaz de la tabla de líderes

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

A continuación, implemente esta interfaz y use la segunda opción y encapsule el comportamiento de la colección en el actor:

## Ejemplo de código de memoria caché inteligente: actor de la tabla de líderes

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

El miembro de estado de la clase ofrece el estado del actor y en el ejemplo de código anterior también facilita métodos para leer y escribir datos.

## Ejemplo de código de memoria caché inteligente: LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

No hay envíos de datos ni bloqueos, simplemente la manipulación de objetos remotos en un tiempo de ejecución distribuido, el servicio a varios clientes como si fueran objetos individuales en una sola aplicación que dan servicio a un solo cliente. Este es el cliente de ejemplo:

## Ejemplo de código de memoria caché inteligente: llamada al actor de la tabla de líderes

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

El resultado tendrá un aspecto similar al siguiente:

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## Escalado de la arquitectura
Puede parecer que el ejemplo anterior podría crear un cuello de botella en la instancia de la tabla de líderes. ¿Qué sucede si, por ejemplo, está previsto admitir cientos de miles de jugadores? Una forma de abordarlo podría ser introducir agregadores sin estado que actuasen como un búfer: mantener las puntuaciones parciales (llamémoslas subtotales) y, después, enviarlas periódicamente el actor de la tabla de líderes, que puede mantener la tabla de líderes final. Trataremos esta técnica de "agregación" con más detalle más adelante. Además, no es necesario tener en cuenta exclusiones mutuas, semáforos u otras estructuras de simultaneidad que tradicionalmente requerían los programas con un comportamiento de simultaneidad correcto. A continuación, otro ejemplo de memoria caché que muestra que la semántica enriquecida que se puede implementar con actores. Esta vez, se implementa la lógica de la cola de prioridad (cuanto menor es el número, mayor es la prioridad) como parte de la implementación del actor. La interfaz de IJobQueue es similar a lo siguiente:

## Ejemplo de código de memoria caché inteligente: interfaz de la cola de elementos Job

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

También se debe definir el elemento Job:

## Ejemplo de código de memoria caché inteligente: Job

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

Finalmente, se implementa la interfaz IJobQueue en el nivel de detalle. Tenga en cuenta que se omiten los detalles de implementación de la cola de prioridad para mayor claridad. Una implementación de ejemplo puede encontrarse en los ejemplos que la acompañan.

## Ejemplo de código de memoria caché inteligente: cola de elementos Job

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

El resultado tendrá un aspecto similar al siguiente:

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## Los actores ofrecen flexibilidad
En los ejemplos anteriores, tabla de líderes y JobQueue, se usaron dos técnicas distintas:

* En el ejemplo de tabla de líderes se encapsuló un objeto Leaderboard como una variable de miembro privado en el actor y simplemente se ofreció una interfaz a este objeto (tanto a sus funcionalidades como a su estado).

* Por otro lado, en el ejemplo de JobQueue, se implementó el actor como una cola de prioridad en sí mismo en lugar de hacer referencia a otro objeto definido en otra parte.

Los actores ofrecen flexibilidad para que el desarrollador defina estructuras de objetos enriquecidos como parte de los actores o gráficos de objetos de referencia fuera de los actores. En términos de la memoria caché, los actores pueden realizar escritura por detrás o escritura a través, o bien se pueden usar distintas técnicas con una granularidad de variables de miembro. En otras palabras, existe un control total sobre lo que se debe conservar y cuándo se debe conservar. No es necesario conservar el estado transitorio o el estado que se puede crear a partir del estado guardado. ¿Qué tal si ahora se rellenan las memorias caché de estos actores? Hay varias formas de hacerlo. Los actores ofrecen métodos virtuales llamados OnActivateAsync() y OnDectivateAsync() para informar de cuándo se activa y desactiva una instancia del actor. Tenga en cuenta que el actor se activa a petición cuando se le envía la primera solicitud. Se puede usar OnActivateAsync() para rellenar el estado a petición como en la lectura a través, quizás desde un almacén estable externo. O se puede rellenar el estado en un temporizador, como un actor de tasa de cambio que ofrece la función de conversión según los tipos de divisa más recientes. Este actor puede rellenar periódicamente su estado desde un servicio externo, por ejemplo cada 5 segundos, y usarlo para la función de conversión. Observe el ejemplo siguiente:

## Ejemplo de código de memoria caché inteligente: conversor de tasas

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

Principalmente, la memoria caché inteligente ofrece:

* Alto rendimiento y baja latencia por parte de las solicitudes de servicio de la memoria.
* Enrutamiento de instancia única y serialización de subproceso único de las solicitudes a un elemento sin contención en un almacén persistente.
* Operaciones semánticas, por ejemplo, poner en la cola (elemento de Job).
* Escritura a través o escritura por detrás fáciles de implementar.
* Expulsión automática de los elementos LRU (usados menos recientemente) (administración de recursos).
* Elasticidad y confiabilidad automáticas.


## Pasos siguientes
[Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=August15_HO6-->