---
title: "Equilibrio de un clúster de Azure Service Fabric | Microsoft Docs"
description: "Una introducción al equilibrio del clúster con el Administrador de recursos de clúster de Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: 06d65878d84fb845cf0c4c333a1e2d12b0aaec2f
ms.contentlocale: es-es
ms.lasthandoff: 09/06/2017

---
# <a name="balancing-your-service-fabric-cluster"></a>Equilibrio del clúster de Service Fabric
Cluster Resource Manager de Service Fabric admite cambios dinámicos de carga, en respuesta a las incorporaciones y las eliminaciones de nodos o servicios. También corrige automáticamente las infracciones de restricciones y reequilibra el clúster de manera proactiva. ¿Pero con qué frecuencia se realizan estas acciones y qué las activa?

Existen tres categorías de trabajo que Cluster Resource Manager realiza. Son las siguientes:

1. Ubicación: esta fase tiene que ver con la colocación de las réplicas con estado o las instancias sin estado que faltan. Abarca los nuevos servicios y la administración de réplicas con estado o instancias sin estado incorrectas. Aquí también se administra la eliminación de réplicas o instancias.
2. Comprobaciones de restricciones: en esta etapa se comprueban y corrigen las infracciones (reglas) de las distintas restricciones de selección de ubicación dentro del sistema. Algunos ejemplos de reglas son asegurarse de que los nodos no sobrepasan la capacidad y que las restricciones de selección de ubicación de un servicio se cumplen.
3. Equilibrio: en esta fase se comprueba si se precisa reequilibrado en función del nivel deseado de equilibrio configurado para las distintas métricas. En caso afirmativo, se intenta encontrar una disposición en el clúster que sea más equilibrada.

## <a name="configuring-cluster-resource-manager-timers"></a>Configuración de temporizadores de Cluster Resource Manager
El primer conjunto de controles en torno al equilibrio es un conjunto de temporizadores. Estos temporizadores controlan la frecuencia con la que Cluster Resource Manager examina el clúster y realiza acciones correctivas.

Cada uno de estos tipos diferentes de correcciones que Cluster Resource Manager puede realizar se controla mediante un temporizador diferente que determina su frecuencia. Cuando se desencadena cada temporizador, se programa la tarea. De forma predeterminada, Resource Manager:

* Examina su estado y aplica las actualizaciones (por ejemplo, la grabación de que un nodo está inactivo) cada 1/10 de segundo.
* establece la marca de comprobación de selección de ubicación. 
* establece la marca de comprobación de restricción cada segundo.
* Establece el indicador de equilibrio cada cinco segundos.

Seguidamente se ofrecen ejemplos de la configuración que regulan estos temporizadores:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Ahora, Cluster Resource Manager solo realiza estas acciones una a una, de forma secuencial. Por esto nos referimos a estos temporizadores como “intervalos mínimos” y a las acciones que se realizan cuando los temporizadores se desactivan "marcas de establecimiento". Por ejemplo, Cluster Resource Manager se encarga de las solicitudes pendientes para crear servicios antes de equilibrar el clúster. Como puede ver por los intervalos de tiempo predeterminados especificados, Cluster Resource Manager examina y comprueba con frecuencia si hay algo que deba hacer. Normalmente, esto significa que el conjunto de cambios realizados durante cada paso es pequeño. Realizar pequeños cambios con frecuencia permite que Cluster Resource Manager responda a los eventos que se producen en el clúster. Los temporizadores predeterminados proporcionan algún procesamiento por lotes porque muchos eventos del mismo tipo tienden a producirse al mismo tiempo. 

Por ejemplo, cuando los nodos no funcionan pueden provocar que todos los dominios dejen de funcionar a la vez. Tos estos errores se capturan durante la siguiente actualización de estado después de *PLBRefreshGap*. Las correcciones se determinan durante las siguientes ejecuciones de selección de ubicación, comprobación de restricciones y equilibrio. De forma predeterminada, Cluster Resource Manager no realiza exámenes durante las horas de cambios en el clúster e intenta abordar todos los cambios al mismo tiempo. Hacerlo produciría ráfagas de fragmentos.

Cluster Resource Manager también necesita información adicional para determinar si el clúster está desequilibrado. Para ello se cuenta con dos elementos de configuración: *BalancingThresholds* y *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Umbrales de equilibrio
Un umbral de equilibrio es el control principal para la activación del reequilibrado. El umbral de equilibrio de una métrica es una _proporción_. Si la carga de una métrica en el nodo más cargado dividida entre la cantidad de carga en el nodo menos cargado supera el valor de *BalancingThreshold* de la métrica, el clúster está desequilibrado. Como resultado, se activa el equilibrado durante la próxima comprobación de Cluster Resource Manager. El temporizador *MinLoadBalancingInterval* determina la frecuencia con la que Cluster Resource Manager debe comprobar si es necesario realizar el reequilibrado. La comprobación no significa que suceda nada. 

Los umbrales de equilibrio se definen por métrica como parte de la definición de clúster. Para más información sobre las métricas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>
![Ejemplo de umbral de equilibrio][Image1]
</center>

En este ejemplo, cada servicio solo consume una unidad de alguna métrica. En el ejemplo superior, la carga máxima en un nodo es cinco y la mínima es dos. Supongamos que el umbral de equilibrio de esta métrica es tres. Puesto que la proporción del clúster es 5/2 = 2,5, que es menor que el umbral de equilibrio especificado de tres, el clúster está equilibrado. El equilibrado no se activa durante la comprobación de Cluster Resource Manager.

En el ejemplo de abajo, la carga máxima en un nodo es 10, mientras que la mínima es dos, lo que da lugar a una proporción de cinco. Cinco es mayor que el umbral de equilibrio establecido en tres para esa métrica. Como resultado, la próxima vez que el temporizador de equilibrio se active, se programará un reequilibrado. En una situación como esta, parte de la carga suele distribuirse a Node3. Como el enfoque de Cluster Resource Manager de Service Fabric no es ambicioso, también podría distribuir cierta carga a Node2. 

<center>
![Acciones de ejemplo de umbral de equilibrio][Image2]
</center>

> [!NOTE]
> "Equilibrio" controla dos estrategias distintas para administrar la carga del clúster. La estrategia predeterminada que usa Cluster Resource Manager consiste en distribuir la carga entre los nodos del clúster. La otra estrategia es la [desfragmentación](service-fabric-cluster-resource-manager-defragmentation-metrics.md). La desfragmentación se realiza durante la misma ejecución del equilibrio. Las estrategias de equilibrio y desfragmentación se pueden usar con distintas métricas en un mismo clúster. Un servicio puede tener métricas de equilibrio y desfragmentación. En cuanto a las métricas de desfragmentación, la proporción de las cargas del clúster activa el reequilibrado cuando es _inferior_ al umbral de equilibrio. 
>

El objetivo explícito no es estar por debajo del umbral de equilibrio. Los umbrales de equilibrio son tan solo un *desencadenador*. Cuando se ejecuta el equilibrio, Cluster Resource Manager determina qué mejoras puede realizar, si hay alguna. El inicio de una búsqueda de equilibrio no significa que nada cambie. A veces, el clúster está desequilibrado pero tiene demasiadas restricciones para que se corrija. Otra posibilidad es que las mejoras requieran movimientos que resulten demasiado [costosos](service-fabric-cluster-resource-manager-movement-cost.md).

## <a name="activity-thresholds"></a>umbrales de actividad
En ocasiones, aunque los nodos están relativamente desequilibrados, la cantidad de carga *total* en el clúster es baja. La falta de carga puede deberse a una caída transitoria, o porque el clúster es nuevo y se acaba de arrancar. En cualquier caso, no querrá perder tiempo en equilibrar el clúster porque no hay mucho que ganar. Si el clúster se somete a un equilibrado, se invertirían recursos de red y de proceso para realizar cambios que no suponen *ninguna* diferencia. Para evitar movimientos innecesarios, hay otro control conocido como umbrales de actividad. Los umbrales de actividad permiten especificar un límite inferior absoluto para la actividad. Si ningún nodo se encuentra por encima de umbral, el equilibrado no se desencadena aunque se alcance el umbral de equilibrio.

Supongamos que se mantiene el umbral de equilibrio de tres para esta métrica. Supongamos también que se tiene un umbral de actividad de 1536. En el primer caso, aunque el clúster está desequilibrado según el umbral de equilibrio, ningún nodo llega al umbral mínimo de actividad, por lo que no sucede nada. En el ejemplo de abajo, Node1 supera el umbral de actividad. Puesto que se superan el umbral de equilibrio y el umbral de actividad para la métrica, se programa el equilibrado. Como ejemplo, observemos el diagrama siguiente. 

<center>
![Ejemplo de umbral de actividad][Image3]
</center>

Al igual que los umbrales de equilibrio, los umbrales de actividad se definen por métrica a través de la definición de clúster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

mediante ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Observe que los umbrales de equilibrio y actividad están vinculados a la métrica; el equilibrado solo se desencadenará si se superan los umbrales de equilibrio y actividad de la misma métrica.

> [!NOTE]
> Cuando no se especifica, el umbral de equilibrio de una métrica es 1 y el de actividad, 0. Esto significa que el Administrador de recursos de clúster tratará de mantener esa métrica perfectamente equilibrada para cualquier carga determinada. Si usas métricas personalizadas, se recomienda definir umbrales de equilibrio y actividad explícitos para las métricas. 
>

## <a name="balancing-services-together"></a>Equilibrio conjunto de los servicios
La decisión de si el clúster está desequilibrado o no afecta a todo el clúster. Sin embargo, para corregirlo movemos instancias y réplicas de servicio individuales. Tiene sentido, ¿no? Si la memoria está apilada en un nodo, podría haber varias réplicas o instancias contribuyendo a ella. Para corregir el desequilibrio podría ser necesario mover cualquiera de las réplicas con estado o instancias sin estado que utiliza la métrica desequilibrada.

Aunque, a veces, se mueve un servicio que no estaba desequilibrado (recuerde la explicación anterior sobre ponderaciones locales y globales). ¿Por qué habría de moverse un servicio si todas sus métricas están equilibradas? Veamos un ejemplo:

- Supongamos que hay cuatro servicios, Service1, Service2, Service3 y Service4. 
- Service1 informa de las métricas Metric1 y Metric2. 
- Service2 informa de las métricas Metric2 y Metric3. 
- Service3 informa de las métricas Metric3 y Metric4.
- Service4 informa de la Metric99. 

Seguramente puede ver adónde queremos llegar: hay una cadena. En realidad no tenemos cuatro servicios independientes, sino tres servicios que están relacionados y uno que va por su cuenta.

<center>
![Equilibrio conjunto de los servicios][Image4]
</center>

Debido a esta cadena, es posible que un desequilibrio en las métrica 1-4 provoque el movimiento de las réplicas o instancias que pertenecen a los servicios 1-3. También sabemos que un desequilibrio en las métricas 1, 2 o 3 no puede ocasionar movimientos en Service4. No tendría sentido porque mover las réplicas o instancias que pertenecen a Service4 no afectará al equilibrio de las métricas 1-3.

Cluster Resource Manager averigua automáticamente qué servicios están relacionados. La incorporación, eliminación o modificación de las métricas de los servicios puede afectar a sus relaciones. Por ejemplo, entre dos ejecuciones de equilibrado, puede que Service2 se haya actualizado y quitado Metric2. Esto rompe la cadena entre el Servicio1 y el Servicio2. Ahora, en lugar de dos grupos de servicios relacionados, hay tres:

<center>
![Equilibrio conjunto de los servicios][Image5]
</center>

## <a name="next-steps"></a>Pasos siguientes
* Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre las métricas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
* El costo del movimiento es una forma de señalizar al Administrador de recursos de clúster que determinados servicios son más caros de mover que otros. Para más información sobre el coste del movimiento, consulte [este artículo](service-fabric-cluster-resource-manager-movement-cost.md)
* El Administrador de recursos de clúster presenta varias limitaciones que se pueden configurar para ralentizar la renovación del clúster. Aunque no son normalmente necesarias, si las necesita, puede encontrar información sobre ellas [aquí](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

