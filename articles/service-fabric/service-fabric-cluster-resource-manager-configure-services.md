---
title: "Especificación de la configuración de las métricas y la ubicación en microservicios de Azure | Microsoft Docs"
description: "Descripción de un servicio de Service Fabric mediante la especificación de métricas, restricciones de ubicación y otras directivas de ubicación."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0ae4e874d0fd0922295a4ec7ad719a0a1fb108c8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configuración de Cluster Resource Manager para los servicios de Service Fabric
Cluster Resource Manager de Service Fabric permite un control muy exhaustivo de las reglas que rigen cada servicio con nombre. Cada servicio con nombre puede especificar reglas sobre cómo debe asignarse en el clúster. Cada servicio con nombre también puede definir el conjunto de métricas que incluirá en los informes, y su importancia para ese servicio. La configuración de los servicios se divide en tres tareas distintas:

1. Configuración de restricciones de colocación
2. Configuración de métricas
3. Configuración de directivas de ubicación avanzadas (menos frecuentes)

## <a name="placement-constraints"></a>Restricciones de selección de ubicación
Las restricciones de posición se utilizan para controlar en qué nodos del clúster puede ejecutarse realmente un servicio. Normalmente, una instancia de servicio con nombre determinada o todos los servicios de un tipo concreto se restringen a un tipo de nodo específico. Las restricciones de colocación son extensibles. Puede definir cualquier conjunto de propiedades por tipo de nodo y luego seleccionar para ellas restricciones al crear servicios. También puede cambiar las restricciones de colocación de un servicio mientras se ejecuta. De esta forma, puede responder a los cambios en el clúster o a los requisitos del servicio. Las propiedades de un nodo concreto también se pueden actualizar de forma dinámica en el clúster. En [este artículo](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Métricas
Las métricas son el conjunto de recursos que necesita un servicio con nombre determinado. La configuración de las métricas de un servicio incluye la cantidad de ese recurso que cada réplica con estado o instancia sin estado de ese servicio consume de forma predeterminada. Las métricas también incluyen una ponderación que indica la importancia que el equilibrio tiene para ese servicio, en caso de que resulte necesario realizar compensaciones.

## <a name="advanced-placement-rules"></a>Reglas de colocación avanzadas
Existen otros tipos de reglas de colocación que son útiles en escenarios menos comunes. A continuación, se indican algunos ejemplos:
- Restricciones que ayudan con los clústeres distribuidos geográficamente
- Determinadas arquitecturas de aplicaciones

Otras reglas de ubicación se configuran mediante correlaciones o directivas.

## <a name="next-steps"></a>pasos siguientes
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre las métricas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- Afinidad es un modo en que puede configurar los servicios. No es muy común, pero si lo necesita puede encontrar información [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Existen muchas reglas de colocación diferentes que se pueden configurar en el servicio para administrar escenarios adicionales. Puede encontrar información sobre esas directivas de colocación diferentes [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager tiene muchas opciones para describir el clúster. Para obtener más información sobre ellas, consulte este artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).
