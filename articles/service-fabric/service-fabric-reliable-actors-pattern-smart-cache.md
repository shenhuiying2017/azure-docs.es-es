<properties
   pageTitle="Patrón de diseño de la memoria caché inteligente | Microsoft Azure"
   description="Patrón de diseño sobre cómo se puede usar el modelo de programación Reliable Actors de Service Fabric para crear una infraestructura de almacenamiento en caché para las aplicaciones basadas en web."
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Patrón de diseño de Reliable Actors: memoria caché inteligente

La combinación de una capa web, una capa de almacenamiento en memoria caché, una capa de almacenamiento y, ocasionalmente, una capa de trabajo constituyen las partes estándares de las aplicaciones actuales. La capa de almacenamiento en memoria caché normalmente es esencial para el rendimiento y puede constar de varias capas. Muchas memorias caché son pares clave-valor simples. Otros sistemas, como [Redis](http://redis.io), se utilizan como memorias caché, pero también ofrecen semánticas más enriquecidas. No obstante, cualquier capa de almacenamiento en caché especial tendrá semánticas limitadas. Es más importante aún contar con otra capa para administrar.

En su lugar, los objetos pueden mantener el estado en variables locales y a esos objetos se les puede hacer una instantánea o se pueden almacenar de forma persistente en un almacén duradero automáticamente. Además, las colecciones enriquecidas, como las listas, los conjuntos ordenados y otros tipos personalizados, se pueden modelar simplemente como métodos y variables de miembro.

![Actores y almacenamiento en caché][1]

## Exploración del ejemplo de marcador

Echemos un vistazo a los marcadores como ejemplo. Un objeto de marcador debe mantener una lista ordenada de los jugadores y sus puntuaciones, de modo que se puedan realizar consultas en la lista. Una consulta puede encontrar los 100 jugadores principales. También puede buscar la posición de un jugador en el marcado en relación con un número específico de jugadores por encima y por debajo de dicho jugador. Una solución tradicional requeriría obtener el objeto de marcador (una colección que permite insertar una tupla nueva `<Player, Points>` llamada **Score**) con la utilización de GET, ordenarlo y, por último, volver a colocarlo en la caché con PUT. Probablemente bloquearía el objeto de marcador (GETLOCK, PUTLOCK) para mantener la coherencia. Consideremos una solución basada en actores donde el estado y el comportamiento estén juntos. Hay dos opciones:

* Implementar la colección de marcador como parte del actor.
* Usar el actor como una interfaz para la colección que se pueda mantener en una variable miembro.

El ejemplo de código siguiente muestra cuál podría ser el aspecto de la interfaz.

### Ejemplo de código de memoria caché inteligente: interfaz del marcador

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

A continuación, puede implementar esta interfaz con la utilización de la segunda opción y encapsular el comportamiento de la colección en el actor:

### Ejemplo de código de memoria caché inteligente: actor del marcador

```
public class Leaderboard : StatefulActor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
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

El miembro de estado de la clase proporciona el estado del actor. En el código de ejemplo anterior, también proporciona métodos para leer y escribir datos.

### Ejemplo de código de memoria caché inteligente: LeaderboardCollection

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

No existen bloqueos ni envío de datos en este enfoque. Solo manipula objetos remotos en un tiempo de ejecución distribuido, tratando a varios clientes como si fueran objetos individuales en una sola aplicación que da servicio a un solo cliente. El siguiente ejemplo de código se centra en el cliente de ejemplo.

### Ejemplo de código de memoria caché inteligente: llamada al actor del marcador

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
Puede parecer que el ejemplo anterior podría crear un cuello de botella en la instancia del marcador. Por ejemplo, ¿qué ocurre si piensa admitir miles de jugadores? Una forma de abordarlo sería introducir agregadores sin estado que actúan como búferes. Estos agregadores contendrían puntuaciones parciales (subtotales) y, después, las enviarían periódicamente al actor de marcador, que mantendría el marcador final. Trataremos esta técnica con más detalle más adelante. Además, no es necesario tener en cuenta exclusiones mutuas, semáforos u otras estructuras de simultaneidad que tradicionalmente requerían los programas simultáneos que se comportan correctamente.

A continuación, otro ejemplo de memoria caché que muestra la semántica enriquecida que se puede implementar con actores. Esta vez, se implementa la lógica de una cola de prioridad (cuanto menor es el número, mayor es la prioridad) como parte de la implementación del actor. En el siguiente ejemplo de código se ilustra la interfaz de **IJobQueue**.

### Ejemplo de código de memoria caché inteligente: interfaz de la cola de elementos Job

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

También se debe definir el elemento **Job**:

### Ejemplo de código de memoria caché inteligente: Job

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

Finalmente, se implementa la interfaz IJobQueue en el actor. Tenga en cuenta que se omiten los detalles de implementación de la cola de prioridad para mayor claridad. Se ilustra la implementación en los ejemplos adjuntos.

### Ejemplo de código de la memoria caché inteligente: cola de elementos Job

```
public class JobQueue : StatefulActor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

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

## Uso de actores para ofrecer flexibilidad
En los ejemplos del marcador y la cola de elementos Job anteriores, hemos usado dos técnicas diferentes:

* En el ejemplo de marcador, se encapsula un objeto de marcador como una variable de miembro privado en el actor. A continuación, simplemente proporcionamos una interfaz para este objeto, tanto para su estado como para su funcionalidad.

* En el ejemplo de la cola de elementos Job, se implementó el actor como una cola de prioridad, en lugar de hacer referencia a otro objeto definido en otra parte.

Los actores ofrecen flexibilidad para que el desarrollador defina estructuras de objetos enriquecidos como parte de los actores o gráficos de objetos de referencia fuera de los actores. En términos de la memoria caché, los actores pueden realizar escritura por detrás o escritura a través, o bien se pueden usar distintas técnicas con una granularidad de variables de miembro. Puede controlar totalmente qué se debe conservar y cuándo. No es necesario conservar el estado transitorio o el estado que se puede crear a partir del estado guardado.

¿Cómo se rellenan las memorias caché de estos actores? Hay varias formas de hacerlo. Los actores ofrecen los métodos virtuales **OnActivateAsync()** y **OnDeactivateAsync()** para informar de cuándo se activa y desactiva una instancia del actor. Tenga en cuenta que el actor se activa a petición cuando se le envía la primera solicitud.

Se puede usar OnActivateAsync() para rellenar el estado a petición como en la lectura a través, como desde un almacén estable externo. Se puede rellenar el estado en un temporizador, como un actor de tasa de cambio que ofrece la función de conversión según los tipos de divisa más recientes. Este actor puede rellenar periódicamente su estado desde un servicio externo (por ejemplo, cada 5 segundos) y usarlo para la función de conversión. En el ejemplo de código siguiente se muestra cómo se puede hacer esto.

### Ejemplo de código de memoria caché inteligente: convertidor de índice

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

}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
}

```

Básicamente, el enfoque de memoria caché inteligente ofrece:

* Alto rendimiento y baja latencia por parte de las solicitudes de servicio de la memoria.
* Enrutamiento de instancia única y serialización de subproceso único de las solicitudes a un elemento sin contención en un almacén persistente.
* Operaciones semánticas, por ejemplo, **poner en cola (elemento de Job)**.
* Escritura a través y escritura por detrás fáciles de implementar.
* Expulsión automática de los elementos menos usados recientemente (LRU) (administración de recursos).
* Elasticidad y confiabilidad automáticas.


## Pasos siguientes

[Patrón: gráficos y redes distribuidos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a Actores confiables de Service Fabric.](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=AcomDC_0121_2016-->