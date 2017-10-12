---
title: "Recuperación ante desastres de Service Fabric de Azure | Microsoft Docs"
description: "Service Fabric de Azure ofrece las funciones necesarias para tratar con todo tipo de desastre. En este artículo se describen los tipos de desastres que pueden surgir y cómo resolverlos."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5346e331d76149ac3aed7aaf11eb3171e0ac5cfc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperación ante desastres en Service Fabric de Azure
Una parte fundamental de la entrega de alta disponibilidad es garantizar que los servicios sobreviven a cualquier tipo de error. Esto es especialmente importante para los errores imprevistos y que se encuentran fuera de control. En este artículo se describen algunos modos de error comunes que podrían ser desastres si no hubieran modelado y administrado correctamente. También se explican las mitigaciones y las acciones que deben realizarse en caso de desastre. El objetivo es limitar o eliminar el riesgo de pérdida de datos o de tiempo de inactividad cuando se produzcan errores, tanto planeados como de otro tipo.

## <a name="avoiding-disaster"></a>Impedimento de desastres
El objetivo principal de Service Fabric es ayudarle a modelar tanto el entorno como los servicios de tal manera que los tipos de error comunes no se conviertan en desastres. 

En general, hay dos tipos de escenarios de desastre o error:

1. Errores de hardware o software
2. Errores operativos

### <a name="hardware-and-software-faults"></a>Errores de hardware y software
Los errores de hardware y software son imprevisibles. La manera más fácil de sobrevivir a errores es ejecutar más copias del servicio distribuido más allá de los límites del hardware o software defectuoso. Por ejemplo, si el servicio se ejecuta solamente en una máquina concreta, el error que se produce en esa máquina es un desastre para el servicio. La manera más sencilla de evitar este desastre es asegurarse de que el servicio se ejecuta en realidad en varias máquinas. Puede ser necesario realizar pruebas para garantizar que el error de una máquina no interrumpe el servicio en ejecución. El planeamiento de capacidad garantiza que se puede crear una instancia de reemplazo en otra parte y que la reducción de capacidad no sobrecarga el resto de servicios. Este patrón funciona independientemente de dónde intente evitar el error. Por ejemplo. si le preocupa el error de una red de área de sistema, debe ejecutarlo en varias redes de área de sistema. Si le preocupa perder un conjunto de servidores, debe ejecutarlo en varios conjuntos. Si le preocupa la pérdida de centros de datos, el servicio debe ejecutarse en varias regiones de Azure o centros de datos. 

Con la ejecución en este tipo de modo distribuido, sigue sujeto a ciertos tipos de errores simultáneos, pero errores únicos o incluso varios del mismo tipo (por ejemplo, un error solo en una máquina virtual o un vínculo de la red) se resuelven automáticamente, por lo que dejan de ser un "desastre". Service Fabric proporciona varios mecanismos para expandir el clúster y se ocupa de recuperar nodos y servicios defectuosos. También permite ejecutar varias instancias de los servicios para evitar que estos tipos de errores imprevistos se conviertan en verdaderos desastres.

Puede haber razones por las que ejecutar una gran implementación para abarcar los errores no sea posible. Por ejemplo, puede implicar más recursos de hardware que los que esté dispuesto a pagar por el riesgo de error. Cuando se trabaja con aplicaciones distribuidas, es posible que los costos de los saltos de comunicación o de replicación de estados adicionales a distancia geográfica provoquen una latencia inaceptable. Esta línea se traza en función de cada aplicación. En concreto, para errores de software, el error puede encontrarse en el servicio que intente escalar. En este caso, el hecho de tener más copias no evita el desastre, ya que la condición de error se encuentra en todas las instancias.

### <a name="operational-faults"></a>Errores operativos
Aunque se distribuya el servicio por todo el mundo con redundancias, pueden seguir produciéndose eventos de desastre. Por ejemplo, si alguien vuelve a configurar por error el nombre de dns del servicio o directamente lo elimina. A modo de ejemplo, supongamos que tuviera un servicio de Service Fabric con estado y que alguien lo elimina por error. A menos que haya otra mitigación, ese servicio y su estado habrán desaparecido. Estos tipos de desastres operativos ("vaya") requieren más mitigaciones y pasos para la recuperación que los errores imprevistos normales. 

Lo mejor para evitar estos tipos de errores operativos es
1. restringir el acceso operativo al entorno;
2. inspeccionar minuciosamente las operaciones peligrosas;
3. imponer la automatización, evitar los cambios manuales o fuera de banda y validar los cambios concretos en el entorno real antes de establecerlos, y
4. asegurarse de que las operaciones destructivas "se suavizan". Las operaciones de software suavizadas no surten efecto inmediatamente o se pueden deshacer en un plazo determinado.

Service Fabric proporciona mecanismos para evitar errores operativos, como un control de acceso [basado en rol](service-fabric-cluster-security-roles.md) para las operaciones del clúster. Sin embargo, la mayoría de estos errores operativos requiere esfuerzos organizativos y otros sistemas. Service Fabric proporciona mecanismos para sobrevivir a errores operativos, concretamente, de copia de seguridad y restauración, para los servicios con estado.

## <a name="managing-failures"></a>Administración de errores
El objetivo de Service Fabric casi siempre es la administración automática de los errores. Sin embargo, para abordar algunos tipos de errores, los servicios deben tener código adicional. Otros tipos de errores _no_ deben solucionarse automáticamente, por motivos de seguridad y continuidad empresarial. 

### <a name="handling-single-failures"></a>Abordaje de errores únicos
Una máquina puede fallar por todo tipo de razones. Algunas de las causas provienen del hardware, como los errores en los suministros de alimentación o el hardware de las redes. Otros errores son de software. Se trata de errores en el sistema operativo y el propio servicio. Service Fabric detecta automáticamente estos tipos de errores, incluidos los casos en los que la máquina se aísla de otras por problemas de red.

Independientemente del tipo de servicio, ejecutar una sola instancia se traduce en tiempo de inactividad de ese servicio si se produce un error en la copia única del código por cualquier motivo. 

Para abordar cualquier error único, lo más sencillo es garantizar que los servicios se ejecutan en más de un nodo de forma predeterminada. Para los servicios sin estado, esto se consigue con un `InstanceCount` mayor que 1. Para los servicios con estado, la recomendación es siempre que `TargetReplicaSetSize` y `MinReplicaSetSize` sean 3 como mínimo. La ejecución de más copias del código de servicio garantiza que este puede abordar cualquier error único automáticamente. 

### <a name="handling-coordinated-failures"></a>Abordaje de errores coordinados
En un clúster se pueden producir errores coordinados por errores y cambios de infraestructura planeados o imprevistos, o por cambios de software planeados. Service Fabric modela las zonas de infraestructura que experimentan errores coordinados como dominios de error. Las áreas que experimentarán cambios coordinados de software se modelan como dominios de actualización. Para más información acerca de los dominios de error y de actualización, consulte [este documento](service-fabric-cluster-resource-manager-cluster-description.md), que indica la definición y la topología del clúster.

Service Fabric considera los dominios de error y de actualización al planear dónde deben ejecutarse los servicios. De forma predeterminada, intenta asegurarse de que los servicios se ejecutan en varios dominios de error y de actualización, de manera que si se realizan cambios planeados o imprevistos, los servicios permanecen disponibles. 

Por ejemplo, supongamos que un error en una fuente de alimentación provoca un error simultáneo en un conjunto de máquinas. Con varias copias del servicio en ejecución, la pérdida de varias máquinas del dominio de error se convierte simplemente en otro ejemplo de error único para un servicio determinado. Por este motivo la administración de dominios de error es crucial para garantizar la alta disponibilidad de los servicios. Al ejecutar Service Fabric en Azure, los dominios de error se administran automáticamente. En otros entornos puede que esto no sea así. Si va a compilar sus propios clústeres locales, asegúrese de asignar y planear el diseño de los dominios de error correctamente.

Los dominios de actualización son útiles para modelar las áreas donde se va actualizar software de manera simultánea. Por este motivo, los dominios de actualización a menudo también definen los límites de desconexión del software durante las actualizaciones planeadas. Las actualizaciones de Service Fabric y los servicios siguen el mismo modelo. Para más información acerca de las actualizaciones graduales, los dominios de actualización y el modelo de estado de Service Fabric que ayuda a evitar que los cambios por error afecten al clúster y al servicio, consulte estos documentos:

 - [Actualización de aplicaciones](service-fabric-application-upgrade.md)
 - [Tutorial sobre la actualización de aplicaciones](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de estado de Service Fabric](service-fabric-health-introduction.md)

Puede visualizar el diseño del clúster mediante el mapa de clúster que se proporciona en [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Distribución de nodos entre dominios de error en Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> El modelado de áreas de error, las actualizaciones graduales, la ejecución de muchas instancias del código de servicio y el estado, las reglas de colocación para asegurarse de que los servicios se ejecutan entre dominios de error y de actualización, y la supervisión de estado integrada son solo **algunas** de las características que Service Fabric proporciona para evitar que los errores y problemas operativos normales se conviertan en desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Abordaje de errores simultáneos en el hardware y en el software
Anteriormente hablamos de errores únicos. Como puede ver, son fáciles de solucionar para los servicios con y sin estado con más copias del código (y del estado) en varios dominios de error y de actualización. También se pueden producir varios errores simultáneos y aleatorios. Estos tienen mayor probabilidad de resultar en un verdadero desastre.


### <a name="random-failures-leading-to-service-failures"></a>Errores aleatorios que dan lugar a errores en el servicio
Supongamos que el servicio tenía un `InstanceCount` de 5 y en varios nodos donde se ejecutan las instancias se produjeron errores al mismo tiempo. Service Fabric responde mediante la creación automática de instancias de reemplazo en otros nodos. Seguirá creando reemplazos hasta que el servicio vuelva al recuento de instancias deseado. Otro ejemplo: supongamos que había un servicio sin estado con un `InstanceCount`de -1, lo que significa que se ejecuta en todos los nodos válidos del clúster. Supongamos que algunas de esas instancias produjeron un error. En este caso, Service Fabric observa que el servicio no se encuentra en el estado deseado e intenta crear las instancias en los nodos donde faltan. 

Para los servicios con estado la situación depende de si estos tienen estado persistente o no. También depende de cuántas réplicas tuviera el servicio y cuántas produjeran el error. La determinación de si se ha producido un desastre para un servicio con estado y su administración constan de tres etapas:

1. Determinación de si se ha producido pérdida de cuórum o no
 - Una pérdida de cuórum ocurre siempre que la mayoría de réplicas de un servicio con estado están inactivas al mismo tiempo, la principal incluida.
2. Determinación de si la pérdida de cuórum es permanente o no
 - La mayoría de las veces, los errores son temporales. Los procesos, los nodos y las máquinas virtuales se reinician y las particiones de la red se reparan. Sin embargo, a veces los errores son permanentes. 
    - Para servicios sin estado persistente, un error de un cuórum o varios en las réplicas deriva _inmediatamente_ en una pérdida de cuórum permanente. Cuando Service Fabric detecta la pérdida de cuórum en un servicio sin estado persistente, declara (posible) pérdida de datos y procede inmediatamente al paso 3. Proceder como pérdida de datos tiene sentido, ya que Service Fabric sabe que no merece la pena esperar el retorno de las réplicas, porque aunque se recuperaran, estarían vacías.
    - En los servicios con estado persistente, un error de un cuórum o varios en las réplicas provoca que Service Fabric empiece a esperar el retorno de las réplicas y la restauración del cuórum. Esto supone la interrupción del servicio de _escritura_ en las particiones afectadas (o "conjunto de réplicas") del servicio. Sin embargo, la lectura seguirá siendo posible, con garantía de coherencia reducida. La cantidad de tiempo predeterminada que espera Service Fabric a que el cuórum se restaure es infinita, ya que continuar supone un (posible) evento de pérdida de datos y acarrea otros riesgos. Es posible invalidar el valor `QuorumLossWaitDuration` predeterminado, pero no se recomienda. Llegados a este punto, es mejor concentrarse en restaurar las réplicas fuera de servicio. Esto requiere reactivar los nodos fuera de servicio y asegurarse de que pueden volver a montar las unidades donde almacenaban el estado local persistente. Si la pérdida de cuórum se produce por un error de proceso, Service Fabric intenta volver a crear los procesos y reiniciar sus réplicas automáticamente. Si no es posible, Service Fabric informa de los errores de estado. Si se pueden resolver, las réplicas se suelen restaurar. A veces, sin embargo, no se pueden restaurar. Por ejemplo, que el error se haya producido en todas las unidades o que las máquinas se hayan destruido físicamente de alguna manera. En estos casos, lo que ocurre es una evento de pérdida de cuórum permanente. Para indicar a Service Fabric que deje de esperar a que las réplicas fuera de servicio se restauren, un administrador de clústeres debe determinar qué particiones de qué servicios están afectadas y llamar a la API `Repair-ServiceFabricPartition -PartitionId` o ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`.  Esta API permite especificar el identificador de la partición que va a cambiar de pérdida de cuórum a posible pérdida de datos.

> [!NOTE]
> _Nunca_ es seguro utilizar esta API de manera que no sea directamente con particiones específicas. 
>

3. Determinación de si se ha producido realmente pérdida de datos y restauración de copias de seguridad
  - Cuando Service Fabric llama al método `OnDataLossAsync`, siempre es porque se _sospecha_ pérdida de datos. Service Fabric garantiza que esta llamada se entrega a la _mejor_ de las réplicas restantes, que es la que haya progresado más. El motivo por el que siempre decimos _sospecha_ de pérdida de datos es que puede que el resto de réplicas tengan el mismo estado que la principal cuando quedara fuera de servicio. Sin embargo, sin ese estado con el que compararlo, no hay forma de que Service Fabric o los operadores lo sepan con seguridad. En este punto, Service Fabric también sabe que las demás réplicas no se van a recuperar. Esa fue la decisión que se tomó al detener la espera a que la pérdida de cuórum se resolviera por sí sola. La mejor forma de proceder con el servicio es la inmovilización mientras se espera la intervención administrativa específica. Entonces, ¿qué hace una implementación típica del método `OnDataLossAsync`?
  - En primer lugar, registra que se ha desencadenado `OnDataLossAsync` y activa las alertas administrativas necesarias.
   - En este momento suele proceder la inmovilización y la espera a la toma de otras decisiones y a la realización de acciones manuales. Esto es así porque aunque haya copias de seguridad disponibles, puede ser necesario prepararlas. Por ejemplo, si dos servicios diferentes coordinan información, es necesario modificar las copias de seguridad para garantizar que, cuando se haya realizado la restauración, la información importante para los dos servicios sea coherente. 
  - A menudo también hay otros datos de telemetría o salidas del servicio. Estos metadatos pueden estar contenidos en otros servicios o registros. Esta información puede usarse si es necesaria para determinar si hubo llamadas recibidas y procesadas en la réplica principal que no estaban presentes en la copia de seguridad o en esta réplica determinada. Puede que sea necesario reproducirlas o agregarlas a la copia de seguridad para poder realizar la restauración.  
   - Comparación del estado de las réplicas restantes con el de las copias de seguridad disponibles. Si se usan las colecciones de confianza de Service Fabric, hay herramientas y procesos disponibles para ello, que se describen en [este artículo](service-fabric-reliable-services-backup-restore.md). El objetivo consiste en ver si el estado de la réplica es suficiente o si falta algo en la copia de seguridad.
  - Una vez realizada la comparación y si se completó la restauración necesaria, el código del servicio debe devolver true si se han realizado cambios de estado. Si la réplica determinó que era la mejor copia disponible del estado y no realizó ningún cambio, devolverá false. True indica que _cualquier_ réplica restante ahora puede ser incoherente con esta. Se descartarán y se volverán a crear a partir de esta réplica. False indica que se realizó ningún cambio de estado, por lo que las demás réplicas pueden conservar el contenido. 

Es muy importante que los autores de los servicios practiquen con escenarios de posible pérdida de datos y error antes de implementarlos en producción. Como protección ante la posible pérdida de datos, es importante periódicamente realizar una [copia de seguridad del estado](service-fabric-reliable-services-backup-restore.md) de los servicios con estado en almacenes con redundancia geográfica. También debe asegurarse de que la puede restaurar. Como se realizan copias de seguridad de muchos servicios diferentes en momentos distintos, deberá asegurarse de que una vez restaurados, los servicios se ven de manera coherente entre ellos. Por ejemplo, considere una situación donde un servicio genera un número y lo almacena, a continuación, lo envía a otro servicio que también lo almacena. Después de una restauración, puede que descubra que el segundo servicio tiene el número pero el primero no, porque su copia de seguridad no incluye esa operación.

Si descubre que las réplicas restantes son insuficientes para continuar en un escenario de pérdida de datos y no se puede reconstruir el estado del servicio a partir de los datos de telemetría o la salida, la frecuencia de las copias de seguridad determina el mejor objetivo de punto de recuperación (RPO) posible. Service Fabric proporciona muchas herramientas para probar varios escenarios de error, incluido el cuórum permanente y la pérdida de datos que requieren la restauración a partir de una copia de seguridad. Estos escenarios forman parte de las herramientas de capacidad de prueba de Service Fabric, administradas por el servicio de análisis de errores. Más información sobre las herramientas y los patrones disponible [aquí](service-fabric-testability-overview.md). 

> [!NOTE]
> Los servicios del sistema también pueden sufrir pérdida de quórum, afectando específicamente al servicio en cuestión. Por ejemplo, la pérdida de cuórum en el servicio de nombres afecta a la resolución de nombres, mientras que la pérdida de cuórum en el administrador de conmutación por error bloquea la creación de nuevos servicios y las conmutaciones por error. Aunque los servicios de sistema de Service Fabric siguen el mismo patrón que los servicios de administración de estado, no se recomienda intentar sacarlos de la pérdida de cuórum para incluirlos en posible pérdida de datos. En su lugar, se recomienda [buscar soporte técnico](service-fabric-support.md) para determinar una solución a la situación específica.  Suele ser preferible esperar a que las réplicas inactivas vuelvan a estar disponibles.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidad del clúster de Service Fabric
Por lo general, el propio clúster de Service Fabric es un entorno altamente distribuido sin puntos de error único. Un error en cualquiera de los nodos no provoca problemas de disponibilidad o confiabilidad en el clúster, principalmente porque los servicios de sistema de Service Fabric siguen las directrices anteriores: siempre se ejecutan con tres o más réplicas de forma predeterminada y los servicios de sistema sin estado se ejecutan en todos los nodos. Las capas subyacentes de detección de errores y redes de Service Fabric están totalmente distribuidas. La mayoría de los servicios de sistema se regeneran a partir de los metadatos del clúster o saben cómo volver a sincronizar su estado desde otros lugares. La disponibilidad del clúster podría deteriorarse si los servicios de sistema se encuentran en situaciones de pérdida de cuórum como las descritas anteriormente. En estos casos, quizá no pueda realizar algunas operaciones en el clúster, como comenzar una actualización o implementar servicios nuevos, pero el clúster continúa activo. Los servicios en ejecución seguirán ejecutándose en estas condiciones, a menos que requieran que las operaciones de escritura en los servicios de sistema continúen funcionando. Por ejemplo, si el Administrador de conmutación por error está en pérdida de cuórum, todos los servicios continuarán ejecutándose, pero los servicios que produzcan un error no podrán reiniciarse automáticamente, ya que esto requiere la participación del Administrador de conmutación por error. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Errores en un centro de datos o una región de Azure
En raras ocasiones, los centros de datos físicos pueden dejar de estar disponibles temporalmente debido a la pérdida de conectividad de red o de alimentación. En estos casos, los clústeres de Service Fabric y los servicios de ese centro de datos o la región de Azure dejarán de estar disponibles. Sin embargo, _los datos se conservan_. Para los clústeres que se ejecutan en Azure, puede ver las actualizaciones en caso de interrupciones en la [página de estado de Azure][azure-status-dashboard]. En el caso muy poco probable de que se destruyera un centro de datos físico parcial o totalmente, los clústeres de Service Fabric que hospede o sus servicios se podrían perder. Esto incluye cualquier estado del que no se haya realizado copia de seguridad fuera de ese centro de datos o esa región.

Hay dos estrategias diferentes para sobrevivir al error permanente o duradero de un único centro de datos o región. 

1. Ejecutar clústeres de Service Fabric independientes en varias de estas regiones y utilizar algún mecanismo de conmutación por error y conmutación por recuperación entre estos entornos. Este tipo de modelo de varios clústeres activos/activos o activos/ pasivos requiere código adicional de administración y operación. Esto también requiere la coordinación de las copias de seguridad de los servicios de un centro de datos o región para que estén disponibles en otros centros de datos o regiones cuando uno produzca un error. 
2. Ejecutar un único clúster de Service Fabric que abarque varios centros de datos o regiones. La configuración mínima admitida para esto es tres centros de datos o regiones. El número recomendado de regiones o centros de datos es cinco. Esto requiere una topología de clúster más compleja. Sin embargo, la ventaja de este modelo es que el error de un centro de datos o región se transforma de desastre en error normal. Estos errores se solucionan mediante los mecanismos que funcionan para los clústeres de una única región. Los dominios de error, los dominios de actualización y las reglas de colocación de Service Fabric aseguran que las cargas de trabajo se distribuyen para tolerar los errores de normales. Para más información sobre las directivas que pueden ayudar a que los servicios funcionen en este tipo de clúster, lea sobre las [directivas de colocación](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Errores aleatorios que dan lugar a errores en el clúster
Service Fabric tiene el concepto de los nodos raíz. Se trata de nodos que conservan la disponibilidad del clúster subyacente. Estos nodos ayudan a garantizar que el clúster permanece activo, al establecer concesiones con otros nodos y servir de elemento diferenciador durante ciertos tipos de errores de red. Si los errores aleatorios eliminan la mayoría de los nodos raíz del clúster y no se recuperan, el clúster se cierra automáticamente. En Azure, los nodos raíz se administran automáticamente: se distribuyen entre los dominios de actualización y error disponibles y, si se quita un solo nodo raíz del clúster, se crea otro en su lugar. 

En los clústeres independientes de Service Fabric y Azure, el "tipo de nodo principal" es el que ejecuta los nodos raíz. Al definir un tipo de nodo principal, Service Fabric aprovechará automáticamente el número de nodos proporcionado mediante la creación de hasta 9 nodos raíz y 9 réplicas de cada servicio de sistema. Si un conjunto de errores aleatorios deshabilita la mayoría de las réplicas del servicio de sistema al mismo tiempo, los servicios de sistema entran en pérdida del cuórum, tal y como se ha descrito anteriormente. Si se pierde la mayoría de los nodos raíz, el clúster se apaga poco después.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre cómo simular varios errores mediante la [plataforma de comprobación](service-fabric-testability-overview.md)
- Lea otros recursos sobre alta disponibilidad y recuperación ante desastres. Microsoft ha publicado una gran cantidad de instrucciones sobre estos temas. Aunque algunos de estos documentos se refieren a técnicas específicas para su uso en otros productos, contienen muchos procedimientos recomendados generales que se pueden aplicar también en el contexto de Service Fabric:
  - [Lista de comprobación de disponibilidad](../best-practices-availability-checklist.md)
  - [Exploración en profundidad de la recuperación ante desastres](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure][dr-ha-guide]
- Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
