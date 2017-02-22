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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configuración de Cluster Resource Manager para los servicios de Service Fabric
Cluster Resource Manager de Service Fabric permite un control muy exhaustivo de las reglas que rigen cada servicio con nombre. Cada instancia de servicio con nombre puede especificar reglas para definir cómo debe asignarse en el clúster. Cada servicio con nombre también puede definir el conjunto de métricas que incluirá en los informes, y su importancia para ese servicio. La configuración de los servicios se divide en tres tareas distintas:

1. Configuración de restricciones de colocación
2. Configuración de métricas
3. Configuración de directivas de ubicación avanzadas (menos frecuentes)

## <a name="placement-constraints"></a>Restricciones de selección de ubicación
Las restricciones de posición se utilizan para controlar en qué nodos del clúster puede ejecutarse realmente un servicio. Normalmente, una instancia de servicio con nombre determinada o todos los servicios de un tipo concreto se restringen a un tipo de nodo específico. Dicho esto, las restricciones de posición son extensibles: puede definir cualquier conjunto de propiedades para cada tipo de nodo y, después, seleccionarlas para ellos con restricciones cuando se crea el servicio. Las restricciones de posición también se pueden actualizar de forma dinámica mientras dure el servicio, lo que le permite responder a los cambios en el clúster. Las propiedades de un nodo concreto también se pueden actualizar de forma dinámica en el clúster. En [este artículo](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## <a name="metrics"></a>Métricas
Las métricas son el conjunto de recursos que necesita una instancia de servicio con nombre determinada. La configuración de las métricas de un servicio incluye la cantidad de ese recurso que cada réplica con estado o instancia sin estado de ese servicio consume de forma predeterminada. Las métricas también incluyen una ponderación que indica la importancia que el equilibrio tiene para ese servicio, en caso de que resulte necesario realizar compensaciones.

## <a name="other-placement-rules"></a>Otras reglas de posición
Hay otros tipos de reglas de ubicación que resultan útiles en clústeres con distribución geográfica o en otros escenarios menos habituales. Otras reglas de ubicación se configuran mediante correlaciones o directivas.

## <a name="next-steps"></a>Pasos siguientes
* Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para obtener más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)
* Afinidad es un modo en que puede configurar los servicios. No es muy común, pero si lo necesita puede encontrar información [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* Existen muchas reglas de colocación diferentes que se pueden configurar en el servicio para administrar escenarios adicionales. Puede encontrar información sobre esas directivas de colocación diferentes [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO4-->


