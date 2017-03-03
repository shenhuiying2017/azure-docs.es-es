---
title: "Cluster Resource Manager de Service Fabric: integración de la administración | Microsoft Docs"
description: "Una visión general de los puntos de integración entre el Administrador de recursos de clúster y la administración de Service Fabric."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: b2428f93680c12c76000fa8eb1a7138f72a8efe7
ms.openlocfilehash: 9d67f089f4aba03e846a8fe020a91b6b1ac6ea48
ms.lasthandoff: 02/11/2017


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integración del Administrador de recursos de clúster con la administración de clústeres de Service Fabric
Cluster Resource Manager de Service Fabric no es el componente principal de Service Fabric que controla las operaciones de administración (como las actualizaciones de aplicaciones) pero sí contribuye. Cluster Resource Manager contribuye a la administración, en primer lugar, realizando un seguimiento del estado deseado del clúster y los servicios que contiene. Además, envía informes de mantenimiento cuando no puede colocar el clúster en la configuración deseada. Por ejemplo, porque no hay capacidad suficiente o porque hay reglas en conflicto en cuanto a dónde colocar un servicio. Otro aspecto de la integración tiene que ver con el funcionamiento de las actualizaciones. Durante ellas, Cluster Resource Manager modifica ligeramente su comportamiento. Estos puntos de integración se tratan a continuación.

## <a name="health-integration"></a>Integración del mantenimiento
Cluster Resource Manager realiza un seguimiento constante de las reglas que ha definido para los servicios y las capacidades disponibles en los nodos y el clúster. Si no puede cumplir esas reglas, o si no hay capacidad suficiente, se emiten errores y advertencias de mantenimiento. Por ejemplo, si un nodo está por encima de su capacidad y Cluster Resource Manager trata de corregir la situación moviendo servicios. Si no puede, emite una advertencia de mantenimiento que indica qué nodo está por encima de su capacidad y en qué métricas.

Otro ejemplo de advertencias de mantenimiento de Resource Manager son las infracciones de restricciones de ubicación. Por ejemplo, si ha definido una restricción de selección de ubicación (como `“NodeColor == Blue”`) y Resource Manager detecta una infracción de esa restricción, emitirá una advertencia de mantenimiento. Esto pasa con las restricciones personalizadas y las predeterminadas (como las restricciones de dominios de actualización y de error).

A continuación se muestra un ejemplo de un informe de mantenimiento de este tipo En este caso, el informe de mantenimiento es para una de las particiones del servicio de sistema. El mensaje de mantenimiento indica que las réplicas de esa partición se empaquetan temporalmente en demasiado pocos dominios de actualización.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Esto es lo que nos dice este mensaje de mantenimiento:

1. Todas las réplicas en sí mismas son correctas (esta es la primera prioridad de Service Fabric).
2. La restricción de distribución de dominios de actualización se está infringiendo actualmente (lo que significa que un determinado dominio de actualización tiene más réplicas para esta partición de las que debería).
3. El nodo que contiene la réplica que provoca la infracción (el nodo con el id.: 3d1a4a68b2592f55125328cd0f8ed477)
4. Cuándo se produjo el informe (el 08/10/2015 a las 19:13:02).

La información de este tipo genera alertas que se activan durante la producción para advertirle de que hay un problema. En este caso, queremos ver si podemos averiguar por qué Resource Manager tuvo que empaquetar las réplicas en el dominio de actualización. Esto podría deberse a que los nodos de los otros dominios de actualización estuvieran inactivos, por ejemplo.

Supongamos que quiere crear un servicio o que Resource Manager está tratando de encontrar un lugar donde colocar algunos servicios, pero no hay ninguna solución que funcione. Esto podría ser por muchos motivos, pero habitualmente se debe a una de las siguientes condiciones:

1. Alguna condición transitoria ha hecho que sea imposible colocar esta instancia o réplica de servicio correctamente.
2. Los requisitos del servicio están mal configurados de forma que son imposibles de satisfacer.

En cada una de estas condiciones, verá un informe de mantenimiento de Cluster Resource Manager con información para ayudarlo a determinar qué es lo que pasa y por qué no se puede colocar el servicio. A este proceso lo llamamos "secuencia de eliminación de restricciones". Durante dicho proceso, el sistema recorre las restricciones configuradas que afectan al servicio y registra lo que eliminan. De esta forma, cuando los servicios no se pueden colocar, puede ver qué nodos se eliminaron y por qué.

## <a name="constraint-types"></a>Tipos de restricción:
Vamos a hablar sobre cada una de las diferentes restricciones que puede ver en estos informes de mantenimiento. La mayoría de las veces, estas restricciones no eliminan nodos, ya que están en el nivel suave o de optimización de manera predeterminada. Sin embargo, podría ver mensajes de mantenimiento relacionados con estas restricciones si están configuradas como restricciones fuertes o en los casos excepcionales que hacen que los nodos se puedan eliminar.

* **ReplicaExclusionStatic** y **ReplicaExclusionDynamic**: estas restricciones indican que dos réplicas con estado o instancias sin estado de la misma partición se tendrían que colocar en el mismo nodo (lo cual no se permite). ReplicaExclusionStatic y ReplicaExclusionDynamic son casi la misma regla. La restricción ReplicaExclusionDynamic indica que no se ha podido colocar esa réplica en la ubicación porque la única solución propuesta ya había colocado ahí una réplica. Se diferencia de la restricción ReplicaExclusionStatic en que no indica un conflicto propuesto, sino uno real. En este caso, ya hay una réplica en el nodo. ¿Resulta confuso? Sí. ¿Importa mucho? No. Si está viendo una secuencia de eliminación de restricciones que contiene las restricciones ReplicaExclusionStatic o ReplicaExclusionDynamic, Cluster Resource Manager considera que no hay suficientes nodos. Las restricciones siguientes normalmente pueden indicar por qué estamos acabando con tan pocos nodos.
* **PlacementConstraint**: si ve este mensaje, significa que se eliminaron algunos nodos porque no coincidían con las restricciones de selección de ubicación del servicio. Realizamos el seguimiento de las restricciones de selección de ubicación configuradas actualmente como parte de este mensaje. Esto es normal si tiene definidas restricciones de selección de ubicación. Sin embargo, si hay algún error en la restricción de selección de ubicación debido al cual se eliminan demasiados nodos, aquí es donde se daría cuenta.
* **NodeCapacity**: esta restricción significa que Cluster Resource Manager no pudo colocar las réplicas en los nodos indicados porque, de hacerlo, el nodo habría sobrepasado su capacidad.
* **Affinity**: esta restricción indica que no pudimos colocar la réplica en los nodos afectados porque se produciría una infracción de la restricción de afinidad. Puede obtener más información sobre la afinidad en [este artículo](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md).
* **FaultDomain** y **UpgradeDomain**: estas restricciones eliminan nodos si el hecho de colocar la réplica en los nodos indicados provocaría el empaquetamiento en un determinado dominio de error o de actualización. En el tema sobre [las restricciones de dominio de error y de actualización y el comportamiento resultante](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: lo normal es que no vea esta restricción que hace que los nodos se eliminen de la solución, dado que está configurado en el nivel de optimización de manera predeterminada. Además, la restricción de ubicación preferida suele estar presente durante las actualizaciones, y se utiliza para mover las réplicas al lugar donde se encontraban al inicio de la actualización.

### <a name="constraint-priorities"></a>Prioridades de restricción
En todas estas restricciones, puede que haya estado pensando: "Creo que esas restricciones de selección de ubicación son el aspecto más importante de mi sistema. Estoy dispuesto a infringir otras restricciones, incluso cosas como afinidad y capacidad, si con ello tengo la garantía de que las restricciones de colocación no se van a infringir jamás".

Bien, pues resulta que podemos hacerlo. Se pueden configurar restricciones con unos cuantos niveles diferentes de aplicación, pero se reducen a "fuerte" (0), "suave" (1), "optimización" (2) y "desactivado" (-1). Hemos definido la mayoría de las restricciones como fuertes de manera predeterminada. Por ejemplo, la mayoría de las personas no piensan normalmente en la capacidad como algo en lo que están dispuestos a bajar la guardia y casi todas son fuertes o suaves.

Las diferentes prioridades de restricción son también el motivo de que se muestren algunas advertencias de infracción de restricciones con más frecuencia que otras: hay ciertas restricciones que estamos dispuestos a dejar pasar (infringir) temporalmente. Estos niveles no significan realmente que se vaya a infringir o no una restricción determinada: solo es un orden para aplicarlas en función de las preferencias. De este modo, Cluster Resource Manager puede tomar la decisión más acertada si es imposible cumplir todas las restricciones.

En situaciones avanzadas se pueden cambiar las prioridades de restricción. Por ejemplo, supongamos que desea asegurarse de que se infringiría la afinidad para solucionar problemas de capacidad de nodo. Para lograr esto, podría establecer la prioridad de la restricción de afinidad como "suave" (1) y dejar la restricción de capacidad establecida en "fuerte" (0).

Los valores de prioridad predeterminados de las distintas restricciones se especifican en el archivo de configuración:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Restricciones de dominio de error y dominio de actualización
Cluster Resource Manager hace realidad el deseo de mantener los servicios repartidos entre dominios de actualización y de error como una restricción dentro del motor de Resource Manager. Para obtener más información sobre cómo se utilizan, consulte el artículo sobre [configuración de clústeres](service-fabric-cluster-resource-manager-cluster-description.md).

En ocasiones, hemos tenido que ponernos estrictos con los dominios de error y de actualización para evitar la aparición de algún problema. También ha habido ocasiones en las que hemos tenido que pasarlos por alto completamente (aunque por poco tiempo). Por lo general, la flexibilidad de la infraestructura de prioridades de restricciones ha funcionado muy bien, pero no suele ser necesaria. La mayoría de las veces todo funciona en las prioridades predeterminadas. Los dominios de actualización mantienen una restricción suave. Cluster Resource Manager puede necesitar empaquetar un par de réplicas en un dominio de actualización para administrar una actualización, errores o infracciones de restricciones. Esto suele suceder cuando hay varios errores o cuando otra renovación en el sistema impide la ubicación correcta. Si el entorno está configurado correctamente, todas las restricciones, incluidas las de error y de actualización, se respetan completamente, incluso durante las actualizaciones. Lo que realmente importa es que Cluster Resource Manager vigila sus restricciones e informa inmediatamente si detecta una infracción.

## <a name="the-preferred-location-constraint"></a>La restricción de ubicación preferida
La restricción PreferredLocation es un poco diferente, de ahí que sea la única restricción establecida en "optimización". Usamos esta restricción mientras las actualizaciones están en marcha para tratar de volver a colocar los servicios donde estaban antes de la actualización. Existen todo tipo de razones por las que esto podría no funcionar en la práctica, pero es una buena optimización.

## <a name="upgrades"></a>Actualizaciones
Cluster Resource Manager también es útil en las actualizaciones de clústeres y aplicaciones, durante las cuales se encarga de estas dos tareas:

* Garantizar que las reglas y el rendimiento del clúster no se vean afectados
* Tratar de ayudar a que la actualización se realice sin dificultades

### <a name="keep-enforcing-the-rules"></a>Aplicación continuada de las reglas
Lo más importante a tener en cuenta en este punto es que las reglas, los controles estrictos en torno a elementos como las restricciones de selección de ubicación, se siguen aplicando durante las actualizaciones. Las restricciones de selección de ubicación garantizan que sus cargas de trabajo se ejecuten solamente donde tengan permiso para ello, incluso durante las actualizaciones. Si su entorno está muy restringido, las actualizaciones pueden tardar mucho. Esto se debe a que puede haber muy pocas opciones a las que puede acudir un servicio si este (o el nodo en el que se encuentra) deja de estar disponible temporalmente para una actualización.

### <a name="smart-replacements"></a>Sustituciones inteligentes
Cuando se inicia una actualización, Resource Manager crea una instantánea de la disposición actual del clúster. A medida que se van completando los dominios de actualización, trata de colocar todo de nuevo con su disposición original. De este modo, hay dos transiciones como mucho durante la actualización (la salida del nodo afectado y el regreso al mismo). El hecho de que el clúster recupere su disposición original también garantiza que la actualización no afecte a la distribución del clúster. Si la distribución del clúster era adecuada antes de la actualización, lo seguirá siendo después, o al menos no empeorará.

### <a name="reduced-churn"></a>Renovación reducida
Otra cosa que sucede durante las actualizaciones es que Cluster Resource Manager desactiva el equilibrio para la entidad que se actualiza. Esto significa que, si tiene dos instancias de aplicación diferentes e inicia una actualización en una de ellas, el equilibrio se detiene para esa instancia de aplicación, pero no para la otra. La detención del equilibrio evita respuestas innecesarias a la propia actualización, como mover servicios a nodos que se vaciaron para la actualización. Si la actualización en cuestión es una actualización de clúster, se detiene el equilibrio en todo el clúster durante la actualización. Las comprobaciones de restricción se mantienen activas, lo que garantiza la aplicación de las reglas; solo se deshabilita el reequilibrado.

### <a name="relaxed-rules"></a>Reglas relajadas
Por regla general, quiere que la actualización se complete, aunque el clúster esté restringido o completamente lleno. Tener la capacidad de administrar la capacidad del clúster durante las actualizaciones es más importante si cabe que de costumbre. Esto es debido a que suele haber entre un 5 y un 20 por ciento de la capacidad del clúster inactiva mientras se implementa la actualización en el clúster. Ese trabajo normalmente tiene que ir a algún sitio. Aquí es donde entra en juego el concepto de [capacidades almacenadas en búfer](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity). Aunque la capacidad almacenada en búfer se respeta durante el funcionamiento normal (dejando algo de espacio de sobrecarga), Cluster Resource Manager utiliza la capacidad total (ocupando todo el búfer) durante las actualizaciones.

## <a name="next-steps"></a>Pasos siguientes
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)

