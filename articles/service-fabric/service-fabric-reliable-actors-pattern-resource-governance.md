<properties
   pageTitle="Patrón de diseño de gobernanza de recursos de los actores de Service Fabric de Azure"
   description="Patrón de diseño sobre cómo se pueden usar los actores de Service Fabric para modelar la aplicación que se necesita escalar, pero con el uso de recursos restringidos"
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
   ms.date="03/17/2015"
   ms.author="claudioc"/>

# Patrón de diseño de los actores de Service Fabric de Azure: gobernanza de recursos
Este patrón y los escenarios relacionados son fácilmente reconocibles para los desarrolladores (empresariales o de otro tipo) que tienen limitaciones de recursos locales o en la nube, que no puede escalar inmediatamente o que quieren incorporar aplicaciones de gran escala y datos en la nube.

En el ámbito empresarial, estos recursos limitados, como las bases de datos, se ejecutan en hardware con escalado vertical. Cualquier persona con una experiencia empresarial sabe que esta es una situación común en ámbitos locales. Incluso en la escala de nube, se produce esta situación cuando un servicio en la nube intenta exceder el límite de 64.000 conexiones TCP entre una tupla de dirección y puerto o al intentar conectarse a una base de datos basada en la nube que limita el número de conexiones simultáneas.

Antes, esto se solía resolver con una limitación a través de software intermedio basado en mensajes o mediante mecanismos de agrupación personalizada y de fachada. Son difíciles de conseguir correctamente, especialmente cuando es necesario escalar la capa intermedia y mantener los recuentos de conexiones correctos. Es simplemente frágil y complejo.

De hecho, como sucede con en el patrón de memoria caché inteligente, este patrón se extiende en varios escenarios y clientes que ya tienen sistemas funcionales con recursos restringidos. Se están creando sistemas en los que no solo es necesario escalar de forma horizontal los servicios, sino también su estado en memoria así como el estado persistente en un almacenamiento estable.

En el diagrama siguiente se muestra este escenario:

![][1]

## Escenarios de modelado de memoria caché con actores
Esencialmente, se modela el acceso a los recursos como un actor o varios actores que actúan como servidores proxy (por ejemplo, de conexión) en un recurso o grupo de recursos. Entonces, puede administrar directamente el recurso a través de actores individuales o usar un actor de coordinación que administre los actores del recurso. Para que sea más concreto, nos referiremos a la necesidad habitual de tener que trabajar con una capa de almacenamiento particionada (también conocida como "sharded") por motivos de rendimiento y escalabilidad. La primera opción es bastante básica: se puede usar una función estática para asignar y resolver los actores en los recursos descendentes. Esta función puede devolver, por ejemplo, una cadena de conexión con una entrada dada. Se puede decidir por completo cómo implementar esa función. Por supuesto, este enfoque conlleva sus propios inconvenientes, como la afinidad estática, que hace que volver a realizar una partición de los recursos o la reasignación de un actor a recursos sea muy difícil. Este es un ejemplo muy sencillo: se llevará a cabo aritmética de módulo para determinar el nombre de base de datos mediante el userId y se usará la región para identificar el servidor de base de datos.

## Ejemplo de código de gobernanza de recursos: resolución estática

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

Sencilla, pero no es muy flexible. Ahora, veamos un enfoque más avanzado y útil. En primer lugar, se modela la afinidad entre los recursos físicos y los actores. Esto se realiza a través de un actor denominado Resolver que entiende la asignación entre usuarios, particiones lógicas y recursos físicos. Resolver mantiene sus datos en estado persistente, aunque se almacenan en memoria caché para una búsqueda fácil. Como se vio en el ejemplo anterior de tipo de cambio en el modelo de memoria caché inteligente, Resolver puede capturar la información más reciente de forma proactiva mediante un temporizador. Cuando el actor user resuelve el recurso que debe usar, lo almacena en memoria caché en una variable local variable denominada _resolution y lo usa durante su duración. Se eligió una resolución basada en búsqueda (que se muestra a continuación) en lugar de un hash simple o un intervalo hash, por la flexibilidad que ofrece en operaciones como la reducción o el escalado horizontales, o el cambio de un usuario de un recurso a otro.

![][2]

En la ilustración anterior, el actor B23 primero resuelve su recurso (también conocido como resolución) DB1 y se almacena en memoria caché. Las operaciones posteriores ahora pueden usar la resolución almacenada en memoria caché para obtener acceso al recurso restringido. Dado que los actores admiten ejecución de subproceso único, los desarrolladores ya no necesitan preocuparse por el acceso simultáneo a los recursos. Los actores User y Resolver tienen este aspecto:

## Ejemplo de código de gobernanza de recursos: Resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : Actor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

Gobierno de recursos: ejemplo de Resolver

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : Actor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## Acceso a los recursos con capacidad finita
Ahora se analizará otro ejemplo: acceso exclusivo a recursos valiosos como bases de datos, cuentas de almacenamiento y sistemas de archivos con capacidad de rendimiento finita. Nuestro escenario es el siguiente: se quieren procesar eventos mediante un actor llamado EventProcessor, que es responsable de procesar y conservar el evento, en este caso a un archivo .CSV por motivos de simplicidad. Aunque se puede seguir el enfoque de particionamiento mencionado anteriormente para escalar horizontalmente los recursos, aún hay que ocuparse los problemas de simultaneidad. Por ese motivo se eligió un ejemplo basado en archivos para ilustrar este punto concreto: la escritura en un único archivo de varios actores provocará problemas de simultaneidad. Para resolver el problema, se introduce otro actor denominado EventWriter que tiene una propiedad exclusiva de los recursos restringidos. El escenario se ilustra a continuación:

![][3]

Se marcan los actores EventProcessor como "trabajadores sin estado", lo que permite que el tiempo de ejecución los escale en el clúster según sea necesario. Por lo tanto, no se usaron identificadores en la ilustración anterior para estos actores. En otras palabras, los actores sin estado son un grupo de trabajadores que mantiene el tiempo de ejecución. En el código de ejemplo siguiente, el actor EventProcessor hace dos cosas: primero decide qué EventWriter (y por tanto, el recurso) usará e invoca el actor seleccionado para escribir el evento procesado. Por motivos de simplicidad, se elige el tipo Event como identificador del actor EventWriter. En otras palabras, habrá uno y solo un elemento EventWriter para este tipo Event que ofrece un subproceso único y acceso exclusivo al recurso.

## Ejemplo de código de gobernanza de recursos: procesador de eventos

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

Ahora, echemos un vistazo al actor EventWriter. Realmente no hace mucho, aparte de controlar el acceso exclusivo al recurso restringido, en este caso el archivo, y escribir los eventos en él.
## Ejemplo de código de gobernanza de recursos: escritor de eventos

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

Al disponer de un único actor responsable del recurso, es posible agregar funciones como el almacenamiento en búfer. Se pueden almacenar en búfer los eventos de entrada y escribir dichos eventos periódicamente mediante un temporizador o cuando el búfer esté lleno. Este es un ejemplo basado en un temporizador simple:
## Ejemplo de código de gobernanza de recursos: escritor de eventos con búfer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

Aunque el código anterior funciona bien, los clientes no sabrán si su evento llegó al almacén subyacente. Para permitir el almacenamiento en búfer y que los clientes sepan lo que pasa con su solicitud, presentamos el siguiente enfoque para permitir que los clientes esperen hasta que su evento se escriba en el archivo .CSV:

## Ejemplo de código de gobernanza de recursos: procesamiento por lotes asincrónico

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

Esta clase se usará para crear y mantener una lista de tareas incompletas (para bloquear clientes) y completarlas en una sola operación cuando se escriban los eventos almacenados en el búfer en el almacenamiento. En la clase EventWriter, es necesario hacer tres cosas: marcar la clase del actor como reentrante, devolver el resultado de SubmitNext() y vaciar el temporizador. El código modificado es el siguiente:

## Ejemplo de código de gobernanza de recursos: almacenamiento en búfer con procesamiento por lotes asincrónico

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

¿Parece sencillo? En realidad, lo es. Pero la facilidad se contrapone a la eficacia de la empresa. Con esta arquitectura, se obtiene:

* Un direccionamiento de recursos independiente de la ubicación.
* Un tamaño de grupo ajustable, en función simplemente del número de actores que intervienen en nombre de un recurso.
* Uso del grupo coordinado en el lado cliente (como se muestra) o en el lado servidor (imagine un actor único delante de los grupos de la imagen).
* Adición de grupo escalable (simplemente agregar actores que representen el nuevo recurso).
* El actor (tal y como se demostró antes) puede almacenar en memoria caché los resultados del recurso de back-end a petición o almacenarlos previamente en memoria caché con un temporizador para reducir la necesidad de usar el recurso de back-end.
* Envío asincrónico eficaz.
* Un entorno de codificación que le resultará familiar a cualquier programador, no solo a los especialistas de software intermedio.

Este patrón es muy habitual en escenarios en los que los desarrolladores tienen recursos restringidos en los que deben desarrollar o deben crear grandes sistemas de escalado horizontal.


## Pasos siguientes
[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png
 

<!---HONumber=July15_HO4-->