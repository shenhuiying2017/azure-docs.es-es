<properties
   pageTitle="Presentación del Administrador de recursos de clúster de Service Fabric | Microsoft Azure"
   description="Una introducción a Service Fabric Cluster Resource Manager."
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
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Presentación del Administrador de recursos de clúster de Service Fabric
Tradicionalmente, administrar sistemas de TI o un conjunto de servicios significaba tener algunas máquinas físicas o virtuales dedicadas a esos servicios o sistemas específicos. Muchos servicios principales se dividían en un nivel "web" y un nivel de "datos" o "almacenamiento", quizá con otros componentes especializados, como una caché. Otros tipos de aplicaciones tenían un nivel de mensajería donde entraban y salían las solicitudes, conectado a un nivel de trabajo para los análisis o las transformaciones que fueran necesarios como parte de la mensajería. Cada tipo de carga de trabajo tenía máquinas específicas dedicadas a ello: la base de datos contaba con un par de máquinas dedicadas, y los servidores web tenían unas cuantas más. Si un tipo determinado de carga de trabajo provocaba que las máquinas en las que estaba se sobrecalentaran, se agregaban más máquinas configuradas para ejecutar en ellas ese tipo de carga de trabajo, o bien se reemplazaban algunas de las máquinas por otras que fueran más grandes. Así de sencillo. Si se producía un error en una máquina, la parte afectada de la aplicación general se ejecutaba con una capacidad inferior hasta que pudiera restaurarse la máquina. Esto sigue siendo sencillo (aunque no necesariamente divertido).

Ahora bien, supongamos que tuviera la necesidad de escalar horizontalmente y usara la opción de contenedores y/o microservicios. Inesperadamente, se encontraría con decenas, cientos o incluso miles de máquinas, docenas de tipos distintos de servicios y puede que cientos de instancias diferentes de esos servicios, cada uno con una o más instancias o réplicas para conseguir una alta disponibilidad.

De repente, administrar su entorno ya no es tan sencillo como administrar unas cuantas máquinas dedicadas a un solo tipo de carga de trabajo. Sus servidores son virtuales y ya no tienen nombres (es decir, *ha* pasado de la mentalidad [de mascota a ganado](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)). La configuración está menos orientada a las máquinas y se orienta directamente a los servicios. El hardware dedicado es cosa del pasado y los servicios se han convertido en pequeños sistemas distribuidos, que abarcan múltiples piezas pequeñas de hardware estándar.

Como consecuencia de la división de su aplicación (de la anterior división por bloques monolíticos a la división en servicios independientes que se ejecutan en hardware estándar), es posible que ahora tenga que ocuparse de muchas más combinaciones. ¿Quién decide qué tipos de cargas de trabajo pueden ejecutarse en cada hardware o cuántas? ¿Qué cargas de trabajo funcionan bien en el mismo hardware y cuáles entran en conflicto? Cuando una máquina deja de funcionar... ¿cómo podemos saber qué se estaba ejecutando ahí? ¿Quién es el responsable de asegurarse de que esa carga de trabajo empiece a ejecutarse de nuevo? ¿Debe esperar hasta que la máquina (virtual) regrese o sus cargas de trabajo conmutarán por error automáticamente a otras máquinas y seguirán ejecutándose? ¿Se requiere la intervención humana? ¿Cómo se realizan las actualizaciones en este tipo de entorno?

Como desarrolladores y operadores en este tipo de entorno, vamos a necesitar algo de ayuda en este entorno tan complejo, y seguro que tiene la sensación de que empezar a contratar mano de obra a destajo para administrar la complejidad del proceso no es la mejor solución.

¿Qué debe hacer?

## Presentación de los organizadores
"Orquestrador" es el término general que se usa para los componentes de software que ayudan a los administradores a gestionar estos tipos de entornos. Los orquestadores son los componentes que reciben solicitudes como "Quiero que se ejecuten cinco copias de este servicio en mi entorno", responden a ellas para cumplirlas y además intentan que se mantengan vigentes.

Los orquestadores (y no el personal humano) son los que entran en acción cuando una máquina falla o cuando una carga de trabajo finaliza por algún motivo inesperado. La mayoría de orquestadores no solo administran los errores, sino que también ayudan en las nuevas implementaciones, llevan a cabo las actualizaciones y administran el consumo de recursos. No obstante, fundamentalmente se dedican a mantener el estado de configuración deseado en el entorno. Sabemos que lo que busca es poder indicarle a un orquestador qué quiere y dejar que haga el trabajo pesado. Chronos o Marathon de Mesos, Fleet, Swarm, Kubernetes y Service Fabric son ejemplos de orquestadores (o los tienen integrados). Continuamente se crean nuevos orquestadores, a medida que crece y cambia la complejidad de las formas de administrar las implementaciones reales en diferentes tipos de entornos y condiciones.

## La organización como servicio
El trabajo del organizador en un clúster de Service Fabric está controlado principalmente por Cluster Resource Manager. Service Fabric Cluster Resource Manager es un servicio del sistema en Service Fabric y se inicia automáticamente dentro de cada clúster. Por lo general, el trabajo de Cluster Resource Manager se divide en tres partes:

1. Aplicar reglas
2. Optimizar el entorno
3. Proporcionar asistencia en otros procesos

### Qué no es
En aplicaciones web tradicionales de nivel N, siempre había alguna noción de un "equilibrador de carga", al que normalmente se hacía referencia como "equilibrador de carga de red" (NLB) o "equilibrador de carga de aplicación" (ALB), en función del punto de la pila de red en que se encontrara. Algunos equilibradores de carga se basan en componentes de hardware, como la oferta de BigIP de F5, y otros en componentes de software, como NLB de Microsoft. En otros entornos podría ver algo parecido a HAProxy en este rol. En estas arquitecturas, el trabajo del equilibrador de carga es asegurarse de que todas las máquinas front-end sin estado o las máquinas del clúster reciban (aproximadamente) la misma cantidad de trabajo. Las estrategias para conseguirlo podían variar: desde el envío de cada llamada a un servidor diferente, hasta la asignación o afinidad de la sesión, o bien la estimación real y la asignación de llamadas en función del costo esperado y de la carga actual de la máquina.

Tenga en cuenta que este era el mejor mecanismo para asegurarse de que el nivel web se mantenía más o menos equilibrado. Las estrategias para equilibrar el nivel de datos eran completamente diferentes y dependían del mecanismo de almacenamiento de los datos. Normalmente, se centraban en el particionamiento de los datos, el almacenamiento en caché, las vistas administradas de la base de datos y los procedimientos almacenados, etc.

Aunque algunas de estas estrategias son interesantes, Service Fabric Cluster Resource Manager no se parece en nada a un equilibrador de carga de red ni a una memoria caché. Mientras un equilibrador de carga de red garantiza que los front-end estén equilibrados moviendo el tráfico al lugar donde se están ejecutando los servicios, Service Fabric Cluster Resource Manager adopta una estrategia diferente. Básicamente, Service Fabric mueve los *servicios* al lugar más lógico (y espera que el tráfico o la carga les siga). Por ejemplo, el lugar pueden ser los nodos que actualmente están fríos porque los servicios que se encuentran en ellos no están trabajando mucho en ese momento, o se han eliminado o movido a otro sitio. Otro ejemplo sería el caso en el que Cluster Resource Manager mueve un servicio para sacarlo de una máquina que se vaya a actualizar o que esté sobrecargada debido a un pico en el consumo de los servicios que en ella se ejecutan. Dado que Cluster Resource Manager es responsable de mover servicios de un lado a otro (no de entregar el tráfico de red en donde ya están ubicados los servicios), contiene un conjunto de características muy diferentes en comparación con las que se encuentran en un equilibrador de carga de red, y emplea fundamentalmente distintas estrategias para asegurarse de que los recursos de hardware del clúster se usan bien.

## Pasos siguientes
- Para más información sobre el flujo de información y la arquitectura dentro de Cluster Resource Manager, consulte [este artículo](service-fabric-cluster-resource-manager-architecture.md)
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md).
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para obtener más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- El Administrador de recursos de clúster trabaja con las funcionalidades de administración de Service Fabric. Para más información sobre esa integración, lea [este artículo](service-fabric-cluster-resource-manager-management-integration.md)
- Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0824_2016-->