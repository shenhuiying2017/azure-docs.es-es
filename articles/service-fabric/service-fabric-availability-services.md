---
title: Disponibilidad de los servicios de Service Fabric | Microsoft Docs
description: "Describe la detección de errores, la conmutación por error y la recuperación para los servicios"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 1db7b4bcfa4b7c474a4b0eb4ef469a6cb1fe54a0


---
# <a name="availability-of-service-fabric-services"></a>Disponibilidad de los servicios de Service Fabric
En este artículo se ofrece una visión general de cómo Service Fabric mantiene la disponibilidad de un servicio.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidad de los servicios sin estado de Service Fabric
Los servicios de Service Fabric de Azure pueden ser con o sin estado. Un servicio sin estado es un servicio de aplicación que no tiene ningún [estado persistente local](service-fabric-concepts-state.md).

Para crear un servicio sin estado, es necesario definir un recuento de instancias. El recuento de instancias define el número de instancias de la lógica de la aplicación del servicio sin estado que debe estar ejecutándose en el clúster. El aumento del número de instancias es la manera recomendada de escalar horizontalmente un servicio sin estado.

Cuando se detecta un error en cualquier instancia de un servicio sin estado, se crea una nueva instancia en algún nodo elegible del clúster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidad de los servicios con estado de Service Fabric
Un servicio con estado tiene algún estado asociado a él. En Service Fabric, un servicio con estado se modela como un conjunto de réplicas. Cada réplica es una instancia del código del servicio que tiene una copia del estado. Las operaciones de lectura y escritura se realizan en una réplica (denominada principal). Los cambios en el estado debidos a las operaciones de escritura se *replican* en varias otras réplicas (denominadas secundarias activas). La combinación de las réplicas principal y secundaria activa conforman el conjunto de réplicas del servicio.

Solo puede haber una réplica principal que atienda solicitudes de lectura y escritura, pero puede haber varias réplicas secundarias activas. El número de réplicas secundarias activas es configurable y un mayor número de réplicas puede tolerar un mayor número de errores de hardware y software simultáneos.

Si la réplica principal deja de funcionar, Service Fabric convierte una de las réplicas secundarias activas en la nueva réplica principal. Esta réplica secundaria activa ya tiene la versión actualizada del estado (a través de la *replicación*) y puede continuar procesando más operaciones de lectura y escritura.

Este concepto de una réplica como secundaria activa o principal se conoce como el rol de réplica.

### <a name="replica-roles"></a>Roles de réplica
El rol de una réplica se usa para administrar el ciclo de vida del estado que se está administrando por esa réplica. Una réplica cuyo rol sea solicitudes de lectura de servicios principales. El servidor principal también controla todas las solicitudes de escritura mediante la actualización de su estado y la replicación de los cambios. Estos cambios se aplican a las secundarias activas del conjunto de réplicas. El trabajo de una secundaria activa es recibir cambios de estado que la réplica principal ha replicado y actualizar su vista del estado.

> [!NOTE]
> Los modelos de programación de nivel superior, como el [marco de Reliable Actors](service-fabric-reliable-actors-introduction.md) y [Reliable Services](service-fabric-reliable-services-introduction.md), abstraen el concepto de rol de réplica del desarrollador. En Actors, la noción de rol es innecesaria, mientras que en Services está visible si es necesario, pero simplificado en gran medida.
>
>

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

* [Escalabilidad de servicios de Service Fabric](service-fabric-concepts-scalability.md)
* [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)
* [Definición y administración del estado](service-fabric-concepts-state.md)
* [Reliable Services](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO1-->


