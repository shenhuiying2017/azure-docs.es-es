---
title: "Presentación de Service Fabric Cluster Resource Manager | Microsoft Docs"
description: "Una introducción a Service Fabric Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: ec470466f006265af5c4ccfddeeba975e6e648b5


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Presentación del Administrador de recursos de clúster de Service Fabric
Tradicionalmente, administrar sistemas de TI o un conjunto de servicios significaba tener algunas máquinas físicas o virtuales dedicadas a esos servicios o sistemas específicos. Muchos servicios principales se dividían en un nivel "web" y un nivel de "datos" o "almacenamiento", quizá con otros componentes especializados, como una caché. Otros tipos de aplicaciones tenían un nivel de mensajería donde entraban y salían las solicitudes. Este nivel se conectaba a un nivel de trabajo para cualquier transformación o análisis necesario como parte de la mensajería. Cada tipo de carga de trabajo tenía máquinas específicas dedicadas a ello: la base de datos contaba con un par de máquinas dedicadas, y los servidores web tenían unas cuantas más. Si un tipo determinado de carga de trabajo provocaba que las máquinas en las que estaba se sobrecalentaran, se agregaban más máquinas con esa misma configuración. De todas formas, la mayoría del tiempo se reemplazaban algunas de las máquinas por máquinas de mayor tamaño. Así de sencillo. Si se producía un error en una máquina, la parte afectada de la aplicación general se ejecutaba con una capacidad inferior hasta que pudiera restaurarse la máquina. Esto sigue siendo sencillo (aunque no necesariamente divertido).

Ahora bien, supongamos que tuviera la necesidad de escalar horizontalmente y usara la opción de contenedores y/o microservicios. Inesperadamente, se encontraría con decenas, cientos o incluso miles de máquinas. Tendría docenas de tipos de servicios distintos (ninguno de los cuales consumiría los recursos de toda una máquina), quizás cientos de instancias distintas de esos servicios. Cada instancia con nombre tendría una o más instancias o réplicas para alta disponibilidad (HA).

De repente, administrar su entorno ya no es tan sencillo como administrar unas cuantas máquinas dedicadas a un solo tipo de carga de trabajo. Sus servidores son virtuales y ya no tienen nombres (es decir, *ha* pasado de la mentalidad [de mascota a ganado](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). La configuración está menos orientada a las máquinas y se orienta directamente a los servicios. El hardware dedicado es cosa del pasado y los servicios se han convertido en pequeños sistemas distribuidos, que abarcan múltiples piezas pequeñas de hardware estándar.

Como consecuencia de la división de su aplicación (de la anterior división por bloques monolíticos a la división en servicios independientes que se ejecutan en hardware estándar), es posible que ahora tenga que ocuparse de muchas más combinaciones. ¿Quién decide qué tipos de cargas de trabajo pueden ejecutarse en cada hardware o cuántas? ¿Qué cargas de trabajo funcionan bien en el mismo hardware y cuáles entran en conflicto? Cuando una máquina deja de estar activa… ¿Qué se estaba ejecutando allí? ¿Quién es el responsable de asegurarse de que esa carga de trabajo empiece a ejecutarse de nuevo? ¿Debe esperar hasta que la máquina (virtual) regrese o sus cargas de trabajo conmutarán por error automáticamente a otras máquinas y seguirán ejecutándose? ¿Se requiere la intervención humana? ¿Qué pasa con las actualizaciones en este entorno?

Como desarrolladores y operadores que tienen que trabajar con esto, vamos a necesitar algo de ayuda para administrar esta complejidad. Tiene la sensación de que empezar a contratar mano de obra para ocultar la complejidad del proceso no es la mejor solución.

¿Qué debe hacer?

## <a name="introducing-orchestrators"></a>Presentación de los organizadores
"Orquestrador" es el término general que se usa para los componentes de software que ayudan a los administradores a gestionar estos tipos de entornos. Los orquestadores son los componentes que reciben solicitudes como "Quiero que se ejecuten cinco copias de este servicio en mi entorno". Tratan de que el entorno coincida con el estado deseado, sin importar lo que suceda.

Los orquestadores (y no el personal humano) son los que entran en acción cuando una máquina falla o cuando una carga de trabajo finaliza por algún motivo inesperado. La mayoría de los orquestadores no solo administran los errores. Otras características que tienen es administrar implementaciones nuevas, controlar las actualizaciones y administrar el consumo de recursos. Todos los orquestadores se dedican fundamentalmente a mantener el estado de configuración deseado en el entorno. Sabemos que lo que busca es poder indicarle a un orquestador qué quiere y dejar que haga el trabajo pesado. Chronos o Marathon de Mesos, Fleet, Docker Datacenter/Docker Swarm, Kubernetes y Service Fabric son ejemplos de orquestadores (o los tienen integrados). Continuamente se crean nuevos orquestadores, a medida que crece y cambia la complejidad de las formas de administrar las implementaciones reales en diferentes tipos de entornos y condiciones.

## <a name="orchestration-as-a-service"></a>La organización como servicio
El trabajo del organizador en un clúster de Service Fabric está controlado principalmente por Cluster Resource Manager. Service Fabric Cluster Resource Manager es un servicio del sistema en Service Fabric y se inicia automáticamente dentro de cada clúster. Por lo general, el trabajo de Cluster Resource Manager se divide en tres partes:

1. Aplicar reglas
2. Optimizar el entorno
3. Ayudar con otros procesos

Para ver cómo funciona Cluster Resource Manager, vea el siguiente vídeo de Microsoft Virtual Academy: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Qué no es
En las aplicaciones tradicionales de nivel N siempre hubo alguna noción de "equilibrador de carga". Habitualmente, se trataba de un equilibrador de carga de red (NLB) o un equilibrador de carga de aplicación (ALB), en función del punto de la pila de red en que se encontrara. Algunos equilibradores de carga se basan en componentes de hardware, como la oferta de BigIP de F5, y otros en componentes de software, como NLB de Microsoft. En otros entornos podría ver algo parecido a HAProxy o nginx en este rol. En estas arquitecturas, el trabajo de equilibrio de carga es garantizar que las cargas de trabajo sin estado reciban prácticamente la misma cantidad de trabajo. Las estrategias de equilibro de carga variaban. Algunos equilibradores enviaban cada llamada distinta a un servidor diferente. Otros proporcionaban asignación o afinidad de la sesión. Equilibradores más avanzados usan la estimación real o informes para enrutar una llamada en función del costo esperado y de la carga de la máquina actual.

Los equilibradores de carga o enrutadores de mensajes intentaban garantizar que el nivel web o de trabajo se mantenían prácticamente equilibrados. Las estrategias para equilibrar el nivel de datos eran distintas y dependían del mecanismo de almacenamiento de los datos. Normalmente, se centraban en el particionamiento de los datos, el almacenamiento en caché, las vistas administradas, los procedimientos almacenados y otros mecanismos específicos del almacén.

Aunque algunas de estas estrategias son interesantes, Service Fabric Cluster Resource Manager no se parece en nada a un equilibrador de carga de red ni a una memoria caché. Un equilibrador de carga de red garantiza que los front-end están equilibrados; para ello, mueve el tráfico hacia donde se ejecutan los servicios. Cluster Resource Manager de Service Fabric tiene una estrategia diferente. Básicamente, Service Fabric mueve los *servicios* al lugar donde resulten más útiles, y se espera que siga un tráfico o una carga. Por ejemplo, podría mover los servicios a nodos actualmente inactivos, porque los servicios existentes no hacen mucho trabajo. Los nodos podrían estar inactivos dado que se eliminaron o movieron a otro lugar los servicios que existían. Otro ejemplo es que Cluster Resource Manager también podría mover un servicio lejos de una máquina. Quizás la máquina está a punto de actualizarse o está sobrecargada debido a un aumento en el consumo por parte de los servicios que se ejecutan en ella.

Dado que Cluster Resource Manager es responsable de mover servicios de un lado a otro (y no de entregar el tráfico de red a los lugares donde ya hay servicios), contiene un conjunto de características distintas en comparación con las que se encontrarían en un equilibrador de carga de red. Si observamos con más detalle, veremos que usa estrategias fundamentalmente distintas para garantizar que los recursos de hardware del clúster se usen de manera eficaz.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre el flujo de información y la arquitectura dentro de Cluster Resource Manager, consulte [este artículo](service-fabric-cluster-resource-manager-architecture.md)
* Cluster Resource Manager tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo sobre cómo [describir un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)
* Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para obtener más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md)
* El Administrador de recursos de clúster trabaja con las funcionalidades de administración de Service Fabric. Para más información sobre esa integración, lea [este artículo](service-fabric-cluster-resource-manager-management-integration.md)
* Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)



<!--HONumber=Jan17_HO1-->


