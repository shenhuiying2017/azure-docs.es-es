---
title: Enumerar actores en Azure Service Fabric | Microsoft Docs
description: Aprenda a enumerar Reliable Actors y sus metadatos.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: d5d6ac87db18815aa945d6964338626365b08e64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207274"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Enumerar Reliable Actors de Service Fabric
El servicio Reliable Actors permite que un cliente enumere los metadatos sobre los actores que el servicio hospeda. Debido a que el servicio de actor es un servicio con estado particionado, la enumeración se realiza por partición. Dado que cada partición puede contener muchos actores, la enumeración se devuelve como un conjunto de resultados paginados. Las páginas entran en bucle hasta que se leen todas. El siguiente ejemplo muestra cómo crear una lista de todos los actores activos en una partición de un servicio de actor:

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

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Pasos siguientes
* [Administración de estados de los actores](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida de un actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
* [Documentación de referencia de la API de actores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de ejemplo de .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de ejemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
