<properties
   pageTitle="Configuración de servicios con el Administrador de recursos de clúster de Service Fabric | Microsoft Azure"
   description="Descripción de un servicio Service Fabric mediante la especificación de métricas, restricciones de posición y otras directivas de posición."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>


# Configuración del Administrador de recursos de clúster para los servicios de Service Fabric
Service Fabric Cluster Resource Manager permite disfrutar de un control muy exhaustivo de las reglas que rigen cada servicio con nombre individual. Cada instancia de servicio con nombre puede especificar reglas sobre cómo se debe asignar en el clúster y definir el conjunto de métricas sobre las que desea informar, incluida su importancia para ese servicio. Por lo general, la configuración de los servicios se divide en tres tareas distintas:

1. Configuración de restricciones de colocación
2. Configuración de métricas
3. Configuración de directivas de selección de posición avanzadas (menos frecuentes)

Vamos a analizar cada una de ellas:

## Restricciones de selección de ubicación
Las restricciones de posición se utilizan para controlar en qué nodos del clúster puede ejecutarse realmente un servicio. Normalmente, observará que la ejecución de una instancia de servicio con nombre determinada o todos los servicios de un tipo concreto se restringe a un tipo de nodo específico. Dicho esto, las restricciones de posición son extensibles: puede definir cualquier conjunto de propiedades para cada tipo de nodo y, después, seleccionarlas para ellos con restricciones cuando se crea el servicio. Las restricciones de posición también se pueden actualizar de forma dinámica mientras dure el servicio, lo que le permite responder a los cambios en el clúster. Las propiedades de un nodo concreto también se pueden actualizar de forma dinámica en el clúster. En [este artículo](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties) puede encontrar más información sobre las restricciones de colocación y cómo configurarlas.

## Métricas
Las métricas son el conjunto de recursos que necesita una instancia de servicio con nombre determinada, incluida información sobre la cantidad de ese recurso que consume de forma predeterminada cada instancia sin estado o la réplica con estado de ese servicio. Las métricas también incluyen una ponderación que indica la importancia que reviste para el servicio alcanzar un equilibrio en ella, en caso de que resulte necesario realizar compensaciones.

## Otras reglas de posición
Existen otros tipos de reglas de posición que resultan principalmente útiles en los clústeres con distribución geográfica o en otros escenarios menos habituales. Estas se configuran mediante correlaciones o directivas. Aunque no se utilizan en muchos escenarios, las describiremos para que este artículo esté completo.

## Pasos siguientes
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- Afinidad es un modo en que puede configurar los servicios. No es muy común, pero si lo necesita puede encontrar información [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md).
- Existen muchas reglas de colocación diferentes que se pueden configurar en el servicio para administrar escenarios adicionales. Puede encontrar información sobre esas directivas de colocación diferentes [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).
- Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
- Para más información sobre la forma en que Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte el artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

<!---HONumber=AcomDC_0824_2016-->