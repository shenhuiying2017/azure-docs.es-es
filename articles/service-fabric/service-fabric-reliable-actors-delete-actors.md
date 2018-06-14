---
title: Eliminar actores de Azure Service Fabric | Microsoft Docs
description: Aprenda a eliminar manualmente Reliable Actors de Service Fabric y su estado.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: fa4fe018a9e6b32158f5bbd13c44ff57069cb1cf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208342"
---
# <a name="delete-reliable-actors-and-their-state"></a>Eliminar Reliable Actors y su estado
La recolección de elementos no utilizados de actores desactivados solo elimina el objeto del actor, pero no los datos que se almacenan en el administrador de estados de un actor. Cuando se vuelve a activar un actor, sus datos vuelven a estar disponibles mediante el administrador de estados. Es posible que, en los casos en que los actores almacenan datos en el administrador de estados y se desactivan, pero nunca se vuelven a activar, haya que limpiar los datos.

El [servicio de actor](service-fabric-reliable-actors-platform.md) proporciona una función para eliminar actores desde un llamador remoto:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

La eliminación de un actor tiene las siguientes consecuencias, con independencia de que esté activo o no en ese momento:

* **Actor activo**
  * El actor se quita de la lista de actores activos y se desactiva.
  * Su estado se elimina permanentemente.
* **Actor inactivo**
  * Su estado se elimina permanentemente.

Un actor no puede llamar a Delete por sí mismo desde uno de sus métodos de actor. Esto se debe a que el actor no se puede eliminar mientras se está ejecutando en un contexto de llamada de actor, en el que el entorno en tiempo de ejecución ha obtenido un bloqueo en la llamada de actor para exigir el acceso uniproceso.

Para obtener más información sobre Reliable Actors, lea la información siguiente:
* [Recordatorios y temporizadores de los actores](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de actor](service-fabric-reliable-actors-events.md)
* [Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)
* [Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)
* [Documentación de referencia de la API de actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de ejemplo de C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de ejemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
