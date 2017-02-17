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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>Equilibrio del clúster de Service Fabric
Cluster Resource Manager de Service Fabric permite cambios de carga dinámicos, reaccionar a las incorporaciones o retiradas de nodos o servicios, corregir las infracciones de restricciones y volver a equilibrar el clúster. Pero, ¿con qué frecuencia se hace esto y qué lo desencadena?

El primer conjunto de controles en torno al equilibrio es un conjunto de temporizadores. Estos temporizadores controlan la frecuencia con la que Cluster Resource Manager examina el estado del clúster para ver qué cosas hay que solucionar. Hay tres categorías de trabajo, cada uno con su propio temporizador correspondiente. Son las siguientes:

1. Ubicación: esta fase tiene que ver con la colocación de las réplicas con estado o las instancias sin estado que faltan. Abarca los nuevos servicios y la administración de réplicas con estado o instancias sin estado que han dado error. Aquí también se administra la eliminación de réplicas o instancias.
2. Comprobaciones de restricciones: en esta etapa se comprueban y corrigen las infracciones (reglas) de las distintas restricciones de selección de ubicación dentro del sistema. Algunos ejemplos de reglas son asegurarse de que los nodos no sobrepasan la capacidad y que las restricciones de selección de ubicación de un servicio se cumplen.
3. Equilibrado: en esta fase se comprueba si es necesario un equilibrado proactivo según el nivel de equilibrio deseado configurado para las distintas métricas. En caso afirmativo, se intenta encontrar una disposición en el clúster que sea más equilibrada.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuración de pasos y temporizadores del Administrador de recursos de clúster
Cada uno de estos tipos diferentes de correcciones que Cluster Resource Manager puede realizar se controla mediante un temporizador diferente que determina su frecuencia. Cuando se desencadena cada temporizador, se programa la tarea. De forma predeterminada, Resource Manager:

* Examina su estado y aplica las actualizaciones (por ejemplo, la grabación de que un nodo está inactivo) cada 1/10 de segundo.
* Establece los indicadores de comprobación de la ubicación y la restricción cada segundo.
* Establece el indicador de equilibrio cada cinco segundos.

Podemos ver esto reflejado en la siguiente información de configuración:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

mediante ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

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
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Ahora, Cluster Resource Manager solo realiza estas acciones de forma secuencial (por eso hacemos referencias a estas configuraciones como “intervalos mínimos”). Por ejemplo, Cluster Resource Manager se encarga de las solicitudes pendientes para crear servicios antes de equilibrar el clúster. Como puede ver por los intervalos de tiempo predeterminados especificados, Cluster Resource Manager examina y comprueba con frecuencia si hay algo que deba hacer, por lo que el conjunto de cambios realizados al final de cada paso suele ser pequeño. Realizar pequeños cambios con frecuencia hace que Cluster Resource Manager responda a los eventos que se producen en el clúster. Los temporizadores predeterminados proporcionan algún procesamiento por lotes porque muchos eventos del mismo tipo tienden a producirse al mismo tiempo. De forma predeterminada, Cluster Resource Manager no realiza exámenes durante las horas de cambios en el clúster e intenta abordar todos los cambios al mismo tiempo. Hacerlo produciría ráfagas de fragmentos.

Cluster Resource Manager también necesita información adicional para determinar si el clúster está desequilibrado. Para ello contamos con dos elementos de configuración: *umbrales de equilibrio* y *umbrales de actividad*.

## <a name="balancing-thresholds"></a>Umbrales de equilibrio
Un umbral de equilibrio es el control principal para la activación del reequilibrio proactivo. El temporizador MinLoadBalancingInterval solo determina la frecuencia con la que Cluster Resource Manager debe realizar la comprobación, no significa que se realizará una acción. El umbral de equilibrio define cómo de desequilibrado debe estar el clúster con respecto a una métrica determinada para que Cluster Resource Manager considere que está desequilibrado y desencadene el equilibrio.

Los umbrales de equilibrio se definen por métrica como parte de la definición de clúster. Para más información sobre las métricas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

mediante ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

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

El umbral de equilibrio de una métrica es una proporción. Si la cantidad de carga en el nodo más cargado dividida entre la cantidad de carga en el nodo menos cargado supera esta cifra, se considera que el clúster está desequilibrado. Como resultado, se activa el equilibrado durante la próxima comprobación de Cluster Resource Manager.

<center>
![Ejemplo de umbral de equilibrio][Image1]
</center>

En este ejemplo, cada servicio solo consume una unidad de alguna métrica. En el ejemplo superior, la carga máxima en un nodo es cinco y la mínima es dos. Supongamos que el umbral de equilibrio de esta métrica es tres. Puesto que la proporción del clúster es 5/2 = 2,5, que es menor que el umbral de equilibrio especificado de tres, el clúster está equilibrado. El equilibrado no se activa durante la comprobación de Cluster Resource Manager.

En el ejemplo de la parte inferior, la carga máxima en un nodo es diez, mientras que el valor mínimo es dos, lo que da lugar a una proporción de cinco. Cinco es mayor que el umbral de equilibrio establecido en tres para esa métrica. Como resultado, la próxima vez que el temporizador de equilibrio se active, se programará un reequilibrado. En una situación como esta, parte de la carga se distribuirá al Nodo3 casi con toda seguridad. Como Cluster Resource Manager de Service Fabric no es avaro, también podría distribuir cierta carga al Nodo2. Con ello se minimizan las diferencias generales entre los nodos, que es uno de los objetivos de Cluster Resource Manager.

<center>
![Acciones de ejemplo de umbral de equilibrio][Image2]
</center>

El objetivo explícito no es estar por debajo del umbral de equilibrio. Los umbrales de equilibrio son solo *desencadenadores* que indican a Cluster Resource Manager que examine el clúster para determinar qué mejoras puede realizar, si es que las hay. El hecho de que se inicie una búsqueda de equilibrio no conlleva ninguna acción; en ocasiones, el clúster no está equilibrado pero la situación no se puede mejorar.

## <a name="activity-thresholds"></a>umbrales de actividad
En ocasiones, aunque los nodos están relativamente desequilibrados, la cantidad de carga *total* en el clúster es baja. La falta de carga puede deberse a una caída transitoria, o porque el clúster es nuevo y se acaba de arrancar. En cualquier caso, no querrá perder tiempo en equilibrar el clúster porque no hay mucho que ganar. Si el clúster se somete a un equilibrado, habría invertido recursos de red y proceso para realizar cambios que no suponen *ninguna* diferencia. Para evitar esto, hay otro control conocido como umbrales de actividad. Los umbrales de actividad permiten especificar un límite inferior absoluto para la actividad. Si ningún nodo se encuentra por encima de umbral, el equilibrado no se desencadena aunque se alcance el umbral de equilibrio.

Como ejemplo, observemos el diagrama siguiente. Supongamos también que conservamos nuestro umbral de equilibrio de tres para esta métrica, pero ahora también tenemos un umbral de actividad de 1536. En el primer caso, aunque el clúster está desequilibrado según el umbral de equilibrio, ningún nodo llega al umbral mínimo de actividad, por lo que no sucede nada. En el ejemplo de abajo, el Nodo1 supera con creces el umbral de actividad. Puesto que se superan el umbral de equilibrio y el umbral de actividad para la métrica, se programa el equilibrado.

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

## <a name="balancing-services-together"></a>Equilibrio conjunto de los servicios
Resulta interesante tener en cuenta que el hecho de que el clúster esté o no equilibrado es una decisión de todo el clúster. Sin embargo, para corregirlo movemos instancias y réplicas de servicio individuales. Tiene sentido, ¿no? Si la memoria está apilada en un nodo, podría haber varias réplicas o instancias contribuyendo a ella. Para corregir el desequilibrio podría ser necesario mover cualquiera de las réplicas con estado o instancias sin estado que utiliza la métrica desequilibrada.

Sin embargo, en ocasiones se mueve un servicio que no está desequilibrado. ¿Cómo podría pasar esto si todas las métricas del servicio estaban equilibradas, además perfectamente, en el momento del otro desequilibrio? ¡Veamos!

Tomemos, por ejemplo, cuatro servicios, Servicio1, Servicio2, Servicio3 y Servicio4. Servicio1 informa de la Métrica1 y la Métrica2, Servicio2 de la Métrica2 y la Métrica3, Servicio3 de la Métrica3 y la Métrica4 y Servicio4 de algunos valores de la Métrica99. Seguramente puede ver adónde queremos llegar. ¡Tenemos una cadena! En realidad no tenemos cuatro servicios independientes, sino un grupo de servicios que están relacionados (Servicio1, Servicio2 y Servicio3) y uno que va por su cuenta.

<center>
![Equilibrio conjunto de los servicios][Image4]
</center>

Por lo tanto, es posible que un desequilibrio en la Métrica1 haga que se muevan réplicas o instancias que pertenecen al Servicio3 (que no informan a la Métrica1). Por lo general, estos movimientos son bastante limitados, pero pueden ser mayores en función del desequilibrio de la Métrica1 y de qué cambios hayan sido necesarios en el clúster para corregirlo. También podemos decir con certeza que un desequilibrio en las métricas 1, 2 o 3 no puede causar movimientos en Servicio4. No tendría sentido porque mover las réplicas o instancias que pertenecen al Servicio4 no afectará al equilibrio de las métricas 1, 2 o 3.

Cluster Resource Manager calcula automáticamente qué servicios están relacionados, puesto que se pueden haber agregado o eliminado servicios o haberse cambiado la configuración de las métricas. Por ejemplo, entre las dos ejecuciones de equilibrado, se podría haber reconfigurado el Servicio2 para quitar la Métrica2. Este cambio rompe la cadena entre el Servicio1 y el Servicio2. Ahora, en lugar de dos grupos de servicios hay tres:

<center>
![Equilibrio conjunto de los servicios][Image5]
</center>

## <a name="next-steps"></a>Pasos siguientes
* Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para obtener más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)
* El costo del movimiento es una forma de señalizar al Administrador de recursos de clúster que determinados servicios son más caros de mover que otros. Para más información sobre el coste del movimiento, consulte [este artículo](service-fabric-cluster-resource-manager-movement-cost.md)
* El Administrador de recursos de clúster presenta varias limitaciones que se pueden configurar para ralentizar la renovación del clúster. Aunque no son normalmente necesarias, si las necesita, puede encontrar información sobre ellas [aquí](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


