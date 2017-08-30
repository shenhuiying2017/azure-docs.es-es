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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: b0d4615a9b8ab566f69b27e4879b6e2d597b4990
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---

# <a name="availability-of-service-fabric-services"></a>Disponibilidad de los servicios de Service Fabric
En este artículo se ofrece una visión general de cómo Service Fabric mantiene la disponibilidad de un servicio.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidad de los servicios sin estado de Service Fabric
Los servicios de Service Fabric de Azure pueden ser con o sin estado. Un servicio sin estado es un servicio de aplicación que no tiene un [estado local](service-fabric-concepts-state.md), es decir, no tiene que tener una alta disponibilidad o confiabilidad.

Para crear un servicio sin estado, es necesario definir `InstanceCount`. El recuento de instancias define el número de instancias de la lógica de la aplicación del servicio sin estado que debe estar ejecutándose en el clúster. El aumento del número de instancias es la manera recomendada de escalar horizontalmente un servicio sin estado.

Cuando una instancia de un servicio con nombre sin estado produce un error, se crea una nueva instancia en algún nodo válido del clúster. Por ejemplo, una instancia de servicio sin estado podría producir error en el Nodo1 y crearse de nuevo en el Nodo5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidad de los servicios con estado de Service Fabric
Un servicio con estado tiene algún estado asociado a él. En Service Fabric, un servicio con estado se modela como un conjunto de réplicas. Cada réplica es una instancia en ejecución del código del servicio que también tiene una copia del estado de ese servicio. Las operaciones de lectura y escritura se realizan en una réplica (denominada principal). Los cambios en el estado de operaciones de escritura se *replican* en otras réplicas del conjunto de réplicas (llamadas secundarias activas) y se aplican. 

Solo puede haber una réplica principal, pero puede haber varias réplicas secundarias activas. El número de réplicas secundarias activas es configurable y un mayor número de réplicas puede tolerar un mayor número de errores de hardware y software simultáneos.

Si la réplica principal deja de funcionar, Service Fabric convierte una de las réplicas secundarias activas en la nueva réplica principal. Esta réplica secundaria activa ya tiene la versión actualizada del estado (a través de la *replicación*) y puede continuar procesando más operaciones de lectura y escritura.

Este concepto de una réplica como secundaria activa o principal se conoce como el rol de réplica.

### <a name="replica-roles"></a>Roles de réplica
El rol de una réplica se usa para administrar el ciclo de vida del estado que se está administrando por esa réplica. Una réplica cuyo rol sea solicitudes de lectura de servicios principales. El servidor principal también controla todas las solicitudes de escritura mediante la actualización de su estado y la replicación de los cambios. Estos cambios se aplican a las secundarias activas del conjunto de réplicas. El trabajo de una secundaria activa es recibir cambios de estado que la réplica principal ha replicado y actualizar su vista del estado.

> [!NOTE]
> Los modelos de programación de nivel superior, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) y [Reliable Services](service-fabric-reliable-services-introduction.md) ocultan el concepto de rol de réplica del desarrollador. En Actors, la noción de rol es innecesaria, mientras que en Services se simplifica enormemente en la mayoría de los escenarios.
>

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

- [Escalado de aplicaciones de Service Fabric](service-fabric-concepts-scalability.md)
- [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)
- [Definición y administración del estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

