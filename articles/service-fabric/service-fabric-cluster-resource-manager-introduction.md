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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Presentación del Administrador de recursos de clúster de Service Fabric
Tradicionalmente, administrar sistemas de TI o un conjunto de servicios significaba tener algunas máquinas dedicadas a esos servicios o sistemas específicos. Muchos servicios principales se dividían en un nivel "web" y un nivel de "datos" o "almacenamiento", quizá con otros componentes especializados, como una caché. Otros tipos de aplicaciones tenían un nivel de mensajería donde entraban y salían las solicitudes, conectado a un nivel de trabajo para los análisis o las transformaciones que fueran necesarios como parte de la mensajería. Cada parte tenía una máquina específicas dedicada a ello (o incluso más de una): la base de datos contaba con un par de máquinas dedicadas, y los servidores web tenían unas cuantas más. Si un tipo determinado de carga de trabajo provocaba que las máquinas en las que estaba se sobrecalentaran, se agregaban más máquinas configuradas para ese tipo de carga de trabajo para que pudieran ejecutarla, o bien se reemplazaban algunas de las máquinas por otras que fueran más grandes. Así de sencillo. Si se producía un error en una máquina, la parte afectada de la aplicación general se ejecutaba con una capacidad inferior hasta que pudiera restaurarse la máquina. Esto sigue siendo sencillo (aunque no necesariamente divertido).

Ahora bien, supongamos que tuviera la necesidad de escalar horizontalmente y usara la opción de contenedores y/o microservicios. Inesperadamente, se encontraría con cientos o incluso miles de máquinas, decenas de tipos distintos de servicios y puede que cientos de instancias diferentes de esos servicios, cada uno con una o más instancias o réplicas para conseguir una alta disponibilidad.

De repente, administrar su entorno ya no es tan sencillo como administrar unas cuantas máquinas dedicadas a un solo tipo de carga de trabajo. Sus servidores son virtuales y ya no tienen nombres (es decir, ha pasado de la mentalidad de [mascota a ganado](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)), y las máquinas no están dedicadas a un solo tipo de cargas de trabajo. La configuración está menos orientada a las máquinas y se orienta directamente a los servicios.

Como consecuencia de este desacoplamiento y de la división de su aplicación (de la anterior división por bloques monolíticos a la división en servicios independientes), es posible que ahora tenga que gestionar muchas más combinaciones. ¿Quién decide qué tipos de cargas de trabajo pueden ejecutarse en un hardware específico o de forma paralela? Cuando una máquina deja de funcionar... ¿cómo podemos saber qué se estaba ejecutando ahí? ¿Quién es el responsable de asegurarse de que empiece a ejecutarse ese servicio de nuevo? ¿Debe esperar hasta que la máquina (virtual) regrese o sus cargas de trabajo seguirán ejecutándose? ¿Se requiere intervención humana para que los procesos vuelvan a funcionar? ¿Y cómo se realizan las actualizaciones en este tipo de entorno? Vamos a necesitar algo de ayuda, y seguro que tiene la sensación de que empezar a contratar mano de obra para gestionar la complejidad del proceso no es la mejor solución.

¿Qué debe hacer?

## Presentación de los organizadores
"Organizador" es el término general que se usa para los componentes de software que ayudan a los administradores a gestionar estos tipos de implementaciones. Los organizadores son los componentes que reciben solicitudes como "Quiero que se ejecuten cinco copias de este servicio en mi entorno", responden a ellas para cumplirlas e (intentan) que se mantengan vigentes. Los organizadores (y no el personal humano) son los que entran en acción cuando una máquina falla o cuando una carga de trabajo finaliza por algún motivo inesperado. La mayoría de organizadores no solo gestionan los errores, sino que también ayudan en las nuevas implementaciones, llevan a cabo las actualizaciones y administran el consumo de recursos. No obstante, fundamentalmente se dedican a mantener el estado de configuración deseado en el entorno. Sabemos que lo que busca es poder indicarle a un organizador qué quiere y dejar que haga el trabajo pesado. El componente Chronos de Mesosphere, Fleet, Swarm, Kubernetes y Service Fabric son organizadores o los tiene integrados. Continuamente se crean nuevos organizadores, a medida que crece y cambia la complejidad de las formas de administrar las implementaciones reales en diferentes tipos de entornos y condiciones.

## La organización como servicio
El trabajo del organizador en un clúster de Service Fabric está controlado principalmente por Cluster Resource Manager. Service Fabric Cluster Resource Manager es un servicio del sistema en Service Fabric y se inicia automáticamente dentro de cada clúster. Por lo general, el trabajo de Resource Manager se divide en tres partes:

1. Aplicar reglas
2. Optimizar el entorno
3. Proporcionar asistencia en otros procesos

Antes de examinar todas las funcionalidades que tiene Resource Manager, veamos rápidamente cómo funciona. Service Fabric Cluster Resource Manager

## Arquitectura general e implementación
Antes de adentrarnos en la descripción de Cluster Resource Manager y en todas sus características, primero vamos a ver qué es y cómo funciona.

### Qué no es
En aplicaciones web tradicionales de nivel N, siempre había alguna noción de un "equilibrador de carga", al que normalmente se hacía referencia como "equilibrador de carga de red" (NLB) o "equilibrador de carga de aplicación" (ALB), en función del punto de la pila de red en que se encontrara. Algunos equilibradores de carga se basan en componentes de hardware, como la oferta de BigIP de F5, y otros en componentes de software, como NLB de Microsoft. En estas arquitecturas, el trabajo del equilibrador de carga es asegurarse de que todas las máquinas front-end sin estado o las máquinas del clúster reciban (aproximadamente) la misma cantidad de trabajo. Las estrategias para conseguirlo podían variar: desde el envío de cada llamada a un servidor diferente, hasta la asignación o afinidad de la sesión, o bien la estimación real y la asignación de llamadas en función del costo esperado y de la carga actual de la máquina.

Tenga en cuenta que este era el mejor mecanismo para asegurarse de que el nivel web se mantenía más o menos equilibrado. Las estrategias para equilibrar el nivel de datos eran completamente diferentes y dependían del mecanismo de almacenamiento de los datos. Normalmente, se centraban en el particionamiento de los datos, el almacenamiento en caché, las vistas administradas de la base de datos y los procedimientos almacenados, etc.

Aunque algunas de estas estrategias son interesantes, Service Fabric Cluster Resource Manager no se parece en nada a un equilibrador de carga de red ni a una memoria caché. Mientras un equilibrador de carga garantiza que los front-end estén equilibrados moviendo el tráfico al lugar donde se están ejecutando los servicios, Service Fabric Resource Manager adopta un enfoque diferente. Básicamente, Service Fabric mueve los servicios al lugar donde resulten más útiles. Por ejemplo, los nodos que actualmente están fríos porque los servicios que se encuentran ahí no están en ejecución en ese momento. También podría separarlos de un nodo que se vaya a actualizar o que esté sobrecargado debido a un pico en el consumo de los servicios que se ejecutan en él. Dado que es responsable de desplazar los servicios (no de enviar tráfico de red al lugar en el que ya se encuentran dichos servicios), Service Fabric Resource Manager es más versátil y también contiene funcionalidades adicionales para controlar dónde y cómo se mueven los servicios.

## Pasos siguientes
- Para más información sobre el flujo de información y la arquitectura dentro del Administrador de recursos de clúster, consulte [este artículo ](service-fabric-cluster-resource-manager-architecture.md).
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).
- Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones del Administrador de recursos de clúster disponibles. [Más información sobre cómo configurar los servicios](service-fabric-cluster-resource-manager-configure-services.md).
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- El Administrador de recursos de clúster trabaja con las funcionalidades de administración de Service Fabric. Para más información sobre esa integración, lea [este artículo](service-fabric-cluster-resource-manager-management-integration.md).
- Para más información sobre cómo el Administrador de recursos de clúster administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).

<!---HONumber=AcomDC_0316_2016-->