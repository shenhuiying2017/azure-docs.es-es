---
title: "Administración de estado de Reliable Actors | Microsoft Docs"
description: "Describe cómo se administra, se conserva y se replica el estado de Reliable Actors para alta disponibilidad."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: aca8cf2b94e8b746a5cac6af021c7221a29b7345
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-actors-state-management"></a>Administración de estado de Reliable Actors
Reliable Actors son objetos uniproceso que encapsulan la lógica y el estado. Como los actores se ejecutan en Reliable Services, pueden mantener el estado de forma confiable con los mismos mecanismos de persistencia y replicación utilizados por Reliable Services. De este modo, los actores no pierden su estado después de los errores, cuando se reactivan después de la recolección de elementos no utilizados o cuando se mueven entre los nodos de un clúster debido un equilibrio de los recursos o actualizaciones.

## <a name="state-persistence-and-replication"></a>Persistencia y replicación del estado
Reliable Actors se considera *con estado* porque cada instancia de actor se asigna a un identificador único. Esto significa que las llamadas repetidas al mismo identificador de actor se enrutan a la misma instancia de actor. En un sistema sin estado, en cambio, no se garantiza que las llamadas del cliente se enruten al mismo servidor cada vez. Por este motivo, los servicios de actor siempre son servicios con estado.

Aunque los actores se consideran con estado, no significa que deben almacenar el estado de manera confiable. Los actores pueden elegir el nivel de persistencia y replicación de estado basado en los requisitos de almacenamiento de sus datos:

* **Estado persistente:** el estado se conserva en el disco y se replica a tres o más réplicas. Esta es la opción de almacenamiento de estado más duradera, ya que el estado puede persistir a través de la interrupción de todo el clúster.
* **Estado volátil:** el estado se replica a tres o más réplicas y solo se conserva en memoria. Esto proporciona resistencia frente a errores de nodo, errores de actores y durante las actualizaciones y el equilibrio de recursos. Sin embargo, el estado no persiste en el disco. Por tanto, si se pierden todas las réplicas al mismo tiempo, el estado también se pierde.
* **Sin estado persistente:** no se replica el estado ni se escribe en el disco. Este nivel es para aquellos actores que simplemente no necesitan mantener el estado de forma confiable.

Cada nivel de persistencia es simplemente otra configuración del *proveedor de estado* y de la *replicación* del servicio. Si el estado se escribe o no en el disco depende del proveedor de estado (el componente de un servicio Reliable Services que almacena el estado). La replicación depende de con cuántas réplicas se ha implementado un servicio. Al igual que con Reliable Services, tanto el proveedor de estado como el número de réplicas pueden establecerse manualmente con facilidad. La plataforma de actores proporciona un atributo, que, cuando se utiliza en un actor, selecciona automáticamente un proveedor de estado predeterminado y genera automáticamente la configuración para el número de réplicas, a fin de lograr una de estas tres opciones de persistencia. El atributo StatePersistence no es heredado por una clase derivada; cada tipo de actor debe proporcionar su nivel de StatePersistence.

### <a name="persisted-state"></a>Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
Esta configuración usa un proveedor de estado que almacena datos en el disco y establece automáticamente el número de réplicas del servicio en 3.

### <a name="volatile-state"></a>Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Esta configuración utiliza un proveedor de estado solo en memoria y establece el número de réplicas en 3.

### <a name="no-persisted-state"></a>Sin estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Esta configuración utiliza un proveedor de estado solo en memoria y establece el número de réplicas en 1.

### <a name="defaults-and-generated-settings"></a>Valores predeterminados y configuración generada
Cuando se usa el atributo `StatePersistence`, se selecciona automáticamente un proveedor de estado en tiempo de ejecución cuando se inicia el servicio de actor. Sin embargo, el número de réplicas, se establece en tiempo de compilación con las herramientas de compilación de actores de Visual Studio. Las herramientas de compilación generan automáticamente un *servicio predeterminado* del servicio de actor en ApplicationManifest.xml. Los parámetros se crean para **min replica set size** y **target replica set size**.

Puede cambiar estos parámetros manualmente. Sin embargo, cada vez que se cambia el atributo `StatePersistence`, los parámetros se establecen, de forma predeterminada, en los valores del tamaño del conjunto de réplicas para el atributo `StatePersistence`, invalidando los valores anteriores. Es decir, los valores establecidos en ServiceManifest.xml *solo* se reemplazan al compilar cuando cambia el valor del atributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Administrador de estado
Cada instancia de actor tiene su propio administrador de estado: una estructura de datos similar a un diccionario que almacena de manera confiable los pares clave-valor. El administrador de estado es un contenedor alrededor de un proveedor de estado. Se puede utilizar para almacenar datos, con independencia de la configuración de persistencia que se utilice, pero no proporciona ninguna garantía de que se pueda cambiar un servicio de actor en ejecución de una configuración de estado volátil (solo en memoria) a una configuración de estado persistente mediante una actualización gradual mientras se conservan los datos. Sin embargo, es posible cambiar el número de réplicas para un servicio en ejecución.

Las claves del administrador de estado deben ser cadenas. Los valores son genéricos y pueden ser de cualquier tipo, incluidos los tipos personalizados. Los valores almacenados en el administrador de estado deben ser serializables en el contrato de datos, ya que pueden transmitirse a través de la red a otros nodos durante la replicación y pueden escribirse en el disco, dependiendo de la configuración de persistencia del estado de un actor.

El administrador de estado expone los métodos de diccionario comunes para administrar el estado, de manera similar a los que se encuentran en un diccionario confiable.

### <a name="accessing-state"></a>Acceso al estado
Se puede acceder al estado a través del administrador de estado mediante una clave. Los métodos del administrador de estado son completamente asincrónicos, ya que pueden requerir una E/S de disco cuando los actores tienen un estado persistente. Después del primer acceso, los objetos de estado se almacenan en caché en la memoria. Las operaciones de acceso repetidas acceden a los objetos directamente desde la memoria y devuelven sincrónicamente sin incurrir en la sobrecarga de intercambio de contexto asincrónico o de E/S de disco. Se quita un objeto de estado de la memoria caché en los casos siguientes:

* Un método de actor produce una excepción no controlada después de recuperar un objeto del administrador de estado.
* Un actor se vuelve a activar después de haberse desactivado o después de un error.
* El proveedor de estado pagina el estado en el disco. Este comportamiento depende de la implementación del proveedor de estado. El proveedor de estado predeterminado para la configuración `Persisted` tiene este comportamiento.

El estado se puede recuperar mediante una operación *Get* estándar que inicia `KeyNotFoundException` (C#) o `NoSuchElementException` (Java) si no existe ninguna entrada para la clave:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

El estado también se puede recuperar mediante un método *TryGet* que no se inicia si no existe ninguna entrada para una clave:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

### <a name="saving-state"></a>Almacenamiento del estado
Los métodos de recuperación del administrador de estado devuelven una referencia a un objeto en la memoria local. La modificación de este objeto solo en la memoria local no hace que se guarde de forma duradera. Cuando un objeto se recupera desde el administrador de estado y se modifica, debe volver a insertarse en el administrador de estado para que se guarde de forma duradera.

El estado se puede insertar mediante una operación *Set* no condicional, que equivale a la sintaxis `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Puede agregar el estado mediante un método *Add*. Este método produce `InvalidOperationException` (C#) o `IllegalStateException` (Java) cuando intenta agregar una clave que ya existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

También puede agregar el estado mediante un método *TryAdd*. Este método no se inicia cuando intenta agregar una clave que ya existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Al final de un método de actor, el administrador de estado guarda automáticamente los valores agregados o modificados por una operación de inserción o actualización. El "guardado" puede incluir la persistencia en el disco y la replicación, dependiendo de la configuración utilizada. Los valores que no se han modificado no se conservan ni se replican. Si no se ha modificado ningún valor, la operación de guardar no hace nada. Si se produce un error al guardar, el estado modificado se descarta y se vuelve a cargar el estado original.

También puede guardar el estado manualmente con una llamada al método `SaveStateAsync` en la base de actor:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

### <a name="removing-state"></a>Eliminación del estado
Puede quitar el estado de manera permanente de administrador de estado del actor mediante una llamada al método *Remove*. Este método produce `KeyNotFoundException` (C#) o `NoSuchElementException` (Java) cuando intenta quitar una clave que no existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

También puede quitar el estado de forma permanente con el método *TryRemove*. Este método no se inicia cuando intenta quitar una clave que no existe.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

Un estado que se almacena en Reliable Actors debe serializarse antes de escribirse en el disco y replicarse para lograr alta disponibilidad. Aprenda más sobre la [serialización del tipo de actor](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

A continuación, aprenda más en [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md).
