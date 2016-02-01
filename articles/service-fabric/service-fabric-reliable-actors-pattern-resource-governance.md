<properties
   pageTitle="Patrón de diseño de gobernanza de recursos | Microsoft Azure"
   description="Patrón de diseño sobre cómo se puede usar Reliable Actors de Service Fabric para modelar la aplicación que se necesita escalar verticalmente, pero con el uso de recursos restringidos."
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

# Patrón de diseño de Reliable Actors: gobernanza de recursos

Este patrón y los escenarios relacionados son fácilmente reconocibles para los desarrolladores (empresariales o de otro tipo) que tienen limitaciones de recursos locales o en la nube, que no pueden escalar inmediatamente. También son familiares para los desarrolladores que desean distribuir aplicaciones a gran escala y datos en la nube.

En el ámbito empresarial, estos recursos limitados, como las bases de datos, se ejecutan en hardware con escalado vertical. Cualquier persona con una amplia experiencia empresarial sabe que esta es una situación común en ámbitos locales. Incluso a escala de la nube, los desarrolladores observan que esta situación ocurre cuando un servicio en la nube intenta exceder el límite de conexiones de 64 000 TCP entre una tupla de dirección y puerto. Esto también ocurre al intentar conectarse a una base de datos basada en la nube que limita el número de conexiones simultáneas.

Antes, esto se solía resolver con una limitación a través de software intermedio basado en mensajes o mediante mecanismos de agrupación personalizada y de fachada. Son difíciles de conseguir correctamente, especialmente cuando es necesario escalar la capa intermedia y mantener los recuentos de conexiones correctos. Es una solución frágil y compleja.

Como sucede con en el patrón de memoria caché inteligente, este patrón se extiende en varios escenarios y clientes que ya tienen sistemas funcionales con recursos restringidos. En los sistemas no solo es necesario escalar horizontalmente los servicios, sino también su estado en memoria, así como el estado persistente en un almacenamiento estable.

En el diagrama siguiente se muestra este escenario:

![Actores sin estado, creación de particiones y recursos restringidos][1]

## Escenarios de modelado de memoria caché con actores

Puede modelar el acceso a los recursos con la utilización de un actor o de varios actores que actúan como servidores proxy en un recurso o grupo de recursos (por ejemplo, de conexión). Puede administrar directamente el recurso a través de actores individuales o usar un actor de coordinación que administre los actores del recurso.

Para que este concepto sea más concreto, nos referiremos a la necesidad habitual de tener que trabajar con una capa de almacenamiento particionada (o compartida) por motivos de rendimiento y escalabilidad. La primera opción es bastante básica. Puede usar una función estática para asignar y resolver los actores en los recursos de bajada. Esta función puede devolver, por ejemplo, una cadena de conexión con una entrada dada. El usuario es quien decide cómo implementar la función. Pero este enfoque conlleva sus propios inconvenientes, como la afinidad estática, que dificulta la acción de volver a particionar los recursos o la reasignación de actores a los recursos.

A continuación se muestra un ejemplo sencillo. Se llevará a cabo la aritmética de módulo para determinar el nombre de base de datos mediante el **userId** y se usará la **región** para identificar el servidor de base de datos.

### Ejemplo de código de gobernanza de recursos: resolución estática

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

Es sencillo, pero no muy flexible. Ahora, veamos un enfoque más avanzado y útil.

En primer lugar, se modela la afinidad entre los recursos físicos y los actores. Esto se realiza a través de un actor conocido como **solucionador**. Entiende la asignación entre usuarios, particiones lógicas y recursos físicos. El solucionador mantiene sus datos en un almacén persistente, pero se almacenan en caché para poder buscarlos con facilidad. Como se vio en [el ejemplo anterior de tipo de cambio en el modelo de memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md), el solucionador puede capturar la información más reciente de forma proactiva mediante un temporizador. Cuando el actor user resuelve el recurso que necesita usar, lo almacena en la memoria caché en una variable local denominada **\_resolution** y lo usa durante su vigencia.

Se eligió una resolución basada en búsqueda (que se muestra a continuación), en lugar de un hash simple o un hash de intervalo, por la flexibilidad que ofrece en operaciones. Esto puede incluir el escalado horizontal y vertical, así como la transferencia de un usuario de un recurso a otro.

![Una solución de resolución de búsqueda][2]

En la ilustración anterior, se puede observar que el actor B23 primero resuelve su recurso (resolución) **DB1** y se almacena en memoria caché. Las operaciones posteriores ahora pueden usar la resolución almacenada en memoria caché para obtener acceso al recurso restringido. Dado que los actores admiten ejecución de subproceso único, los desarrolladores ya no necesitan preocuparse por el acceso simultáneo a los recursos. Consulte el ejemplo de código siguiente para ver el usuario y los actores de resolución.

### Ejemplo de código de gobernanza de recursos: solucionador

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


public class User : StatefulActor<UserState>, IUser
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

#### Gobernanza de recursos: ejemplo de solucionador

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

public class Resolver : StatefulActor<ResolverState>, IResolver
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

## Acceso a recursos con capacidad limitada

Ahora se analizará otro ejemplo: acceso exclusivo a recursos valiosos como bases de datos, cuentas de almacenamiento y sistemas de archivos con capacidad de rendimiento limitada. En este escenario, deseamos procesar eventos mediante un actor llamado EventProcessor. Este actor es responsable de procesar y conservar el evento, en este caso en un archivo .csv por motivos de simplicidad. Se puede seguir el enfoque de particionamiento mencionado anteriormente para escalar horizontalmente los recursos, pero aún hay que ocuparse de los problemas de simultaneidad. Se eligió un ejemplo basado en archivos para ilustrar este punto concreto, porque la escritura en un único archivo de varios actores provocará problemas de simultaneidad. Para resolver el problema, se introduce otro actor denominado EventWriter que tiene una propiedad exclusiva de los recursos restringidos. El escenario se ilustra a continuación:

![Escritura y procesamiento de eventos con EventWriter y EventProcessor][3]

Se marcan los actores EventProcessor como trabajadores sin estado, lo que permite que el tiempo de ejecución los escale en el clúster según sea necesario. Tenga en cuenta que no se usaron identificadores en la ilustración anterior para estos actores. Los actores sin estado son un grupo de trabajadores que mantiene el tiempo de ejecución.

En el código de ejemplo siguiente, el actor EventProcessor hace dos cosas. En primer lugar, decide qué EventWriter (y, por tanto, el recurso) utilizar y luego invoca el actor seleccionado para escribir el evento procesado. Por motivos de simplicidad, elegimos el tipo de evento como el identificador del actor EventWriter. Por tanto, solo hay un EventWriter para este tipo de evento, y ofrece un acceso exclusivo y de uniproceso al recurso.

### Ejemplo de código de gobernanza de recursos: EventProcessor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
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

Ahora, echemos un vistazo al actor EventWriter. Controla el acceso exclusivo al recurso restringido, en este caso el archivo, y escribir los eventos en él, pero no hace mucho más.

### Ejemplo de código de gobernanza de recursos: EventWriter

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

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
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

Al usar un único actor responsable del recurso, es posible agregar funciones como el almacenamiento en búfer. Se pueden almacenar en búfer los eventos de entrada y escribir dichos eventos periódicamente mediante un temporizador o cuando el búfer esté lleno. El ejemplo de código siguiente proporciona un ejemplo sencillo basado en temporizador.

### Ejemplo de código de gobernanza de recursos: EventWriter con búfer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
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

El código anterior funciona bien, pero los clientes no sabrán si su evento llegó al almacén subyacente. Para permitir el almacenamiento en búfer y que los clientes obtengan información sobre lo que sucede con su solicitud, el siguiente enfoque permite que los clientes esperen hasta que su evento se escriba en el archivo .csv.

### Ejemplo de código de gobernanza de recursos: procesamiento por lotes asincrónico

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

Esta clase se utilizará para crear y mantener una lista de tareas incompletas (para bloquear clientes). Se completarán de una vez después de haber escrito en el almacenamiento los eventos del búfer.

En la clase EventWriter, es necesario hacer tres cosas: marcar la clase del actor como reentrante, devolver el resultado de **SubmitNext()** y vaciar el temporizador. Vea el siguiente código modificado.

### Ejemplo de código de gobernanza de recursos: almacenamiento en búfer con procesamiento por lotes asincrónico

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
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

La facilidad de este enfoque se contrapone a la eficacia de la empresa. Mediante el uso de esta arquitectura, obtendrá:

* Un direccionamiento de recursos independiente de la ubicación.
* Un tamaño de grupo ajustable, en función simplemente del número de actores que intervienen en nombre de un recurso.
* Uso del grupo coordinado en el lado cliente (como se muestra) o en el lado servidor (imagine un actor único delante de los grupos de la imagen).
* Adición de grupo escalable (simplemente agregar actores que representen el nuevo recurso).
* Actores que pueden almacenar en caché los resultados de los recursos back-end a petición o en caché previa utilizando un temporizador, como se ha mostrado anteriormente. Esto reduce la necesidad de seleccionar recursos back-end.
* Envío asincrónico eficaz.
* Un entorno de codificación que resultará familiar a cualquier desarrollador, no solo a los especialistas de software intermedio.

Este patrón es habitual en escenarios en los que los desarrolladores tienen recursos restringidos en los que deben desarrollar. También es común cuando los desarrolladores crean grandes sistemas de escalado horizontal.


## Pasos siguientes

[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a Actores confiables de Service Fabric.](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=AcomDC_0121_2016-->