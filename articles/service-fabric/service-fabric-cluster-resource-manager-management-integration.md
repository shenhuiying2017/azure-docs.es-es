---
title: 'Administrador de recursos de clúster de Service Fabric: integración de la administración | Microsoft Docs'
description: Una visión general de los puntos de integración entre el Administrador de recursos de clúster y la administración de Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Integración del Administrador de recursos de clúster con la administración de clústeres de Service Fabric
Cluster Resource Manager de Service Fabric no es el componente principal de Service Fabric que controla las operaciones de administración (como las actualizaciones de aplicaciones) pero sí contribuye. Para comenzar, Cluster Resource Manager realiza el seguimiento del estado deseado del clúster y de los servicios que contiene desde una perspectiva de la dotación de recursos y el equilibrio, y envía informes de mantenimiento cuando no puede colocar el clúster en la configuración deseada (por ejemplo, cuando no hay capacidad suficiente o hay conflicto entre las reglas sobre dónde se debe colocar un servicio). Otra parte de la integración tiene que ver con cómo funcionan las actualizaciones: durante las actualizaciones, Cluster Resource Manager altera su comportamiento. Trataremos ambos temas a continuación.

## Integración del mantenimiento
Cluster Resource Manager rastrea continuamente las reglas que se han definido para los servicios, además de las capacidades disponibles en los nodos y en el clúster, y emite advertencias de mantenimiento y errores si no puede satisfacer esas reglas o si no hay capacidad suficiente. Por ejemplo, si un nodo está por encima de su capacidad y Cluster Resource Manager no puede corregir la situación, emitirá una advertencia de mantenimiento indicando cuál es el nodo que ha sobrepasado su capacidad y cuáles son las métricas implicadas.

Otro ejemplo de cuando verá que el Administrador de recursos emite advertencias de mantenimiento es si ha definido una restricción de selección de ubicación (como "NodeColor == Azul") y el Administrador de recursos detecta una infracción de restricción. Esto se hace para las restricciones personalizadas y para las predeterminadas (como las restricciones de dominios de actualización y de error) que se aplican automáticamente.

A continuación se muestra un ejemplo de un informe de mantenimiento de este tipo En este caso, el informe de mantenimiento es para una de las particiones del sistema porque las réplicas de esa partición están empaquetadas temporalmente en demasiados pocos dominios de actualización, como podría pasar si se produjera una cadena de errores:

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
4. Cuándo sucedió todo esto (8/10/2015 7:13:02 P.M.)

Estos son muy buenos datos para disparar una alerta en producción que le permita saber que algo ha sucedido, ya que probablemente querrá echar un vistazo. En este caso, por ejemplo, queremos ver si podemos averiguar por qué Resource Manager no creyó que tuviera más opción que empaquetar las réplicas en el dominio de actualización. Pudo ser porque todos los nodos de los otros dominios de actualización estaban inactivos y no había suficientes dominios de repuesto, o, si había suficientes dominios activos, otra cosa provocó que los nodos de esos otros dominios de actualización se invalidaran (por ejemplo, debido a alguna directiva de colocación en el servicio o a capacidad insuficiente).

Sin embargo, supongamos que quiere crear un servicio o que el Administrador de recursos está intentando encontrar un lugar donde colocar algunos servicios pero no parece haber ninguna solución que funcione. Esto podría ser por muchos motivos, pero habitualmente se debe a una de las siguientes condiciones:

1. Alguna condición transitoria ha hecho que sea imposible colocar esta instancia o réplica de servicio correctamente.
2. Los requisitos del servicio están mal configurados de forma que son imposibles de satisfacer.

En cada una de estas condiciones, verá un informe de mantenimiento de Cluster Resource Manager con información para ayudarle a determinar qué es lo que pasa y por qué no se puede colocar el servicio. A este proceso lo llamamos "secuencia de eliminación de restricciones". Durante dicho proceso, el sistema recorre las restricciones configuradas que afectan al servicio y registra lo que eliminan. De esta forma, cuando los servicios no se pueden colocar, puede ver qué nodos se eliminaron y por qué.

## Tipos de restricción:
Vamos a hablar sobre cada una de las diferentes restricciones que puede ver en estos informes de mantenimiento y qué es lo que comprueban. Tenga en cuenta que casi nunca verá cómo alguna de estas restricciones elimina nodos, dado que, de forma predeterminada, las restricciones están en el nivel suave o de optimización (encontrará más información sobre las prioridades de las restricciones a lo largo de este artículo). Sin embargo, podría ver mensajes de mantenimiento relacionados con estas restricciones si están configuradas como restricciones rígidas o en los casos excepcionales que hacen que los nodos se pueden eliminar, por lo que las presentamos aquí para dar una idea completa:

* ReplicaExclusionStatic y ReplicaExclusionDynamic: son restricciones internas que indican que durante la búsqueda, hemos encontrado una situación en la que dos réplicas con estado o instancias sin estado de la misma partición se tendrían que colocar en el mismo nodo (lo cual no se permite). ReplicaExclusionStatic y ReplicaExclusionDynamic son casi exactamente la misma regla. La restricción ReplicaExclusionDynamic dice "no hemos podido colocar esta réplica aquí porque la única solución propuesta ya había colocado aquí una réplica". Esta exclusión es diferente de la exclusión ReplicaExclusionStatic que no indica un conflicto propuesto sino uno real: ya hay una réplica en el nodo. ¿Resulta confuso? Sí. ¿Importa mucho? No. Basta con decir que si está viendo una secuencia de eliminación de restricciones que contiene la restricción ReplicaExclusionStatic o ReplicaExclusionDynamic, Cluster Resource Manager considera que no hay suficientes nodos para colocar todas las réplicas. Las restricciones siguientes normalmente pueden decirnos cómo es que estamos acabando con tan pocos nodos en el primer lugar.
* PlacementConstraint: si ve este mensaje, significa que eliminamos algunos nodos porque no coincidían con las restricciones de selección de ubicación del servicio. Realizamos el seguimiento de las restricciones de selección de ubicación configuradas actualmente como parte de este mensaje. Generalmente esto es normal si ha proporcionado alguna restricción de selección de ubicación, pero si se produce un error en la restricción de selección de ubicación que causa la eliminación de demasiados nodos, es aquí donde puede ver ese resultado.
* NodeCapacity: si ve esta restricción, significa que no pudimos colocar las réplicas en los nodos indicados porque de hacerlo el nodo habría sobrepasado su capacidad.
* Affinity: esta restricción indica que no pudimos colocar la réplica en los nodos afectados porque se produciría una infracción de la restricción de afinidad.
* FaultDomain y UpgradeDomain: estas restricciones eliminan los nodos si el hecho de colocar la réplica en los nodos indicados haría que se empaquetara en un determinado dominio de error o actualización. En el tema sobre [las restricciones de dominio de error y de actualización y el comportamiento resultante](service-fabric-cluster-resource-manager-cluster-description.md) se presentan varios ejemplos que tratan esta restricción.
* PreferredLocation: lo normal es que no vea esta restricción que hace que los nodos se eliminen de la solución dado que, de forma predeterminada, solo es una optimización. Además, aunque la restricción de ubicación preferida solo está presente habitualmente durante las actualizaciones (cuando se usa para mover las réplicas a donde estaban cuando se inició la actualización), es posible verla.

### Prioridades de restricción
En todas estas restricciones, puede que haya estado pensando: "Creo que esas restricciones de colocación son el aspecto más importante de mi sistema. Estoy dispuesto a infringir otras restricciones, incluso cosas como afinidad y capacidad, si con ello tengo la garantía de que las restricciones de colocación no se van a infringir jamás".

Bien, pues resulta que podemos hacerlo. Se pueden configurar restricciones con unos cuantos niveles diferentes de aplicación, pero se reducen a "fuerte" (0), "suave" (1), "optimización" (2) y "desactivado" (-1). La mayoría de las restricciones las hemos definido como fuertes de forma predeterminada (dado que, por ejemplo, la mayoría de la gente no piensa normalmente en la capacidad como algo en lo que están dispuestos a bajar la guardia) y casi todas son fuertes o suaves. Sin embargo, en situaciones más complejas se pueden cambiar. Por ejemplo, ¿y si quisiera asegurarse de que la afinidad siempre se infrinja con el fin de resolver los problemas de capacidad de los nodos? Podría establecer la restricción de afinidad en "suave" (1) como algo prioritario y dejar la restricción de capacidad establecida como "rígida" (0). Las diferentes prioridades de restricción son también el motivo de que se vean algunas advertencias de infracción de restricciones con más frecuencia que otras, puesto que hay ciertas restricciones que estamos dispuestos a dejar pasar (infringir) temporalmente. Tenga en cuenta que estos niveles no significan realmente que una restricción dada vaya o no a ser siempre infringida, sino simplemente que hay un orden en el que se aplicarán preferentemente de modo que podamos sopesar las ventajas e inconvenientes cuando sea imposible satisfacer todas.

La configuración y los valores de prioridad predeterminados de las distintas restricciones se enumeran a continuación:

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

Aquí podrá observar que hay restricciones definidas para dominios de actualización y error, y también que la restricción del dominio de actualización es suave. También está esta rara restricción, "PreferredLocation", con una prioridad. ¿Qué es todo esto?

En primer lugar, damos forma al deseo de mantener los servicios repartidos entre dominios de actualización y error como una restricción dentro del motor de Resource Manager. Históricamente, ha habido un par de veces en que debíamos ser realmente estrictos sobre la colocación con respecto a los dominios de actualización y error, y también otro par de veces en que había algún problema donde simplemente debíamos ignorarla por completo (aunque brevemente), así que en general nos hemos sentido complacidos por esa elección de diseño y la flexibilidad de la infraestructura de restricciones. De todas formas, la mayoría del tiempo, se puede considerar una restricción suave, lo que significa que si Resource Manager debe empaquetar temporalmente un par de réplicas en un dominio de actualización para tratar con, pongamos, una actualización en curso o un grupo de errores repetidos u otras infracciones de restricciones (de las restricciones fuertes), pues está bien hecho. Esto normalmente no pasa a no ser que haya muchos errores u otra renovación en el sistema que impida la correcta colocación, y si el entorno está configurado correctamente, el estado estable es que los dominios de y actualización siempre se respeten completamente.

La restricción PreferredLocation es un poco diferente, de ahí que sea la única restricción establecida en "optimización". Usamos esta restricción mientras las actualizaciones están en marcha para intentar volver a colocar los servicios donde estaban antes de la actualización. Existen todo tipo de razones por las que esto podría no funcionar en la práctica, pero es una buena optimización, así que aquí la dejamos. Trataremos este tema más a fondo cuando hablemos sobre cómo Cluster Resource Manager ayuda con las actualizaciones.

## Actualizaciones
Cluster Resource Manager también ayuda durante las actualizaciones de clústeres y aplicaciones para garantizar que la actualización transcurre sin problemas, y para asegurar también que las reglas y el rendimiento del clúster no se vean comprometidos.

### Aplicación continuada de las reglas
Lo más importante a tener en cuenta en este punto es que las reglas, los controles estrictos en torno a elementos como las restricciones de selección de ubicación, se siguen aplicando durante las actualizaciones. Podría pensar que no hace falta decirlo, pero de cualquier modo nosotros se lo decimos porque queremos ser explícitos sobre ello. La parte positiva es que puede tener la garantía de que si quisiera asegurarse de que determinadas cargas de trabajo no se ejecuten en determinados nodos, esas reglas se aplicarían incluso durante la actualización automáticamente. Si su entorno está muy restringido, puede que las actualizaciones tarden mucho tiempo, dado que solo hay unas cuantas opciones en relación a dónde puede ir un servicio (o el nodo en el que se encuentra) si es necesario desactivarlo para la aplicación de una actualización.

### Sustituciones inteligentes
Cuando se inicia una actualización, el Administrador de recursos toma una instantánea de la disposición actual del clúster e intenta devolver las cosas a ese estado cuando se realiza la actualización. La lógica detrás de este procedimiento es sencilla: primero se asegura de que solo haya un par de transiciones para cada servicio, réplica o instancia como parte de la actualización (el movimiento fuera del nodo afectado y el movimiento de entrada de vuelta). En segundo lugar, se asegura de que la actualización en sí no tenga mucho impacto sobre el diseño del clúster; si el clúster estaba bien organizado antes de la actualización estará bien organizado después de ella, o al menos no peor.

### Renovación reducida
Otra cosa que sucede durante las actualizaciones es que Cluster Resource Manager desactiva el equilibrio para la entidad que se actualiza. De modo que, si tiene dos instancias de aplicación diferentes y luego inicia una actualización en una de ellas, el equilibrio se detiene para esa instancia de aplicación, pero no para la otra. Al impedir que se reactive el equilibrio se impiden reacciones innecesarias a la propia actualización (¡"Oh no! ¡Un nodo vacío!). ¡Mejor llenarlo con todo tipo de cosas!") y, como consecuencia, se impiden muchos de los movimientos adicionales de los servicios del clúster que se deberían deshacer cuando los servicios tienen que volver a los nodos una vez finalizada la actualización. Si la actualización en cuestión es una actualización de clúster, todo el clúster se pausa para equilibrarlo durante la actualización (las comprobaciones de las restricciones, que aseguran que las reglas se aplican, permanecen activas, solo se desactiva el reequilibrado proactivo).

### Reglas relajadas
Normalmente, durante las actualizaciones, querrá que la operación se complete aunque el clúster esté bastante restringido o lleno. Durante las actualizaciones, poder administrar la capacidad del clúster es incluso más importante que normalmente, ya que generalmente se pierde entre un 5 y un 20 por ciento de la capacidad cada vez que la actualización se implementa en el clúster, y esa carga de trabajo normalmente tiene que ir a algún sitio. Y aquí es donde entra en juego la noción de [capacidades almacenadas en búfer](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity); aunque la capacidad almacenada en búfer se respeta durante el funcionamiento normal (dejando algo de espacio de sobrecarga), Cluster Resource Manager utilizará la capacidad total (ocupando todo el búfer) durante las actualizaciones.

## Pasos siguientes
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).

<!---HONumber=AcomDC_0824_2016-->