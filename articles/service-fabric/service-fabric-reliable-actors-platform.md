<properties
   pageTitle="Reliable Actors en Service Fabric | Microsoft Azure"
   description="Describe cómo Reliable Actors se superpone encima de Reliable Services y usa las características de la plataforma Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Uso de la plataforma Service Fabric por parte de actores confiables

En este artículo se explica cómo funciona Reliable Actors en la plataforma de Service Fabric. Reliable Actors se ejecuta en un marco de trabajo que se hospeda en la implementación de un servicio de confianza con estado llamado *servicio de actor*. El servicio de actor contiene todos los componentes necesarios para administrar el ciclo de vida y el envío de mensajes de sus actores:

 - El tiempo de ejecución de los actores administra el ciclo de vida y la recolección de elementos no utilizados, además de aplicar el acceso uniproceso.
 - Un servicio de actor que actúa como agente de escucha remoto acepta llamadas de acceso remoto a los actores y las envía a un distribuidor que las redirige a la instancia de actor correspondiente.
 - El proveedor de estado de actores encapsula proveedores de estado (como el proveedor de estado de Reliable Collections) y proporciona un adaptador para la administración de estados de los actores.

En conjunto, estos componentes forman el marco de trabajo de Reliable Actor.

## Servicio en capas

Debido a que el servicio de actor es un servicio de confianza, todos los conceptos de [modelo de aplicación](service-fabric-application-model.md), ciclo de vida, [empaquetado](service-fabric-application-model.md#package-an-application), [implementación]((service-fabric-deploy-remove-applications.md#deploy-an-application), actualización y escalado de Reliable Services se aplican del mismo modo a los servicios de actor.

![Servicio de actor en capas][1]

El diagrama anterior muestra la relación que existe entre los marcos de trabajo de la aplicación Service Fabric y el código de usuario. Los elementos en azul representan el marco de trabajo de la aplicación Reliable Services, el color naranjo representa el marco de trabajo de Reliable Actor y el verde, el código de usuario.


En Reliable Services, el servicio hereda la clase `StatefulService`, que deriva de `StatefulServiceBase` (o `StatelessService`, en el caso de servicios sin estado). En Reliable Actors, usa el servicio de actor, que es una implementación diferente de la clase `StatefulServiceBase` que implementa el patrón de actor donde se ejecutan los actores. Debido a que el servicio de actor es simplemente una implementación de `StatefulServiceBase`, puede escribir su propio servicio que deriva de `ActorService` e implementar características de nivel de servicio del mismo modo que lo haría si hereda `StatefulService`, como:

 - Copia de seguridad y restauración del servicio.
 - Funcionalidad compartida para todos los actores, por ejemplo, un interruptor.
 - Llamadas de procedimiento remoto en el servicio de actor mismo, así como en otro actor individual. 

### Uso del servicio de actor

Las instancias de actor tienen acceso al servicio de actor en que se ejecutan. A través del servicio de actor, las instancias de actor pueden obtener de manera programática el contexto de servicio que tiene el id. de partición, el nombre del servicio, el nombre de la aplicación y otra información específica de la plataforma de Service Fabric.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```

Del mismo modo que ocurre con todos los Reliable Services, el servicio de actor se debe registrar con un tipo de servicio en el tiempo de ejecución de Service Fabric. Con el fin de que el servicio de actor ejecute las instancias de actor, el tipo de actor también se debe registrar con el servicio de actor. El método de registro `ActorRuntime` realiza este trabajo para los actores. En el caso más simple, puede registrar el tipo de actor y se usará implícitamente el servicio de actor con configuración predeterminada:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```  

Como alternativa, puede usar una expresión lambda para construir el servicio de actor. Esto le permite configurar el servicio de actor, así como también construir de manera explícita las instancias de actor, donde que puede insertar dependencias en su actor a través de su constructor:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

### Métodos de servicio de actor

El servicio de actor implementa `IActorService` el que, a su vez, implementa `IService`. Esta es la interfaz que usa el servicio remoto de Reliable Services, que permite llamadas de procedimiento remoto en los métodos de servicio. Contiene métodos de nivel de servicio a los que se puede llamar de manera remota a través del servicio remoto.


#### Enumeración de los actores

El servicio de actor permite que un cliente enumere los metadatos sobre los actores que hospeda el servicio. Debido a que el servicio de actor es un servicio con estado particionado, la enumeración se realiza por partición. Dado que cada partición puede contener una gran cantidad de actores, la enumeración se devuelve como un conjunto de resultados paginados. Las páginas entran en bucle hasta que se leen todas. El siguiente ejemplo muestra cómo crear una lista de todos los actores activos en una partición de un servicio de actor:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);
                
    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

#### Eliminación de actores

El servicio de actor también proporciona una función para eliminar actores:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);
            
await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```

Si desea más información sobre cómo eliminar actores y su estado, consulte la [documentación sobre el ciclo de vida de un actor](service-fabric-reliable-actors-lifecycle.md).

### Servicio de actor personalizado

Si usa la expresión lambda para registrar un actor, también puede registrar su propio servicio de actor personalizado que deriva de `ActorService`, en el que puede implementar su propia funcionalidad de nivel de servicio. Para ello, se escribe una clase de servicio que hereda `ActorService`. Un servicio de actor personalizado hereda toda la funcionalidad de tiempo de ejecución de los actores de `ActorService` y se puede usar para implementar sus propios métodos de servicio.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```


#### Implementación de la copia de seguridad y restauración de los actores

 En el siguiente ejemplo, el servicio de actor personalizado expone un método para crear una copia de seguridad de los datos de actor mediante el uso del agente de escucha remoto que ya se encuentra presente en `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(...));
    }
}
```

En este ejemplo, `IMyActorService` es un contrato remoto que implementa `IService` y, luego, lo implementa `MyActorService`. Si agrega este contrato remoto, los métodos de `IMyActorService` ahora también están a disposición de un cliente si se crea un proxy remoto a través de `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```


## Modelo de aplicación

Los servicios de actor son Reliable Services, por lo que el modelo de aplicación es el mismo. Sin embargo, las herramientas de generación del marco de trabajo de actor generan gran cantidad de los archivos del modelo de aplicación por usted.

### Manifiesto de servicio
 
El contenido del archivo ServiceManifest.xml del servicio de actor se genera automáticamente con las herramientas de generación del marco de trabajo de actor. En ella se incluye:

 - El tipo de servicio de actor. El nombre de tipo se genera en función del nombre del proyecto de actor. La marca HasPersistedState se define según el atributo de persistencia del actor.
 - Paquete de código.
 - Paquete de configuración.
 - Recursos y puntos de conexión

### Manifiesto de aplicación

Las herramientas de generación del marco de trabajo de actor crea automáticamente una definición de servicio predeterminada para el servicio de actor. Las herramientas de generación rellenan las propiedades predeterminadas de servicio:

 - El atributo de persistencia del actor determina la cantidad de conjuntos de réplicas. Cada vez que cambia el atributo de persistencia del actor, se restablecerá también la cantidad de conjuntos de réplicas en la definición de servicio predeterminada.
 - El intervalo y el esquema de partición se define en Int64 uniforme con todo el intervalo de claves Int64.

## Conceptos de partición de Service Fabric para los actores

Los servicios de actor son servicios con estado particionados. Cada partición de un servicio de actor contiene un conjunto de actores. Las particiones de servicio se distribuyen automáticamente sobre varios nodos en Service Fabric. Como resultado de esto, se distribuyen las instancias de actor.

![Partición y distribución de actores][5]

Es posible crear Reliable Services con distintos intervalos de claves de partición y esquemas de partición. El servicio de actor usa el esquema de partición Int64 con todo el intervalo de claves Int64 para asignar actores a las particiones.

### Id. de actor

Cada actor que se crea en el servicio tiene asociado un id. único, el que se representa con la clase `ActorId`. La clase `ActorId` es un valor de id. opaco que se puede usar para la distribución uniforme de actores en todas las particiones de servicio a través de la generación de id. aleatorios:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```

A cada `ActorId` se aplica un algoritmo hash en Int64, que es el motivo por el cual el servicio de actor debe usar un esquema de partición Int64 con todo el intervalo de claves Int64. Sin embargo, los valores de id. personalizados se pueden usar para un valor `ActorID`, incluidos GUID, cadenas y valores Int64.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```

Cuando se usan GUID y cadenas, se aplica un algoritmo hash a los valores en un Int64. Sin embargo, cuando se brinda explícitamente un valor Int64 a una clase `ActorId`, el valor Int64 se asignará directamente a una partición sin otra aplicación del algoritmo hash. Se puede usar para controlar en qué partición se colocan los actores.

## Pasos siguientes
 - [Administración de estados de los actores](service-fabric-reliable-actors-state-management.md)
 - [Ciclo de vida de un actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
 - [Documentación de referencia de la API de actores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de ejemplo](https://github.com/Azure/servicefabric-samples)

 
<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

<!---HONumber=AcomDC_0406_2016-->