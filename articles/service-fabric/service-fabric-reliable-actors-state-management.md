---
title: Administración de estado de Reliable Actors | Microsoft Docs
description: Describe cómo se administra, se conserva y se replica el estado de Reliable Actors para alta disponibilidad.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2016
ms.author: vturecek

---
# Administración de estado de Reliable Actors
Reliable Actors son objetos uniproceso que encapsulan la lógica y el estado. Como los actores se ejecutan en Reliable Services, pueden mantener el estado de forma confiable con los mismos mecanismos de persistencia de replicación utilizados por Reliable Services. De este modo, los actores no pierden su estado después de los errores, cuando se reactivan después de la recolección de elementos no utilizados o cuando se mueven entre los nodos de un clúster debido un equilibrio de los recursos o actualizaciones.

## Persistencia y replicación del estado
Reliable Actors se considera *con estado* porque cada instancia de actor se asigna a un identificador único. Esto significa que las llamadas repetidas al mismo identificador de actor se enrutarán a la misma instancia de actor. Esto difiere de un sistema sin estado en que no se garantiza que las llamadas del cliente se enruten al mismo servidor cada vez. Por este motivo, los servicios de actor siempre son servicios con estado.

Sin embargo, aunque los actores se consideran con estado, no significa que deben almacenar el estado de manera confiable. Los actores pueden elegir el nivel de persistencia y replicación de estado basado en los requisitos de almacenamiento de sus datos:

* **Estado persistente:** el estado se conserva en el disco y se replica a tres o más réplicas. Esta es la opción de almacenamiento de estado más duradera, ya que el estado puede persistir a través de la interrupción de todo el clúster.
* **Estado volátil:** el estado se replica a tres o más réplicas y solo se conserva en memoria. Esto proporciona resistencia frente a errores de nodo, errores de actores y durante las actualizaciones y el equilibrio de recursos. Sin embargo, el estado no se conserva en el disco; por lo tanto, si todas las réplicas se pierden al mismo tiempo, también se pierde el estado.
* **Sin estado persistente:** no se replica el estado ni se escribe en el disco. Para aquellos actores que simplemente no necesitan mantener el estado de forma confiable.

Cada nivel de persistencia es simplemente otra configuración del *proveedor de estado* y de la *replicación* del servicio. Si el estado se escribe o no en el disco depende del *proveedor de estado* (el componente de un servicio Reliable Services que almacena el estado) y la replicación depende de con cuántas réplicas se ha implementado un servicio. Al igual que con Reliable Services, tanto el proveedor de estado como el número de réplicas pueden establecerse manualmente con facilidad. La plataforma de actores proporciona un atributo, que, cuando se utiliza en un actor seleccionará automáticamente un proveedor de estado predeterminado y generará automáticamente la configuración para el número de réplicas para lograr una de estas tres opciones de persistencia.

### Estado persistente
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
Esta configuración usa un proveedor de estado que almacena datos en el disco y establece automáticamente el número de réplicas del servicio en 3.

### Estado volátil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
Esta configuración utiliza un proveedor de estado solo en memoria y establece el número de réplicas en 3.

### Sin estado persistente
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
Esta configuración utiliza un proveedor de estado solo en memoria y establece el número de réplicas en 1.

### Valores predeterminados y configuración generada
Cuando se usa el atributo `StatePersistence`, se selecciona automáticamente un proveedor de estado en tiempo de ejecución cuando se inicia el servicio de actor. Sin embargo, el número de réplicas, se establece en tiempo de compilación con las herramientas de compilación de actores de Visual Studio. Las herramientas de compilación generan automáticamente un *servicio predeterminado* del servicio de actor en ApplicationManifest.xml. Los parámetros se crean para **min replica set size** y **target replica set size**. Por supuesto, puede cambiar estos parámetros manualmente; sin embargo, cada vez que se cambia el atributo `StatePersistence`, los parámetros se establecerán, de forma predeterminada, en los valores del tamaño del conjunto de réplicas para el atributo `StatePersistence`, invalidando los valores anteriores. Es decir, los valores establecidos en ServiceManifest.xml **solo** se reemplazarán al compilar cuando cambie el valor del atributo `StatePersistence`.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
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

## Administrador de estado
Cada instancia de actor tiene su propio administrador de estado: una estructura de datos similar a un diccionario que almacena de manera confiable los pares clave-valor. El administrador de estado es un contenedor alrededor de un proveedor de estado. Se puede utilizar para almacenar datos, con independencia de la configuración de persistencia que se utilice, pero no proporciona ninguna garantía de que se pueda cambiar un servicio de actor en ejecución de una configuración de estado volátil (solo en memoria) a una configuración de estado persistente mediante una actualización gradual mientras se conservan los datos. Sin embargo, es posible cambiar el número de réplicas para un servicio en ejecución.

Las claves del administrador de estado deben ser cadenas, mientras que los valores son genéricos y pueden ser de cualquier tipo, incluidos los tipos personalizados. Los valores almacenados en el administrador del estado deben ser serializables en el contrato de datos, ya que pueden transmitirse a través de la red a otros nodos durante la replicación y pueden escribirse en el disco, dependiendo de la configuración de persistencia del estado de un actor.

El administrador de estado expone los métodos de diccionario comunes para administrar el estado, de manera similar a los que se encuentran en un diccionario confiable.

### Acceso al estado
Se puede acceder al estado a través del administrador de estado mediante una clave. Los métodos del administrador de estado son completamente asincrónicos ya que pueden requerir una E/S de disco cuando los actores tienen un estado persistente. Después del primer acceso, los objetos de estado se almacenan en caché en la memoria. Las operaciones de acceso repetidas acceden a los objetos directamente desde la memoria y devuelven sincrónicamente sin incurrir en la sobrecarga de intercambio de contexto asincrónico o de E/S de disco. Se quita un objeto de estado de la memoria caché en los casos siguientes:

* Un método de actor produce una excepción no controlada después de recuperar un objeto del administrador de estado.
* Un actor se vuelve a activar después de haberse desactivado o debido a un error.
* Si el proveedor de estado pagina el estado en el disco. Este comportamiento depende de la implementación del proveedor de estado. El proveedor de estado predeterminado para la configuración `Persisted` tiene este comportamiento.

El estado se puede recuperar mediante una operación *Get* estándar que inicia `KeyNotFoundException` si no existe ninguna entrada para la clave especificada:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

El estado también se puede recuperar mediante un método *TryGet* que no iniciará ninguna excepción si no existe ninguna entrada para la clave especificada:

```csharp
class MyActor : Actor, IMyActor
{
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

### Almacenamiento del estado
Los métodos de recuperación del administrador de estado devuelven una referencia a un objeto en la memoria local. La modificación de este objeto solo en la memoria local no hace que se guarde de forma duradera. Cuando un objeto se recupera desde el administrador de estado y se modifica, debe volver a insertarse en el administrador de estado para que se guarde de forma duradera.

El estado se puede insertar mediante una operación *Set* no condicional, que equivale a la sintaxis `dictionary["key"] = value`:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

El estado puede agregarse con el método *Add*, que iniciará una excepción `InvalidOperationException` cuando se intente agregar una clave que ya existe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

El estado puede agregarse también con el método *TryAdd*, que no iniciará ninguna excepción cuando se intente agregar una clave que ya existe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
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

Al final de un método de actor, el administrador de estado guarda automáticamente los valores agregados o modificados por una operación de inserción o actualización. El "guardado" puede incluir la persistencia en el disco y la replicación, dependiendo de la configuración utilizada. Los valores que no se han modificado no se conservan ni se replican. Si no se ha modificado ningún valor, la operación de guardar no hace nada. En caso de que se produzca un error al guardar, el estado modificado se descarta y se vuelve a cargar el estado original.

El estado también se puede guardar manualmente al llamar al método `SaveStateAsync` en la base de actor:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```

### Eliminación del estado
El estado puede quitarse permanentemente del administrador de estado de un actor mediante la llamada al método *Remove*. Este método iniciará la excepción `KeyNotFoundException` cuando se intente quitar una clave que no existe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

El estado también se puede quitar de manera permanente mediante el método *TryRemove*, que no iniciará ninguna excepción cuando se intente quitar una clave que no existe:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
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

## Pasos siguientes
* [Notas sobre la serialización del tipo de Reliable Actors de Service Fabric](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
* [Polimorfismo de actores y patrones de diseño orientado a objetos](service-fabric-reliable-actors-polymorphism.md)
* [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)
* [Documentación de referencia de la API de actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de ejemplo](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->