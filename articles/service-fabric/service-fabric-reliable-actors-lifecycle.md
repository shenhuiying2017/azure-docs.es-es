---
title: Ciclo de vida de Reliable Actors | Microsoft Docs
description: Explica el ciclo de vida de Reliable Actors de Service Fabric, la recolección de elementos no utilizados y la eliminación manual de actores y su estado
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2016
ms.author: amanbha

---
# Ciclo de vida, recolección automática de elementos no utilizados y eliminación manual de actores
Un actor se activa la primera vez que se realiza una llamada a cualquiera de sus métodos y se desactiva (el runtime de los actores recolecta los elementos no utilizados) si no se usa durante un periodo determinado. Un actor y su estado también se pueden eliminar manualmente en cualquier momento.

## Activación de actores
Cuando se activa un actor, ocurre lo siguiente:

* Cuando se recibe una llamada para un actor y no hay ninguno activo, se crea uno nuevo.
* Se carga el estado del actor si es de mantenimiento.
* Se llama al método `OnActivateAsync` (que se puede invalidar en la implementación del actor).
* El actor ahora se considera activo.

## Desactivación de actores
Cuando se desactiva un actor, ocurre lo siguiente:

* Cuando un actor no se usa durante un período de tiempo, se quita de la tabla de actores activos.
* Se llama al método `OnDeactivateAsync` (que se puede invalidar en la implementación del actor). y esto borra todos los temporizadores del actor. Las operaciones de actor como los cambios de estado no deben llamarse desde este método.

> [!TIP]
> El tiempo de ejecución de actores de Fabric emite algunos [eventos relacionados con la activación y desactivación de actores](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events). Son útiles para la supervisión del rendimiento y los diagnósticos.
> 
> 

### Recolección de actores no utilizados
Cuando un actor está desactivado, se liberan las referencias al objeto de actor y, normalmente, el recolector de elementos no utilizados de Common Language Runtime (CLR) puede recolectarlas. La recolección de elementos no utilizados solo limpia el objeto de actor; **no** quita el estado almacenado en el administrador de estados del actor. La próxima vez que se activa el actor, se crea un nuevo objeto de actor y se restaura su estado.

¿Qué se considera como "en uso" para fines de desactivación y recolección de elementos no utilizados?

* Se recibe una llamada.
* Se invoca al método `IRemindable.ReceiveReminderAsync` (se aplica solo si el actor usa recordatorios).

> [!NOTE]
> Si el actor usa temporizadores y se invoca la devolución de llamada de su temporizador, **no** cuenta como "en uso".
> 
> 

Antes de entrar en detalles sobre la recolección de elementos no utilizados, es importante definir los términos siguientes:

* *Intervalo de detección*. Es el intervalo durante el que el runtime de los actores examina la tabla de actores activos para encontrar los que pueden desactivarse y recolectarse como elementos no utilizados. El valor predeterminado es 1 minuto.
* *Tiempo de espera de inactividad*. Es el tiempo que un actor debe permanecer sin usarse (inactivo) antes de poder recolectarlo como elemento no utilizado. El valor predeterminado es 60 minutos.

Normalmente no es necesario cambiar estos valores Sin embargo, si es necesario, estos intervalos pueden cambiarse a través de `ActorServiceSettings` al registrar el [servicio de actor](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

El runtime de cada actor activo realiza un seguimiento del tiempo que ha estado inactivo (es decir, sin usarse). Dicho runtime comprueba cada uno de los actores para ver si puede recolectarse como elemento no utilizado cada `ScanIntervalInSeconds` y, si ha estado inactivo durante `IdleTimeoutInSeconds`, lo recolecta.

Cada vez que se usa un actor, su tiempo de inactividad se restablece a 0. Después de esto, el actor solo se puede recolectar como elemento no utilizado si permanece de nuevo inactivo durante `IdleTimeoutInSeconds`. Recuerde que se considera que se ha usado un actor si se usa un método de interfaz de actor o una devolución de llamada de recordatorio. **No** se considera que un actor se haya usado si se ejecuta la devolución de llamada del temporizador.

Para ilustrar estos conceptos, en el siguiente diagrama se muestra el ciclo de vida de un solo actor.

![Ejemplo de tiempo de inactividad][1]

Además, se muestra el impacto de las llamadas a los métodos de actor, los recordatorios y los temporizadores en la duración de este actor. Los puntos siguientes sobre el ejemplo son relevantes:

* ScanInterval e IdleTimeout se establecen en 5 y 10, respectivamente. (Las unidades no importan en este caso, ya que el único propósito es ilustrar el concepto).
* La detección de los actores para buscar los que se pueden recolectar como elementos no utilizados se produce en T=0,5,10,15,20,25, como se define al establecerse el elemento ScanInterval en 5.
* Un temporizador periódico se activa en T=4,8,12,16,20,24 y se ejecuta su devolución de llamada. No afecta al tiempo de inactividad del actor.
* Una llamada al método de actor en T=7 restablece el tiempo de inactividad a 0 y retrasa la recolección como elemento no utilizado del actor.
* Una devolución de llamada de un recordatorio del actor se ejecuta en T=14 y retrasa aún más la recolección como elemento no utilizado del actor.
* Durante la detección de la recolección de elementos no utilizados en T=25, el tiempo de inactividad del actor finalmente supera el IdleTimeout de 10 y se recolecta el actor como elemento no utilizado.

Un actor nunca se recolectará como elemento no utilizado mientras se ejecuta uno de sus métodos, independientemente del tiempo que se dedique a la ejecución de ese método. Como se mencionó anteriormente, la ejecución de los métodos de interfaz del actor y las devoluciones de llamada del recordatorio impide la recolección al restablecer el tiempo de inactividad del actor a 0. La ejecución de devoluciones de llamada del temporizador no restablece el tiempo de inactividad a 0. Sin embargo, la recolección del actor como elemento no utilizado se aplaza hasta que la devolución de llamada del temporizador ha finalizado su ejecución.

## Eliminación de actores y su estado
La recolección de elementos no utilizados de actores desactivados solo elimina el objeto del actor, pero no los datos que se almacenan en el administrador de estados de un actor. Cuando se vuelve a activar un actor, sus datos se vuelven a poner disponibles mediante el administrador de estados. Es posible que, en los casos en que los actores almacenan datos en el administrador de estados y se desactivan, pero nunca se vuelven a activar, haya que limpiar los datos.

El [servicio de actor](service-fabric-reliable-actors-platform.md) proporciona una función para eliminar actores desde un llamador remoto:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

La eliminación de un actor tiene las siguientes consecuencias, con independencia de que esté activo o no en ese momento:

* **Actor activo**
  * El actor se quita de la lista de actores activos y se desactiva.
  * Su estado se elimina permanentemente.
* **Actor inactivo**
  * Su estado se elimina permanentemente.

Tenga en cuenta que un actor no puede llamar a Delete por sí mismo desde uno de sus métodos de actor. Esto se debe a que el actor no se puede eliminar mientras se está ejecutando en un contexto de llamada de actor, en el que runtime ha obtenido un bloqueo en la llamada de actor para exigir el acceso uniproceso.

## Pasos siguientes
* [Recordatorios y temporizadores de los actores](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de actor](service-fabric-reliable-actors-events.md)
* [Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)
* [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)
* [Documentación de referencia de la API de actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de ejemplo](https://github.com/Azure/servicefabric-samples)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=AcomDC_0914_2016-->