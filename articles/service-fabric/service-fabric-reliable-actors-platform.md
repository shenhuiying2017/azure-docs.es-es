<properties
   pageTitle="Cómo usan los actores de Fabric la plataforma Service Fabric"
   description="En este artículo se describe cómo usan los actores de Fabric las características de la plataforma de Service Fabric. Abarca los conceptos de la plataforma de Service Fabric desde el punto de vista de los desarrolladores de actores."
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
   ms.date="07/21/2015"
   ms.author="abhisram"/>

# Cómo usan los actores de Fabric la plataforma Service Fabric

## Conceptos del modelo de aplicación de Service Fabric para los actores
Los actores usan el modelo de aplicación de Service Fabric para administrar el ciclo de vida de la aplicación. Cada tipo actor se asigna a un [tipo de servicio](service-fabric-application-model.md#describe-a-service) de Service Fabric. El código de actor se [empaqueta](service-fabric-application-model.md#package-an-application) como una aplicación de Service Fabric y se [implementa](service-fabric-deploy-remove-applications.md#deploy-an-application) en el clúster.

Veamos el ejemplo de un proyecto de actor [creado con Visual Studio](service-fabric-reliable-actors-get-started.md) para ilustrar algunos de los conceptos anteriores.

El manifiesto de la aplicación, el manifiesto del servicio y el archivo de configuración Settings.xml se incluyen en el proyecto del servicio de actor cuando se crea la solución en Visual Studio. Esto se muestra en la captura de pantalla siguiente.

![][1]

El tipo de aplicación y la versión de la misma en que se empaqueta el actor se pueden encontrar si se examina el manifiesto de aplicación que se incluye en el proyecto del servicio de actor. El siguiente fragmento de un manifiesto de aplicación es un ejemplo de esto.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

El tipo de servicio al que se asigna el tipo de actor puede encontrarse si se examina el manifiesto del servicio que se incluye en el proyecto del servicio de actor. El siguiente fragmento de un manifiesto de servicio es un ejemplo de esto.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Cuando se crea el paquete de aplicación mediante Visual Studio, los registros en la ventana de resultados de compilación indican la ubicación del paquete de aplicación. Por ejemplo:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

A continuación, se muestra una lista parcial de la ubicación anterior (se omite el listado completo por razones de brevedad):

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───es-es
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───es-es
                    System.Fabric.Common.Internal.Strings.resources.dll

En el listado anterior se muestran los ensamblados que implementan el actor VoicemailBox incluido en el paquete de código dentro del paquete de servicio dentro del paquete de aplicación.

La solución de Visual Studio incluye los scripts de PowerShell que se usan para implementar la aplicación en el clúster y quitarla del mismo. Los scripts se señalan con círculos en la captura de pantalla siguiente.

![][2]

La administración posterior (es decir, las actualizaciones y la eliminación final) de la aplicación también se realiza mediante mecanismos de administración de aplicaciones de Service Fabric. Para obtener más información, consulte los temas sobre el [modelo de aplicación](service-fabric-application-model.md), [implementación y eliminación de la aplicación](service-fabric-deploy-remove-applications.md) y [actualización de la aplicación](service-fabric-application-upgrade.md).

## Escalabilidad de los servicios de actor
Los administradores de clústeres pueden crear uno o varios servicios de actor de cada tipo de servicio en el clúster. Cada uno de esos servicios de actor puede tener una o más particiones (de igual forma que cualquier otro servicio de Service Fabric). La capacidad de crear varios servicios de un tipo de servicio (que se asigna a un tipo de actor) y la capacidad de crear varias particiones para un servicio permiten que la aplicación de actor se escale. Consulte el artículo sobre [escalabilidad](service-fabric-concepts-scalability.md) para obtener más información.

> [AZURE.NOTE]Los servicios de actores sin estado necesitan tener un recuento de [instancias](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) de 1. No se admite tener más de una instancia de un servicio de actores sin estado en una partición. Por lo tanto, los servicios de actores sin estado no tienen la posibilidad de aumentar el recuento de instancias para lograr escalabilidad. Deben usar las opciones de escalabilidad que se describen en el [artículo sobre escalabilidad](service-fabric-concepts-scalability.md).

## Conceptos de partición de Service Fabric para los actores
El identificador de actor de un actor se asigna a una partición de un servicio de actor. El actor se crea dentro de la partición a la que se asigna su identificador de actor. Cuando se crea un actor, el tiempo de ejecución de los actores escribe un [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) que indica la partición en que se creó. A continuación, se muestra un ejemplo de este evento que indica que un actor con el identificador `-5349766044453424161` se creó en la partición `0583c745-1bed-43b2-9545-29d7e3448156` del servicio `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, aplicación `fabric:/VoicemailBoxAdvancedApplication`.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "0583c745-1bed-43b2-9545-29d7e3448156",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Otro actor con el identificador `-4952641569324299627` se creó en una partición distinta `c146fe53-16d7-4d96-bac6-ef54613808ff` del mismo servicio, como indica el evento siguiente.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "c146fe53-16d7-4d96-bac6-ef54613808ff",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*Nota:* algunos campos de los eventos anteriores se omiten para una mayor brevedad.

El identificador de partición puede usarse para obtener otra información sobre la partición. Por ejemplo, la herramienta de [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) puede usarse para ver información sobre la partición y el servicio y la aplicación a los que pertenece. En la captura de pantalla siguiente se muestra información sobre la partición `c146fe53-16d7-4d96-bac6-ef54613808ff`, que contiene el actor con el identificador `-4952641569324299627` en el ejemplo anterior.

![][3]

Los actores pueden obtener mediante programación el identificador de partición, el nombre del servicio, el nombre de la aplicación y otra información específica de la plataforma de Service Fabric a través de `Host.ActivationContext` y los miembros de la clase base `Host.StatelessServiceInitialization` o `Host.StatefulServiceInitializationParameters` de los que se deriva el tipo de actor. El fragmento de código siguiente muestra un ejemplo:

```csharp
public void ActorMessage<TState>(Actor<TState> actor, string message, params object[] args)
{
    string finalMessage = string.Format(message, args);
    this.ActorMessage(
        actor.GetType().ToString(),
        actor.Id.ToString(),
        actor.Host.ActivationContext.ApplicationTypeName,
        actor.Host.ActivationContext.ApplicationName,
        actor.Host.StatefulServiceInitializationParameters.ServiceTypeName,
        actor.Host.StatefulServiceInitializationParameters.ServiceName.ToString(),
        actor.Host.StatefulServiceInitializationParameters.PartitionId,
        actor.Host.StatefulServiceInitializationParameters.ReplicaId,
        FabricRuntime.GetNodeContext().NodeName,
        finalMessage);
}
```

### Conceptos de particiones de Service Fabric para actores sin estado
Los actores sin estado se crean dentro de una partición de un servicio sin estado de Service Fabric. El identificador de actor determina la partición en la que se crea el actor. El recuento de [instancias](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) de un servicio de actores sin estado debe ser 1. No es posible cambiar el recuento de instancias por cualquier otro valor. Por lo tanto, el actor se crea dentro de la instancia de servicio único dentro de la partición.

> [AZURE.TIP]El tiempo de ejecución de los actores de Fabric emite algunos [eventos relacionados con instancias de actores sin estado](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances). Son útiles para la supervisión del rendimiento y los diagnósticos.

Cuando se crea un actor sin estado, el tiempo de ejecución de los actores escribe un [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) que indica la partición y la instancia en que se creó. A continuación, se muestra un ejemplo de este evento que indica que un actor con el identificador `abc` se creó dentro de la instancia `130745709600495974` de la partición `8c828833-ccf1-4e21-b99d-03b14d4face3` del servicio `fabric:/HelloWorldApplication/HelloWorldActorService`, aplicación `fabric:/HelloWorldApplication`.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

*Nota:* algunos campos del evento anterior se omiten para una mayor brevedad.

### Conceptos de particiones de Service Fabric para actores con estado
Los actores con estado se crean dentro de una partición del servicio con estado de Service Fabric. El identificador de actor determina la partición en la que se crea el actor. Cada partición del servicio puede tener una o más [réplicas](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) que se colocan en nodos distintos del clúster. Al disponer de varias réplicas, se ofrece confiabilidad para el estado del actor. El administrador de recursos optimiza la colocación según el error y los dominios de actualización disponibles en el clúster. Dos réplicas de la misma partición nunca se colocan en el mismo nodo. Los actores siempre se crean en la réplica principal de la partición al que se asigna su identificador de actor.

> [AZURE.TIP]El tiempo de ejecución de actores de Fabric emite algunos [eventos relacionados con las réplicas de actores con estado](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas). Son útiles para la supervisión del rendimiento y los diagnósticos.

Recuerde que en el [ejemplo de VoiceMailBoxActor que se explicó anteriormente](#service-fabric-partition-concepts-for-actors), el actor con el identificador `-4952641569324299627` se creó en la partición `c146fe53-16d7-4d96-bac6-ef54613808ff`. El evento EventSource de ese ejemplo también indicaba que el actor se creó en la réplica `130745418574851853` de dicha partición. Esta era la réplica principal de esa partición en el momento en que se creó el actor. La captura de pantalla siguiente del Explorador de Service Fabric lo confirma.

![][4]

## Opciones de proveedores de estado de actor
Hay algunos proveedores de estado de actor predeterminados que se incluyen en el tiempo de ejecución de los actores. Para elegir un proveedor de estado adecuado para un servicio de actor, es necesario comprender la forma en que los proveedores de estado usan las características subyacentes de la plataforma de Service Fabric para que el estado de actor esté altamente disponible.

De manera predeterminada, un actor con estado usa el proveedor de estado de los actores de almacén de valor de clave. Este proveedor de estado está integrado en el almacén de pares clave y valor distribuido que ofrece la plataforma de Service Fabric. El estado se guarda de forma duradera en el disco local del nodo que hospeda la [réplica](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) principal, así como también se replica y guarda de forma duradera en los discos locales de los nodos que hospedan las réplicas secundarias. El estado guardado se completa solo cuando un cuórum de réplicas ha confirmado el estado en sus discos locales. El almacén de pares clave y valor tiene capacidades avanzadas para detectar incoherencias como un progreso falso y las corrige automáticamente.

El tiempo de ejecución de actores también incluye `VolatileActorStateProvider`. Este proveedor de estado replica el estado en las réplicas, pero el estado permanece en memoria en la réplica. Si una réplica deja de funcionar y vuelve a activarse, se vuelve a generar su estado a partir de la otra réplica. Sin embargo, si todas las réplicas (copias del estado) dejan de funcionar simultáneamente, se perderán los datos de estado. Por lo tanto, este proveedor de estado es adecuado para aplicaciones donde los datos pueden sobrevivir a errores de algunas réplicas y pueden sobrevivir a las conmutaciones por error planeadas, como las actualizaciones. Si se pierden todas las réplicas (copias), los datos deben volver a crearse usando mecanismos externos a Service Fabric. Puede configurar su actor con estado para que use proveedores de estado de actores volátiles, si agrega el atributo `VolatileActorStateProvider` a la clase de actor o un atributo de nivel de ensamblado.

En el siguiente fragmento de código se muestra cómo cambiar todos los actores en el ensamblado que no tiene un atributo de proveedor de estado explícito para usar `VolatileActorStateProvider`.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

En el fragmento de código siguiente se muestra cómo cambiar el proveedor de estado de un tipo de actor determinado, `VoicemailBox` en este caso, para que sea `VolatileActorStateProvider`.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Tenga en cuenta que al cambiar el proveedor de estado, es necesario que se vuelva a crear el servicio de actor. Los proveedores de estado no se pueden cambiar como parte de la actualización de la aplicación.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=July15_HO4-->