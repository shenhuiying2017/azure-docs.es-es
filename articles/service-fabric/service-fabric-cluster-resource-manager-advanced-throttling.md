---
title: "Limitación de Cluster Resource Manager de Service Fabric | Microsoft Docs"
description: Aprenda a configurar las limitaciones proporcionadas por Cluster Resource Manager de Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 8a8419497bda3f1df523d6aff28028548abc155a


---

# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Limitación del comportamiento de Cluster Resource Manager de Service Fabric
Aunque haya configurado correctamente Cluster Resource Manager, el clúster puede ver alterado su comportamiento. Por ejemplo, podría haber errores de nodos simultáneos o de dominios de error. ¿Y qué pasaría si sucediera esto durante una actualización? Cluster Resource Manager intenta corregir todos los errores, pero con esto se puede introducir una renovación en el clúster. Las limitaciones permiten proporcionar un mecanismo de respaldo para que el clúster pueda usar los recursos para estabilizarse: el nodo vuelve, las particiones de red se reparan y se implementan los bits corregidos.

Para ayudarle con este tipo de situaciones, Cluster Resource Manager de Service Fabric incluye varias limitaciones. Estas limitaciones son medidas bastante disruptivas. Los valores predeterminados de esta configuración no se deben cambiar, a menos que se hagan cálculos precisos sobre la cantidad de trabajo que el clúster puede hacer en paralelo.

Las limitaciones tienen valores predeterminados que, gracias a la experiencia, el equipo de Service Fabric encuentra que son adecuados. Si tiene que cambiarlos, debe ajustarlos a la carga real esperada. Puede determinar que necesita implementar ciertas limitaciones, incluso si esto significa que el clúster demora más tiempo en estabilizarse en situaciones principales.

## <a name="configuring-the-throttles"></a>Configuración de las limitaciones
Las limitaciones que se incluyen de forma predeterminada son las siguientes:

* GlobalMovementThrottleThreshold: esta configuración controla el número total de movimientos en el clúster durante cierto tiempo (definido como el valor en segundos de GlobalMovementThrottleCountingInterval).
* MovementPerPartitionThrottleThreshold: esta configuración controla el número total de movimientos en cualquier partición del servicio durante cierto tiempo (el valor en segundos de MovementPerPartitionThrottleCountingInterval).

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThreshold",
          "value": "1000"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      },
      {
          "name": "MovementPerPartitionThrottleThreshold",
          "value": "50"
      },
      {
          "name": "MovementPerPartitionThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

La mayoría de las veces, los clientes han usado estas limitaciones porque ya estaban en un entorno con restricción de recursos. Algunos ejemplos de ese entorno sería un ancho de banda de red limitado en nodos individuales, o discos que no pueden crear muchas réplicas en paralelo debido a las limitaciones de rendimiento. Estos tipos de restricciones significaban que las operaciones que se desencadenaban en respuesta a errores no se realizaban correctamente o serían lentas, incluso sin las restricciones. En estas situaciones, los clientes sabían que extendían la cantidad de tiempo que el clúster podría demorar para llegar a un estado estable. Los clientes también comprendían que podían terminar ejecutándose con una confiabilidad general menor mientras estaban con restricciones.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la forma en que Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo sobre cómo [describir un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)



<!--HONumber=Jan17_HO1-->


