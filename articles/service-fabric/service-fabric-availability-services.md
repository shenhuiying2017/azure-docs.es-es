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
ms.openlocfilehash: 3e46b4bdcf7b55c31afe5e7bc84a1fb95ad98701
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
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

Si la réplica principal deja de funcionar, Service Fabric convierte una de las réplicas secundarias activas en la nueva réplica principal. Esta réplica secundaria activa ya tiene la versión actualizada del estado (a través de la *replicación*) y puede continuar procesando más operaciones de lectura y escritura. Este proceso se conoce como reconfiguración y se describe con mayores detalles en [Reconfiguración](service-fabric-concepts-reconfiguration.md).

Este concepto de una réplica como secundaria activa o principal se conoce como el rol de réplica. Se describen con mayor detalle en [Réplicas e instancias](service-fabric-concepts-replica-lifecycle.md). 

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

- [Escalado de aplicaciones de Service Fabric](service-fabric-concepts-scalability.md)
- [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)
- [Definición y administración del estado](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)
