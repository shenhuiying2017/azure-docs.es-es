<properties
   pageTitle="Descripción general de los actores de Service Fabric de Azure"
   description="Introducción al modelo de programación de los actores de Service Fabric de Azure"
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
   ms.date="03/02/2015"
   ms.author="claudioc"/>

# Introducción a los actores de Service Fabric de Azure
Actores de Service Fabric de Azure es un modelo de programación de actores de [Service Fabric](service-fabric-technical-overview.md). Service Fabric es una plataforma para crear aplicaciones escalables altamente confiables de forma local y en la nube que son fáciles de desarrollar y administrar.

Actores de Fabric ofrece un modelo de actores asincrónico de subproceso único. Un actor representa una unidad de estado y cálculo. Los actores se distribuyen por todo el clúster para lograr una alta escalabilidad. Si se produce un error en un proceso que hospeda el actor, el sistema vuelve a crear el actor en otro proceso. De forma similar, si se produce un error en el nodo en que se ejecuta el proceso de host del actor, el sistema vuelve a crear el actor en un proceso de host de otro nodo. El tiempo de ejecución de los actores aprovecha el almacén distribuido que proporciona la plataforma de Service Fabric subyacente para ofrecer una administración de estados coherente y de alta disponibilidad para los actores. Esto permite que sea extremadamente fácil desarrollar y mantener aplicaciones distribuidas en la nube basadas en actores.

## Actores
Los actores son componentes de subproceso único aislados que encapsulan el comportamiento y el estado. Son similares a los objetos de .NET y, por tanto, ofrecen un modelo de programación natural a los desarrolladores. Cada actor es una instancia de un tipo de actor, similar a la forma en que un objeto de .NET es una instancia de un tipo de .NET. Por ejemplo, puede haber un tipo de actor que implemente la funcionalidad de una calculadora y podría haber muchos actores de ese tipo que se distribuyesen en varios nodos en un clúster. Cada uno de esos actores se identifica de forma única mediante un identificador de actor.

Los actores interactúan con el resto del sistema, incluidos otros actores, mediante el intercambio de mensajes asincrónicos con un patrón de solicitud-respuesta. Estas interacciones se definen en una interfaz como métodos asincrónicos. Por ejemplo, la interfaz de un tipo de actor que implementa la funcionalidad de una calculadora se puede definir como sigue.

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Un tipo de actor puede implementar la interfaz anterior de la siguiente manera:

```csharp
public class CalculatorActor : Actor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

En cada método de interfaz, los argumentos y el tipo de resultado de la tarea que devuelve deben ser [serializables de contratos de datos](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

La implementación de estos métodos no implica tratar con los bloqueos u otros problemas de simultaneidad, dado que el tiempo de ejecución de los actores ofrece simultaneidad basada en turnos para los métodos de actor. Puede encontrar información más detallada al respecto en la sección [Simultaneidad](#concurrency).

> [AZURE.TIP]El tiempo de ejecución de los actores de Fabric emite algunos [eventos y contadores de rendimiento relacionados con los métodos de actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.

Es conveniente comentar las siguientes reglas relativas a métodos de interfaz de actor: - No se pueden sobrecargar los métodos de la interfaz de actor. - Los métodos de interfaz de actor no pueden contener parámetros out, ref u optional.

## Comunicación con actores
La API de cliente de los actores ofrece comunicación entre una instancia de actor y un cliente de actor. Para comunicarse con un actor, un cliente crea un objeto proxy de actor que implementa la interfaz del actor. El cliente interactúa con el actor mediante la invocación de métodos en el objeto proxy. El proxy de actor puede usarse para la comunicación de cliente a actor, así como de actor a actor. Siguiendo con nuestro ejemplo de la calculadora, el código de cliente de un actor de calculadora podría escribirse como sigue:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Como se muestra en el ejemplo anterior, había dos datos que se usan para crear el objeto de proxy de actor: el identificador del actor y el nombre de la aplicación. El identificador del actor identifica de forma única el actor. El nombre de la aplicación es el nombre de la [aplicación de Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) como la que el actor se implementa.

Los agentes son actores virtuales, lo que significa que siempre existen. No es necesario crearlos explícitamente ni destruirlos. El tiempo de ejecución de los actores activa automáticamente un actor la primera vez que recibe una solicitud para ese actor. Si no se usa un actor durante un período determinado, el tiempo de ejecución de los actores lo recolectará como elemento no utilizado y lo activará posteriormente si es necesario. Puede obtener información detallada sobre esto en la sección [Ciclo de vida de actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md).

La API de cliente de los actores también ofrece conmutación por error y transparencia de ubicación. La clase `ActorProxy` en el lado cliente realiza la resolución necesaria y busca el servicio de actor [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) donde se hospeda el actor con el identificador especificado y abre un canal de comunicación con él. `ActorProxy` realiza reintentos si se producen errores de comunicación y en caso de conmutaciones por error. Esto significa que es posible que la implementación de un actor obtenga mensajes duplicados del mismo cliente.

## Simultaneidad
El tiempo de ejecución de los actores ofrece una simultaneidad simple basada en turnos para los métodos de actor. Esto significa que no puede haber más un subproceso activo dentro del código del actor en ningún momento.

Un turno consiste en la ejecución completa de un método de actor en respuesta a la solicitud de otros actores o clientes, o la ejecución completa de la devolución de llamada de un [temporizador o recordatorio](service-fabric-reliable-actors-timers-reminders.md). Aunque estos métodos y devoluciones de llamada son asincrónicos, el tiempo de ejecución de los actores no los intercala. Un turno debe completarse por completo antes de que se permita uno nuevo. En otras palabras, la devolución de llamada de un método de actor, un temporizador o recordatorio que se esté ejecutando se debe completar totalmente antes de que se permita una llamada a un método o una devolución de llamada nuevas. Un método o una devolución de llamada se consideran completados si la ejecución se ha devuelto desde el método o la devolución de llamada y se ha completado la tarea devuelta por el método o la devolución de llamada. Merece la pena resaltar que la simultaneidad basada en turnos se respeta incluso entre devoluciones de llamada, temporizadores y métodos diferentes.

El tiempo de ejecución de los actores impone la simultaneidad basada en turnos mediante la adquisición de un bloqueo por actor al principio de un turno y su liberación al final del turno. Por lo tanto, la simultaneidad basada en turnos se aplica por actor y no para todos los actores. Las devoluciones de llamada de los métodos de actor, los temporizadores y los recordatorios se pueden ejecutar simultáneamente en nombre de los diferentes actores.

En el ejemplo siguiente se muestran los conceptos anteriores. Supongamos un tipo de actor que implementa dos métodos asincrónicos (digamos *Method1* y *Method2*), un temporizador y un recordatorio. En el diagrama siguiente se muestra un ejemplo de una escala de tiempo de la ejecución de estos métodos y las devoluciones de llamada en nombre de los dos actores (*ActorId1* y *ActorId2*) que pertenecen a este tipo de actor.

![][1]

Las convenciones que se siguen en el diagrama anterior son:

- Cada línea vertical muestra el flujo lógico de ejecución de un método o una devolución de llamada en nombre de un actor determinado.
- Los eventos marcados en cada línea vertical se producen en orden cronológico, con los eventos más recientes por debajo de los más antiguos.
- Se usan colores diferentes para las escalas de tiempo correspondientes a actores distintos.
- Se usa el resaltado para indicar la duración durante la cual se mantiene el bloqueo por actor en nombre de un método o una devolución de llamada.

Los puntos siguientes sobre el diagrama anterior son relevantes:

- Cuando *Method1* se ejecuta en nombre de *ActorId2* en respuesta a la solicitud de cliente *xyz789*, llega otra solicitud de cliente *abc123* que también requiere que *Method1* lo ejecute *ActorId2*. Sin embargo, la última ejecución de *Method1* no comienza hasta que se haya completado la ejecución anterior. De forma similar, un recordatorio que haya registrado *ActorId2* se activa cuando *Method1* se ejecuta en respuesta a la solicitud de cliente *xyz789*. La devolución de llamada del recordatorio se ejecuta solo después de que las dos ejecuciones de *Method1* se hayan completado. Todo esto se debe a la simultaneidad basada en turnos que se exige para *ActorId2*.
- De forma similar, también se aplica la simultaneidad basado en turnos para *ActorId1*, como se muestra en la ejecución de *Method1*, *Method2* y la devolución de llamada del temporizador en nombre de *ActorId1* suceden en serie.
- La ejecución de *Method1* en nombre de *ActorId1* se superpone con su ejecución en nombre de *ActorId2*. Esto es así porque solo se exige la simultaneidad basada en turnos dentro de un actor y no para todos los actores.
- En algunas ejecuciones de métodos y devoluciones de llamada, el elemento `Task` que devuelve el método o la devolución de llamada se completa después de la devolución del método. En otras, `Task` ya se ha completado antes de la devolución del método o la devolución de llamada. En cualquier caso, el bloqueo de cada actor solo se libera después de que ambas cosas se hayan producido, es decir, después de la devolución del método o la devolución de llamada y de que `Task` se haya completado.

El tiempo de ejecución de los actores permite la reentrada de forma predeterminada. Esto significa que si un método de actor del actor A llama a un método del actor B que a su vez llama a otro método del actor A, se permite que ese método se ejecute como parte del mismo contexto de la cadena de llamada lógico. Todas las llamadas del temporizador y el recordatorio comienzan con el nuevo contexto de llamada lógico. Consulte la sección [Reentrada](service-fabric-reliable-actors-reentrancy.md) para obtener más detalles.

El tiempo de ejecución de los actores ofrece estas garantías de simultaneidad en situaciones donde controla la invocación de estos métodos. Por ejemplo, ofrece estas garantías para las invocaciones de método que se realizan en respuesta a la recepción de una solicitud de cliente y para las devoluciones de llamada de temporizadores y recordatorios. Sin embargo, si el código del actor invoca directamente a estos métodos fuera de los mecanismos que ofrece el tiempo de ejecución de los actores, el tiempo de ejecución no puede ofrecer ninguna garantía de simultaneidad. Por ejemplo, si el método se invoca en el contexto de una tarea que no está asociada con la tarea que han devuelto los métodos de actor o si se invoca desde un subproceso que el actor crea por sí mismo, el tiempo de ejecución no puede ofrecer garantías de simultaneidad. Por lo tanto, para realizar operaciones en segundo plano, los actores deben usar [temporizadores de actor o recordatorios de actor](service-fabric-reliable-actors-timers-reminders.md) que respeten la simultaneidad basada en turnos.

> [AZURE.TIP]El tiempo de ejecución de los actores de Fabric emite algunos [eventos y contadores de rendimiento relacionados con la simultaneidad](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.

## Administración de estados de los actores
Los actores de Fabric permiten crear actores con o sin estado.

### Actores sin estado
Los actores sin estado, como sugiere el nombre, no tienen ningún estado que administre el tiempo de ejecución de los actores. Los actores sin estado se derivan de clase base Actor. Pueden tener variables miembro que se conservarían a lo largo de la duración del actor, como sucede con cualquier otro tipo de .NET. Sin embargo, si esa instancia de actor se recolecta como elemento no utilizado después de estar inactiva durante un período de tiempo, perdería el estado almacenado en sus variables miembro. De forma similar, el estado podría perderse debido a las conmutaciones por error, lo que podría suceder en situaciones como las actualizaciones, las operaciones de equilibrio de recursos o un error un proceso de actor o del nodo que hospeda el proceso de actor.

A continuación se muestra un ejemplo de un actor sin estado.

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Actores con estado
Los actores con estado tienen un estado que debe conservarse entre las recolecciones de elementos no utilizados y las conmutaciones por error de los actores. Los actores con estado derivan de las clase base `Actor<TState>`, donde `TState` es el tipo del estado que debe conservarse entre las colecciones de elementos no utilizados y las conmutaciones por error. Es posible obtener acceso al estado en los métodos de actor a través de la propiedad `State` en la clase base `Actor<TState>`. A continuación se muestra un ejemplo de un actor con estado que obtiene acceso al estado.

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

El tipo del estado del actor debe ser [serializable de contratos de datos](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [Nota] Consulte el artículo [Notas de los actores confiables sobre la serialización](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) para obtener información detallada sobre cómo se deben definir las interfaces y los tipos de estado de los actores.

#### proveedores de estado de actor
El almacenamiento y la recuperación del estado se proporciona un proveedor de estado de los actores. El proveedor de estado se puede configurar por actor o para todos los actores dentro de un ensamblado, mediante el atributo específico de proveedor de estado. Hay algunos proveedores de estado de actor predeterminados que se incluyen en el tiempo de ejecución de los actores. La durabilidad y la confiabilidad del estado vienen determinadas por las garantías que ofrece el proveedor de estado. Cuando se activa un actor, se carga su estado en memoria. Cuando se completa un método de actor, el tiempo de ejecución de actores guarda automáticamente el estado modificado mediante una llamada a un método en el proveedor de estado. Si se produce un error al guardar el estado, el tiempo de ejecución de los actores recicla esa instancia de actor. Se crea una nueva instancia de actor y se carga con el último estado coherente del proveedor de estado.

De manera predeterminada, un actor con estado usa el proveedor de estado de los actores de almacén de valor de clave. Este proveedor de estado está integrado en el almacén de pares clave y valor distribuido que ofrece la plataforma de Service Fabric. Para obtener más información, consulte el tema sobre [opciones de proveedores de estado](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]El tiempo de ejecución de los actores de Fabric emite algunos [eventos y contadores de rendimiento relacionados con la administración de estados de los actores](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.

#### Métodos de solo lectura
De forma predeterminada, el tiempo de ejecución de los actores guarda el estado del actor tras la finalización de la llamada a un método de actor, la devolución de llamada de un temporizador y la devolución de llamada de un recordatorio. No se permite ninguna otra llamada de actor hasta que la operación de almacenamiento de estado se complete. Dependiendo del proveedor de estado,el almacenamiento del estado puede tardar algo de tiempo y durante este período se no permiten otras llamadas de actor en el actor.

Puede haber métodos de actor que no modifiquen el estado y, en ese caso, el tiempo adicional que se dedica a guardar el estado puede afectar al rendimiento general del sistema. Para evitarlo, se pueden marcar los métodos y las devoluciones de llamada de temporizador que no modifiquen el estado como solo lectura.

En el ejemplo siguiente se muestra cómo marcar un método de actor como de solo lectura mediante el atributo `Readonly`.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Las devoluciones de llamada de temporizador se pueden marcar con el atributo `Readonly` de forma similar. Para los recordatorios, el indicador de solo lectura se pasa como argumento al método `RegisterReminder` que se invoca para registrar el recordatorio.

## Pasos siguientes
### Conceptos
[Ciclo de vida de actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)

[Recordatorios y temporizadores de los actores](service-fabric-reliable-actors-timers-reminders.md)

[Eventos de los actores](service-fabric-reliable-actors-events.md)

[Reentrada de actores](service-fabric-reliable-actors-reentrancy.md)

[Cómo usan los actores de Fabric la plataforma Service Fabric](service-fabric-reliable-actors-platform.md)

[Configuración del actor KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
 

<!---HONumber=July15_HO2-->