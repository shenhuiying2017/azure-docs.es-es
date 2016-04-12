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
   ms.date="03/10/2016"
   ms.author="masnider"/>


# Configuración del Administrador de recursos de clúster para los servicios de Service Fabric
Service Fabric Cluster Resource Manager permite disfrutar de un control muy exhaustivo de las reglas que rigen cada servicio con nombre individual. Cada servicio puede especificar reglas concretas sobre cómo se debe asignar en el clúster y definir el conjunto de métricas sobre las que desea informar, incluida su importancia para ese servicio. Por lo general, la configuración de los servicios se divide en tres tareas distintas:

1. Configuración de restricciones de colocación
2. Configuración de métricas
3. Configuración de reglas de selección de posición avanzadas (menos frecuentes)

Vamos a analizar cada una de ellas:

## Restricciones de selección de ubicación
Las restricciones de posición se utilizan para controlar en qué nodos del clúster puede ejecutarse realmente un servicio. Normalmente, verá un servicio con nombre determinado o todos los servicios de un tipo restringidos a la ejecución en un tipo de nodo concreto, pero las restricciones de posición se pueden extender: es posible definir cualquier conjunto de propiedades según el tipo de nodo y, luego, seleccionarlas con restricciones cuando se cree el servicio. Las restricciones de posición también se pueden actualizar de forma dinámica mientras dure el servicio, lo que le permite responder a los cambios en el clúster.

## Métricas
Las métricas son los recursos según los cuales se debe equilibrar este servicio, incluida información sobre qué cantidad de ese recurso consume de forma predeterminada cada réplica o instancia. Las métricas también incluyen una ponderación que indica su importancia para el servicio, en caso de que resulte necesario realizar compensaciones.

## Otras reglas de posición
Existen otros tipos de reglas de posición que resultan principalmente útiles en los clústeres con distribución geográfica o en otros escenarios menos habituales. Estas se configuran mediante correlaciones o directivas. Aunque no se utilizan en muchos escenarios, las describiremos para que este artículo esté completo.

## Pasos siguientes
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- Afinidad es un modo en que puede configurar los servicios. No es muy común, pero si lo necesita puede encontrar información [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md).
- Existen muchas reglas de colocación diferentes que se pueden configurar en el servicio para administrar escenarios adicionales. Puede encontrar información sobre esas directivas de colocación diferentes [aquí](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).
- Empiece desde el principio y [obtenga una introducción al Administrador de recursos de clúster de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
- Para más información sobre cómo el Administrador de recursos de clúster administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).

<!---HONumber=AcomDC_0316_2016-->