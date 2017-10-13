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
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Limitación de Cluster Resource Manager de Service Fabric
Aunque haya configurado correctamente Cluster Resource Manager, el clúster puede ver alterado su comportamiento. Por ejemplo, podría haber errores simultáneos de nodos y de dominios de error. ¿Qué pasaría si esto sucediera durante una actualización? Cluster Resource Manager siempre intenta corregirlo todo y consume los recursos del clúster tratando de reorganizar y corregir el clúster. Las limitaciones contribuyen a ofrecer un mecanismo de protección a fin de que el clúster pueda usar los recursos para estabilizarse: el nodo se recupera, las particiones de red se reparan y los bits corregidos se implementan.

Para ayudarle con este tipo de situaciones, Cluster Resource Manager de Service Fabric incluye varias limitaciones. Estas limitaciones son medidas bastante disruptivas. Generalmente no debe cambiarse sin realizar una planeación y pruebas rigurosas.

Si modifica las limitaciones de Cluster Resource Manager, debe ajustarlas a la carga real esperada. Tal vez necesite implementar ciertas limitaciones, aunque esto signifique que el clúster tarde más en estabilizarse en algunas situaciones. Es preciso realizar pruebas para determinar los valores correctos de las limitaciones. Las limitaciones deben suficientemente altas para que el clúster responda a los cambios en una cantidad de tiempo razonable y suficientemente bajas para evitar realmente el consumo excesivo de recursos. 

Hemos observado que, la mayoría de las veces, los clientes usan estas limitaciones porque ya estaban en un entorno con restricción de recursos. Entre los ejemplos estaría un ancho de banda de red limitado en nodos individuales o discos que no pueden crear muchas réplicas con estado en paralelo por limitaciones de rendimiento. Sin limitaciones, las operaciones podrían sobrecargar estos recursos, lo que provocaría que las operaciones no se realizaran o fueran lentas. En estas situaciones, los clientes usaban las limitaciones y sabían que aumentaban la cantidad de tiempo que el clúster tardaría en llegar a un estado estable. Los clientes también comprendían que podían terminar ejecutándose con una confiabilidad general menor mientras estaban con restricciones.


## <a name="configuring-the-throttles"></a>Configuración de las limitaciones

Service Fabric tiene dos mecanismos de limitación del número de movimientos de replica. El mecanismo predeterminado anterior a Service Fabric 5.7 representa las limitaciones como un número absoluto de movimientos permitidos. Esto no funciona con los clústeres de todos los tamaños. En concreto, con los clústeres grandes el valor predeterminado puede ser demasiado pequeño, lo que ralentiza considerablemente el equilibrio aunque sea necesario, al tiempo que no tiene ningún efecto en los clústeres más pequeños. Este mecanismo anterior se ha reemplazado por las limitaciones basadas en porcentajes, que se escalan mejor con clústeres dinámicos en los que el número de nodos y servicios cambia periódicamente.

Las limitaciones se basan en un porcentaje del número de réplicas en los clústeres. Las limitaciones basadas en porcentajes permiten expresar la regla: "no mover más del 10% de las réplicas en un intervalo de 10 minutos", por ejemplo.

Las opciones de configuración de las limitaciones basadas en porcentajes son:

  - GlobalMovementThrottleThresholdPercentage: número máximo de movimientos permitidos en un clúster en cualquier momento, expresado como porcentaje del número total de réplicas en el clúster. 0 indica sin límite. El valor predeterminado es 0. Si se especifican esta opción y GlobalMovementThrottleThreshold, se usa el límite más conservador.
  - GlobalMovementThrottleThresholdPercentageForPlacement: número máximo de movimientos permitidos durante la fase de selección de ubicación, expresado como porcentaje del número total de réplicas en el clúster. 0 indica sin límite. El valor predeterminado es 0. Si se especifican esta opción y GlobalMovementThrottleThresholdForPlacement, se usa el límite más conservador.
  - GlobalMovementThrottleThresholdPercentageForBalancing: número máximo de movimientos permitidos durante la fase de equilibrio, expresado como porcentaje del número total de réplicas en el clúster. 0 indica sin límite. El valor predeterminado es 0. Si se especifican esta opción y GlobalMovementThrottleThresholdForBalancing, se usa el límite más conservador.

Al especificar el porcentaje de limitación, 5 % se especificaría como 0,05. El intervalo durante el que se controlan estas limitaciones es GlobalMovementThrottleCountingInterval, que se especifica en segundos.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Recuento predeterminado basado en limitaciones
Esta información se ofrece por si tiene clústeres anteriores o todavía se conservan estas configuraciones en clústeres que se han actualizado desde entonces. En general, se recomienda que se reemplacen por las limitaciones basadas en porcentajes mencionadas antes. Dado que las limitaciones basadas en porcentajes están deshabilitadas de manera predeterminada, estas limitaciones se mantienen como predeterminadas para un clúster hasta que se deshabilitan y se reemplazan por las limitaciones basadas en porcentajes. 

  - GlobalMovementThrottleThreshold: esta opción controla el número total de movimientos en el clúster durante cierto tiempo. La cantidad de tiempo se especifica en segundos como GlobalMovementThrottleCountingInterval. El valor predeterminado de GlobalMovementThrottleThreshold es 1000 y el valor predeterminado de GlobalMovementThrottleCountingInterval es 600.
  - MovementPerPartitionThrottleThreshold: esta opción controla el número total de movimientos para cualquier partición del servicio durante cierto tiempo. La cantidad de tiempo se especifica en segundos como MovementPerPartitionThrottleCountingInterval. El valor predeterminado de MovementPerPartitionThrottleThreshold es 50 y el valor predeterminado de MovementPerPartitionThrottleCountingInterval es 600.

La configuración de estas limitaciones sigue el mismo patrón que las limitaciones basadas en porcentajes.

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la forma en que Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager tiene muchas opciones para describir el clúster. Para obtener más información sobre ellas, consulte este artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).
