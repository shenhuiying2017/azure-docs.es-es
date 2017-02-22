---
title: "Desfragmentación de métricas en Azure Service Fabric | Microsoft Docs"
description: "Información general del uso de la desfragmentación o el empaquetado como estrategia para métricas en Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 5ef6381f7d182c818171eca3e3d32a00bc30268e


---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentación de métricas y carga en Service Fabric
Cluster Resource Manager de Service Fabric se ocupa principalmente del equilibrio en cuanto a distribuir la carga, de forma que se tiene la seguridad de que los nodos del clúster se utilizan por igual. Tener cargas de trabajo distribuidas es el diseño más seguro en términos de superación de errores, ya que asegura que un error no se lleve un gran porcentaje de una carga de trabajo determinada. Cluster Resource Manager de Service Fabric admite también una estrategia diferente: la desfragmentación. Este término generalmente significa que, en lugar de intentar distribuir el uso de una métrica en el clúster, lo que deberíamos intentar realmente es consolidarla. La consolidación es una inversión afortunada de nuestra estrategia normal: en lugar de minimizar la desviación estándar promedio de la carga métrica, Cluster Resource Manager intenta lograr aumentos en la desviación. Pero, ¿para qué sirve esta estrategia?

Si ha repartido la carga equitativamente entre los nodos del clúster, entonces ha consumido algunos de los recursos que ofrecen los nodos. Sin embargo, algunas cargas de trabajo crean servicios que son excepcionalmente grandes y consumen gran parte de un nodo. En estos casos, es posible que entre el 75 % y el 95 % de los recursos de un nodo termine dedicado a un solo objeto de servicio. Las cargas de trabajo de gran tamaño no son problema. Cluster Resource Manager determina, en el momento de la creación del servicio, que debe reorganizar el clúster para hacer espacio para esta carga de trabajo de gran tamaño. Sin embargo, mientras tanto, esa carga de trabajo debe esperar que se la programe en el clúster.

Si hay muchos servicios y estados para desplazar, colocar la carga de trabajo de gran tamaño en el clúster podría demorar más. Esto es probable si otras cargas de trabajo del clúster son de gran tamaño y, por lo tanto, demoran más en desplazarse. El equipo de Service Fabric midió los tiempos de creación en simulaciones de este escenario. Se descubrió que si los servicios son lo suficientemente grandes y el clúster se usaba con gran frecuencia, la creación de esos servicios de gran tamaño sería lenta. Para controlar este escenario, introdujimos la desfragmentación como una estrategia de equilibrio. Se descubrió que para las cargas de trabajo de gran tamaño, especialmente esas en las que el tiempo de creación era importante, la desfragmentación realmente ayudaba a que esas cargas de trabajo nuevas se programaran en el clúster.

Puede configurar las métricas de desfragmentación para que Cluster Resource Manager intente de forma proactiva condensar la carga de los servicios en una menor cantidad de nodos. Esto permite garantizar que (casi) siempre haya espacio para servicios incluso de mayor tamaño. De este modo, esos servicios se pueden crear rápidamente cuando sea necesario.

La mayoría de los usuarios no requiere desfragmentación. Habitualmente, los servicios son pequeños y, por lo tanto, no es difícil que haya espacio para ellos en el clúster. Sin embargo, si tiene servicios de gran tamaño y necesita crearlos rápidamente (y está dispuesto a aceptar los otros inconvenientes), la estrategia de desfragmentación es su opción.

¿Cuáles son los inconvenientes? Principalmente, la desfragmentación puede aumentar el impacto de los errores (debido a que hay más servicios en ejecución en el nodo que generan errores). Además, la desfragmentación garantiza que algunos recursos del clúster no se usan mientras esperan que se programen las cargas de trabajo.

En el diagrama siguiente puede ver una representación visual de dos clústeres diferentes, uno desfragmentado y el otro no. En el caso del clúster equilibrado, considere el número de movimientos que se necesitarían para colocar uno de los objetos de servicio más grandes. Compárelo con el clúster desfragmentado, donde la carga de trabajo de gran tamaño se podría colocar de inmediato en el nodo cuatro o el cinco.

<center>
![Comparación de clústeres equilibrados y desfragmentados][Image1]
</center>

## <a name="defragmentation-pros-and-cons"></a>Ventajas y desventajas de la desfragmentación
¿Cuáles son esas otras soluciones intermedias conceptuales? Le recomendamos que mida de forma exhaustiva sus cargas de trabajo antes de activar las métricas de desfragmentación. A continuación tiene una tabla de referencia rápida con los aspectos que debe tener en cuenta:

| Ventajas de la desfragmentación | Inconvenientes de la desfragmentación |
| --- | --- |
| Permite la creación rápida de servicios de gran tamaño. |Concentra la carga en menos nodos, de modo que aumenta la contención. |
| Permite reducir el movimiento de datos durante la creación. |Los errores pueden afectar a más servicios y provocar más renovaciones. |
| Permite la descripción enriquecida de requisitos y la reclamación de espacio. |La configuración general de administración de recursos es más compleja. |

Puede combinar las métricas desfragmentadas y normales en el mismo clúster. Cluster Resource Manager intenta consolidar las métricas de desfragmentación tanto como sea posible mientras distribuye las otras. Si no hay servicios que compartan esas métricas, los resultados pueden ser convenientes. Los resultados exactos dependerán del número de métricas equilibradas en comparación con el número de métricas de desfragmentación, cuándo se superponen, su peso, las cargas actuales y otros factores. Se requiere realizar una experimentación para determinar la configuración exacta necesaria.

## <a name="configuring-defragmentation-metrics"></a>Configuración de métricas de desfragmentación
Configurar las métricas de desfragmentación es una decisión global en el clúster, y pueden seleccionarse métricas individuales para la desfragmentación:

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Disk",
          "value": "true"
      },
      {
          "name": "CPU",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Pasos siguientes
* Cluster Resource Manager tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo sobre cómo [describir un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png



<!--HONumber=Jan17_HO1-->


