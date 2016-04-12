<properties
   pageTitle="Administrador de recursos de clúster de Service Fabric: integración de la administración | Microsoft Azure"
   description="Una visión general de los puntos de integración entre el Administrador de recursos de clúster y la administración de Service Fabric."
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


# Integración del Administrador de recursos de clúster con la administración de clústeres de Service Fabric
El Administrador de recursos no es el componente principal de Service Fabric que controla las operaciones de administración (como las actualizaciones de aplicaciones) pero sí ayuda. Para empezar, el Administrador de recursos realiza el seguimiento del estado deseado del clúster y de los servicios que contiene desde una perspectiva de distribución de recursos y equilibrio y efectúa cambios mediante el subsistema de Service Fabric cuando las cosas van mal. Otra parte de la integración tiene que ver con el modo en que funcionan las actualizaciones; en concreto, en su transcurso, algunas cosas sobre cómo funciona el Administrador de recursos cambian y se invocan algunos comportamientos especiales. Trataremos ambos temas a continuación.

## Integración del mantenimiento
El Administrador de recursos realiza constantemente un seguimiento de las reglas que ha definido para sus servicios y emite advertencias y errores de mantenimiento si no se pueden satisfacer esas reglas. Por ejemplo, si un nodo está por encima de su capacidad y el Administrador de recursos no puede corregir la situación, emitirá una advertencia de estado para indicar este hecho y cuáles son las métricas implicadas.

Otro ejemplo de cuando verá que el Administrador de recursos emite advertencias de mantenimiento es si ha definido una restricción de selección de ubicación (como "NodeColor == Azul") y el Administrador de recursos detecta una infracción de restricción. Esto se hace para las restricciones personalizadas y para las restricciones predeterminadas (como la distribución de dominio de error y actualización) que el Administrador de recursos impone. A continuación se muestra un ejemplo de un informe de mantenimiento de este tipo En este caso el informe de mantenimiento es de una de las particiones del sistema porque las réplicas de esa partición están temporalmente empaquetadas en demasiados dominios de error, como podría suceder debido a una cadena de errores:

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_FaultDomain', HealthState='Warning', ConsiderWarningAsError=false.

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
                        Property              : ReplicaConstraintViolation_FaultDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: FaultDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Esto es lo que nos dice este mensaje de mantenimiento:

1.	Todas las réplicas en sí mismas son correctas (esta es la primera prioridad de Service Fabric).
2.	La restricción de distribución de dominios de error se está infringiendo actualmente (lo que significa que un determinado dominio de error tiene más réplicas de las que debería).
3.	El nodo que contiene la réplica que provoca la infracción (el nodo con el id.: 3d1a4a68b2592f55125328cd0f8ed477)
4.	Cuándo tuvo lugar todo esto (8/10/2015 7:13:02 PM). Estos son excelentes datos para disparar una alerta en producción que le permita saber que algo ha sucedido, ya que probablemente querrá echar un vistazo. En este caso, por ejemplo, nos gustaría ver si podemos averiguar por qué el Administrador de recursos no creyó que tuviera más opción que empaquetar las réplicas en el dominio de error. Pudo ser porque todos los nodos de los otros dominios de error estaban inactivos y no había suficientes dominios de repuesto, o, si había suficientes dominios activos, otra cosa provocó que los nodos de esos otros dominios de error se invalidaran (por ejemplo, debido a una directiva InvalidDomain en el servicio).

Sin embargo, supongamos que quiere crear un servicio o que el Administrador de recursos está intentando encontrar un lugar donde colocar algunos servicios pero no parece haber ninguna solución que funcione. Esto podría ser por muchos motivos, pero habitualmente se debe a una de las siguientes condiciones:

1.	Alguna condición transitoria ha hecho que sea imposible colocar esta instancia o réplica de servicio correctamente.
2.	Los requisitos del servicio están mal configurados de forma que son imposibles de satisfacer.

En cada una de estas condiciones, verá un informe de mantenimiento del Administrador de recursos con información para ayudarle a determinar qué es lo que pasa y por qué no se puede colocar el servicio. A este proceso lo llamamos "secuencia de eliminación de restricciones". En su transcurso, recorremos las restricciones configuradas que afectan al servicio y vemos qué eliminan. Así, cuando los servicios no se pueden colocar, puede ver qué nodos se eliminaron y por qué. Vamos a hablar sobre cada una de las diferentes restricciones que puede ver en estos informes de mantenimiento y qué es lo que comprueban. Tenga en cuenta que casi nunca verá estos nodos de eliminación de restricciones dado que, de forma predeterminada, están en el nivel de software o de optimización (como hemos indicado anteriormente). Podría verlas si se voltean o se tratan como restricciones de hardware, por lo que las presentamos aquí para que tenga la información más completa posible:

-	ReplicaExclusionStatic y ReplicaExclusionDynamic: son restricciones internas que indican que durante la búsqueda hemos determinado que dos réplicas se deberían colocar en el nodo (lo cual no se permite). ReplicaExclusionStatic y ReplicaExclusionDynamic son casi exactamente la misma regla. La restricción ReplicaExclusionDynamic dice "no hemos podido colocar esta réplica aquí porque la única solución propuesta ya había colocado aquí una réplica". Esta exclusión es diferente de la exclusión ReplicaExclusionStatic que no indica un conflicto propuesto sino uno real: ya hay una réplica en el nodo. ¿Resulta confuso? Sí. ¿Importa mucho? No. Basta con decir que si está viendo una secuencia de eliminación de restricciones que contiene la restricción ReplicaExclusionStatic o ReplicaExclusionDynamic, el Administrador de recursos considera que no hay suficientes nodos para colocar todas las réplicas. Las restricciones siguientes normalmente pueden decirnos cómo es que estamos acabando con tan pocos nodos en el primer lugar.
-	PlacementConstraint: si ve este mensaje, significa que eliminamos algunos nodos porque no coincidían con las restricciones de selección de ubicación del servicio. Realizamos el seguimiento de las restricciones de selección de ubicación configuradas actualmente como parte de este mensaje.
-	NodeCapacity: si ve esta restricción, significa que no pudimos colocar las réplicas en los nodos indicados porque de hacerlo el nodo habría sobrepasado su capacidad.
-	Affinity: esta restricción indica que no pudimos colocar la réplica en los nodos afectados porque se produciría una infracción de la restricción de afinidad.
-	FaultDomain y UpgradeDomain: estas restricciones eliminan los nodos si el hecho de colocar la réplica en los nodos indicados haría que se empaquetara en un determinado dominio de error o actualización.
-	PreferredLocation: lo normal es que no vea esta restricción que hace que los nodos se eliminen de la solución dado que, de forma predeterminada, solo es una optimización. Además, aunque la restricción de ubicación preferida solo está presente habitualmente durante las actualizaciones (cuando se usa para mover las réplicas a donde estaban cuando se inició la actualización), es posible verla.

## Actualizaciones
El Administrador de recursos también ayuda durante las actualizaciones de clústeres y aplicaciones para garantizar que la actualización transcurre sin problemas y para asegurar también que las reglas y el rendimiento del clúster no se vean comprometidos.

### Aplicación continuada de las reglas
Lo más importante a tener en cuenta en este punto es que las reglas, los controles estrictos en torno a cosas como la selección de ubicación de las restricciones, se siguen aplicando durante las actualizaciones. Podría pensar que no hace falta decirlo, pero de cualquier modo nosotros se lo decimos porque queremos ser explícitos sobre ello. La parte positiva es que puede tener la garantía de que si quisiera asegurarse de que determinadas cargas de trabajo no se ejecuten en determinados nodos, eso no pasaría incluso durante la actualización, sin interacción de las operaciones. Si su entorno está muy restringido, puede que las actualizaciones tarden mucho tiempo dado que solo hay unas cuantas opciones de adónde puede ir un servicio si es necesario desactivarlo para la aplicación de revisiones.

### Sustituciones inteligentes
Cuando se inicia una actualización, el Administrador de recursos toma una instantánea de la disposición actual del clúster e intenta devolver las cosas a ese estado cuando se realiza la actualización. El razonamiento es sencillo: primero se asegura de que solo haya un par de transiciones para cada servicio como parte de la actualización (el movimiento fuera del nodo afectado y el movimiento de nuevo dentro). En segundo lugar, se asegura de que la actualización en sí no tenga mucho impacto sobre el diseño del clúster; si el clúster estaba bien organizado antes de la actualización estará bien organizado después de ella.

### Renovación reducida
Otra cosa que sucede durante las actualizaciones es que el Administrador de recursos desactiva el equilibrio para la entidad que se actualiza. De modo que, si tiene dos instancias de aplicación diferentes y luego inicia una actualización en una de ellas, el equilibrio se detiene para esa instancia de aplicación, pero no para la otra. Al impedir el equilibrio reactivo se impide que el Administrador de recursos reaccione a la actualización ("¡Oh no! ¡Un nodo vacío!). ¡Mejor llenarlo con todo tipo de cosas!”) y, como consecuencia, son muchos los movimientos adicionales de los servicios del clúster que se deben deshacer cuando los servicios tienen que volver a los nodos una vez finalizada la actualización. Si la actualización en cuestión es una actualización de clúster, todo el clúster se pausa para equilibrarlo durante la actualización (las comprobaciones de restricciones, lo que asegura que las reglas se aplican, permanecen activas).

### Reglas relajadas
Normalmente, durante las actualizaciones, querrá que la operación se complete aunque el clúster esté bastante restringido o lleno. Aunque ya hemos habado de cómo hacemos esto, durante las actualizaciones es más importante incluso ya que normalmente tendrá entre un 5 y un 20 por ciento del clúster inactivo en el momento en que la actualización se implementa en el clúster y esa carga de trabajo tiene que ir a alguna parte. Y aquí es donde entra en juego la noción de capacidades almacenadas en búfer que mencionamos anteriormente; aunque la capacidad almacenada en búfer se respeta durante el funcionamiento normal, el Administrador de recursos llenará hasta la capacidad total durante las actualizaciones.

## Pasos siguientes
- Empiece desde el principio y [obtenga una introducción al Administrador de recursos de clúster de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).

<!---HONumber=AcomDC_0316_2016-->