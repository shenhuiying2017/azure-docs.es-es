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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 0b8b2bad967532bb0040407dc6a3a7b9599576d2
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Desfragmentación de métricas y carga en Service Fabric
La estrategia predeterminada de Cluster Resource Manager de Service Fabric para administrar métricas de carga en el clúster es distribuir la carga. Al tener la seguridad de que los nodos se utilizan por igual, se evitan zonas activas e inactivas que conducen a la contención y el desperdicio de recursos. La distribución de las cargas de trabajo en el clúster es también lo más seguro si se quiere sobrevivir a los errores dado que de esta forma se garantiza que un error no se llevará un gran porcentaje de una carga de trabajo dada. 

Cluster Resource Manager de Service Fabric admite una estrategia diferente para administrar la carga, que es la desfragmentación. La desfragmentación generalmente significa que, en lugar de intentar distribuir el uso de una métrica en el clúster, lo que se hace es consolidarla. La consolidación es simplemente una inversión de la estrategia de equilibrio predeterminada: en lugar de reducir la desviación estándar media de la carga métrica, Cluster Resource Manager intenta aumentarla.

## <a name="when-to-use-defragmentation"></a>Cuándo se debe usar la desfragmentación
La distribución de la carga en el clúster consume algunos de los recursos de cada nodo. Algunas cargas de trabajo crean servicios que son excepcionalmente grandes y consumen la mayoría del nodo o el nodo entero. En estos casos, puede que cuando se crean cargas de trabajo grandes, no haya suficiente espacio en ningún nodo para ejecutarlas. Las cargas de trabajo grandes no son un problema en Service Fabric; en estos casos, Cluster Resource Manager determina que hace falta reorganizar el clúster para hacerles sitio. Sin embargo, mientras tanto, esa carga de trabajo debe esperar que se la programe en el clúster.

Si hay muchos servicios y estados para desplazar, colocar la carga de trabajo de gran tamaño en el clúster podría demorar más. La probabilidad de que esto ocurra aumenta si otras cargas de trabajo del clúster son también grandes y, por tanto, tardan más en reorganizarse. El equipo de Service Fabric midió los tiempos de creación en simulaciones de este escenario. Se encontró que la creación de servicios grandes tardaba mucho más tiempo en cuanto la utilización del clúster se situaba entre el 30 % y el 50 %. Para controlar este escenario, introdujimos la desfragmentación como una estrategia de equilibrio. Se descubrió que para las cargas de trabajo de gran tamaño, especialmente esas en las que el tiempo de creación era importante, la desfragmentación realmente ayudaba a que esas cargas de trabajo nuevas se programaran en el clúster.

Puede configurar las métricas de desfragmentación para que Cluster Resource Manager intente de forma proactiva condensar la carga de los servicios en una menor cantidad de nodos. De esta forma se garantiza que casi siempre haya espacio para servicios grandes incluso sin reorganizar el clúster. No tener que reorganizar el clúster permite que la creación de cargas de trabajo grandes sea más rápida.

La mayoría de los usuarios no requiere desfragmentación. Como los servicios son normalmente pequeños, no es difícil encontrarles sitio en el clúster. Cuando la reorganización es posible, funciona rápidamente, de nuevo porque la mayoría de los servicios son pequeños y se pueden mover fácilmente y en paralelo. Sin embargo, si tiene servicios grandes y se deben crear rápidamente, la estrategia de desfragmentación es la opción adecuada. A continuación se analizan los inconvenientes de usar la desfragmentación. 

## <a name="defragmentation-tradeoffs"></a>Inconvenientes de la desfragmentación
La desfragmentación puede aumentar los errores de forma impactante, dado que hay más servicios en ejecución en los nodos que dan error. La desfragmentación también puede aumentar los costos, dado que los recursos del clúster se deben mantener en reserva, a la espera de la creación de cargas de trabajo grandes.

En el diagrama siguiente puede ver una representación visual de dos clústeres, uno desfragmentado y el otro no. 

<center>
![Comparación de clústeres equilibrados y desfragmentados][Image1]
</center>

En el caso del clúster equilibrado, considere el número de movimientos que se necesitarían para colocar uno de los objetos de servicio más grandes. En el clúster desfragmentado, la carga de trabajo grande podría colocarse en los nodos cuatro o cinco sin tener que esperar a que se muevan otros servicios.

## <a name="defragmentation-pros-and-cons"></a>Ventajas y desventajas de la desfragmentación
¿Cuáles son esas otras soluciones intermedias conceptuales? A continuación tiene una tabla de referencia rápida con los aspectos que debe tener en cuenta:

| Ventajas de la desfragmentación | Inconvenientes de la desfragmentación |
| --- | --- |
| Permite la creación rápida de servicios de gran tamaño. |Concentra la carga en menos nodos, de modo que aumenta la contención. |
| Permite reducir el movimiento de datos durante la creación. |Los errores pueden afectar a más servicios y provocar más renovaciones. |
| Permite la descripción enriquecida de requisitos y la reclamación de espacio. |La configuración general de administración de recursos es más compleja. |

Puede combinar las métricas desfragmentadas y normales en el mismo clúster. Cluster Resource Manager intenta consolidar las métricas de desfragmentación tanto como sea posible mientras distribuye las otras. El resultado de mezclar estrategias de desfragmentación y equilibrio depende de varios factores, por ejemplo:
  - El número de métricas de equilibrio frente al número de métricas de desfragmentación
  - Si algún servicio usa ambos tipos de métricas 
  - Los pesos de métrica
  - Las cargas de métrica actuales
  
Se requiere realizar una experimentación para determinar la configuración exacta necesaria. Se recomienda medir de forma exhaustiva sus cargas de trabajo antes de permitir métricas de desfragmentación en producción. Esto es especialmente cierto cuando se combinan métricas de desfragmentación y equilibradas en el mismo servicio. 

## <a name="configuring-defragmentation-metrics"></a>Configuración de métricas de desfragmentación
Configurar métricas de desfragmentación es una decisión global en el clúster, y se pueden seleccionar métricas individuales para la desfragmentación. Los siguientes fragmentos de código de configuración muestran cómo configurar métricas para la desfragmentación. En este caso, "Metric1" se configura como una métrica de desfragmentación, mientras que "Metric2" se seguirá equilibrando normalmente. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Pasos siguientes
- Cluster Resource Manager tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo sobre cómo [describir un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png

