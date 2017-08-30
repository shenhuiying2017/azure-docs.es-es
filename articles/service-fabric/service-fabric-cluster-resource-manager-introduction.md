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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 3e8cd4dc8e960e38ba0e4a9a195b2f61d9ec1924
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Presentación de Cluster Resource Manager de Service Fabric
Tradicionalmente, administrar sistemas de TI o servicios en línea significaba dedicar máquinas físicas o virtuales específicas a esos servicios o sistemas específicos. Los servicios se diseñaban como capas. Podía haber una capa "web" y una capa "datos" o "almacenamiento". Las aplicaciones tenían un nivel de mensajería donde había solicitudes de flujo de entrada y salida, así como un conjunto de máquinas dedicado al almacenamiento en caché. Cada nivel o tipo de carga de trabajo tenía máquinas específicas dedicadas a ello: la base de datos contaba con un par de máquinas dedicadas, y los servidores web tenían algunas más. Si un tipo determinado de carga de trabajo provocaba que las máquinas en las que estaba se sobrecalentaran, se agregaban más máquinas con esa misma configuración a esa capa. Sin embargo, no todas las cargas de trabajo se podían escalar horizontalmente tan fácilmente, especialmente con la capa de datos tenía normalmente que reemplazar las máquinas con máquinas de mayor tamaño. Así de sencillo. Si se producía un error en una máquina, la parte afectada de la aplicación general se ejecutaba con una capacidad inferior hasta que pudiera restaurarse la máquina. Esto sigue siendo sencillo (aunque no necesariamente divertido).

Sin embargo, ahora el sector de la arquitectura de software y servicio ha cambiado. Es más habitual que las aplicaciones hayan adoptado un diseño de escalado horizontal. Es habitual la compilación de aplicaciones con contenedores o microservicios (o ambos). Ahora, aunque todavía puede tener solo unas pocas máquinas, no se ejecutan como una única instancia de una carga de trabajo. Pueden incluso ejecutarse varias cargas de trabajo diferentes al mismo tiempo. Ahora tiene docenas de tipos de servicios distintos (ninguno de los cuales consume los recursos de toda una máquina), quizás cientos de instancias distintas de esos servicios. Cada instancia con nombre tendría una o más instancias o réplicas para alta disponibilidad (HA). Dependiendo de los tamaños de las cargas de trabajo y su nivel de ocupación, es probable que se encuentre con cientos o miles de máquinas. 

De repente, administrar su entorno ya no es tan sencillo como administrar unas cuantas máquinas dedicadas a un solo tipo de carga de trabajo. Sus servidores son virtuales y ya no tienen nombres (es decir, ha pasado de la mentalidad [de mascota a ganado](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)). La configuración está menos orientada a las máquinas y se orienta directamente a los servicios. El hardware que se dedica a una única instancia de una carga de trabajo es en gran medida algo que forma parte del pasado. Los propios servicios se han convertido en pequeños sistemas distribuidos que abarcan múltiples piezas pequeñas de hardware estándar.

Puesto que la aplicación ya no es una serie de monolitos repartidos en varias capas, ahora tiene muchas más combinaciones con las que tratar. ¿Quién decide qué tipos de cargas de trabajo pueden ejecutarse en cada hardware o cuántas? ¿Qué cargas de trabajo funcionan bien en el mismo hardware y cuáles entran en conflicto? Cuando una máquina deja de funcionar, ¿cómo sabe lo que se estaba ejecutando en esa máquina? ¿Quién es el responsable de asegurarse de que esa carga de trabajo empiece a ejecutarse de nuevo? ¿Debe esperar hasta que la máquina (virtual) regrese o sus cargas de trabajo conmutarán por error automáticamente a otras máquinas y seguirán ejecutándose? ¿Se requiere la intervención humana? ¿Qué pasa con las actualizaciones en este entorno?

Como desarrolladores y operadores que tienen que trabajar en este entorno, querremos obtener ayuda para administrar esta complejidad. Empezar a contratar mano de obra para ocultar la complejidad del proceso no es probablemente la mejor solución. Así que, ¿qué hacemos?

## <a name="introducing-orchestrators"></a>Presentación de los organizadores
"Orquestador" es el término general que se usa para los componentes de software que ayudan a los administradores a gestionar estos tipos de entornos. Los orquestadores son los componentes que reciben solicitudes como "Quiero que se ejecuten cinco copias de este servicio en mi entorno". Tratan de que el entorno coincida con el estado deseado, sin importar lo que suceda.

Los orquestadores (y no el personal humano) son los que entran en acción cuando una máquina falla o cuando una carga de trabajo finaliza por algún motivo inesperado. La mayoría de los orquestadores no solo administran los errores. Otras características que tienen es administrar implementaciones nuevas, controlar las actualizaciones y administrar el consumo de recursos y gobierno. Todos los orquestadores se dedican fundamentalmente a mantener el estado de configuración deseado en el entorno. Sabemos que lo que busca es poder indicarle a un orquestador qué quiere y dejar que haga el trabajo pesado. Aurora de Mesos, Docker Datacenter/Docker Swarm, Kubernetes y Service Fabric son ejemplos de orquestadores. Estos orquestadores están desarrollándose activamente para satisfacer las necesidades de cargas de trabajo reales en entornos de producción. 

## <a name="orchestration-as-a-service"></a>La organización como servicio
Cluster Resource Manager es el componente del sistema que controla la orquestación de Service Fabric. El trabajo de Cluster Resource Manager se divide en tres partes:

1. Aplicar reglas
2. Optimizar el entorno
3. Ayudar con otros procesos

Para ver cómo funciona Cluster Resource Manager, vea el siguiente vídeo de Microsoft Virtual Academy: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Qué no es
En las aplicaciones tradicionales de nivel N siempre hubo un [equilibrador de carga](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Habitualmente, se trataba de un equilibrador de carga de red (NLB) o un equilibrador de carga de aplicación (ALB), en función del punto de la pila de red en que se encontrara. Algunos equilibradores de carga se basan en componentes de hardware, como la oferta de BigIP de F5, y otros en componentes de software, como NLB de Microsoft. En otros entornos podría ver algo parecido a HAProxy, nginx, Istio o Envoy en este rol. En estas arquitecturas, el trabajo de equilibrio de carga es garantizar que las cargas de trabajo sin estado reciban prácticamente la misma cantidad de trabajo. Las estrategias de equilibro de carga variaban. Algunos equilibradores enviaban cada llamada distinta a un servidor diferente. Otros proporcionaban asignación o afinidad de la sesión. Equilibradores más avanzados usan la estimación de carga real o informes para enrutar una llamada en función del costo esperado y de la carga de la máquina actual.

Los equilibradores de carga o enrutadores de mensajes intentaban garantizar que el nivel web o de trabajo se mantenían prácticamente equilibrados. Las estrategias para el equilibrio de la capa de datos eran diferentes y dependientes en el mecanismo de almacenamiento de datos. El equilibrio de la capa de datos se basaba en el particionamiento de datos, el almacenamiento en caché, las vistas administradas, los procedimientos almacenados y otros mecanismos específicos del almacén.

Aunque algunas de estas estrategias son interesantes, Service Fabric Cluster Resource Manager no se parece en nada a un equilibrador de carga de red ni a una memoria caché. Un equilibrador de carga de red equilibra front-ends mediante la distribución del tráfico a través de front-ends. Cluster Resource Manager de Service Fabric tiene una estrategia diferente. Básicamente, Service Fabric mueve los *servicios* al lugar donde resulten más útiles, y se espera que siga un tráfico o una carga. Por ejemplo, podría mover los servicios a nodos actualmente inactivos, porque los servicios existentes no hacen mucho trabajo. Los nodos podrían estar inactivos dado que se eliminaron o movieron a otro lugar los servicios que existían. Otro ejemplo es que Cluster Resource Manager también podría mover un servicio lejos de una máquina. Quizás la máquina está a punto de actualizarse o está sobrecargada debido a un aumento en el consumo por parte de los servicios que se ejecutan en ella. De manera alternativa, los requisitos de recursos del servicio pueden haber aumentado. Como resultado, no hay suficientes recursos en este equipo para continuar ejecutándolo. 

Dado que Cluster Resource Manager es responsable de mover servicios de un lado a otro, contiene un conjunto de características distintas en comparación con las que se encontrarían en un equilibrador de carga de red. Esto es porque los equilibradores de carga de red entregan el tráfico de red adonde ya están los servicios, incluso si esa ubicación no es ideal para la ejecución del propio servicio. Cluster Resource Manager de Service Fabric usa estrategias fundamentalmente distintas para garantizar que los recursos del clúster se usen de manera eficaz.

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre el flujo de información y la arquitectura dentro de Cluster Resource Manager, consulte [este artículo](service-fabric-cluster-resource-manager-architecture.md)
- Cluster Resource Manager tiene muchas opciones para describir el clúster. Para obtener más información sobre las métricas, vea este artículo acerca de la [descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).
- Para más información acerca de cómo configurar servicios, [lea sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Las métricas son el modo en que Cluster Resource Manager de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- Cluster Resource Manager trabaja con las funcionalidades de administración de Service Fabric. Para más información sobre esa integración, lea [este artículo](service-fabric-cluster-resource-manager-management-integration.md)
- Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)

