<properties
   pageTitle="Patrones y antipatrones de Reliable Actors | Microsoft Azure"
   description="Proporciona información general sobre el modelo de programación de actor, patrones de diseño que funcionan correctamente con Reliable Actors de Service Fabric y algunos antipatrones que se deben evitar."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Introducción a los patrones de diseño de Actores confiables

El modelo de programación de Reliable Actors de Azure Service Fabric es una plataforma basada en el modelo de actor para solucionar problemas del mundo real a escala de la nube. Service Fabric es una plataforma para crear aplicaciones escalables de alta confiabilidad que son fáciles de desarrollar y administrar tanto en la nube como en entornos locales.

La intención de este artículo es tratar problemas concretos de forma práctica. Después de leer sobre los diversos patrones, debería saber cómo usar el modelo de Reliable Actors para crear soluciones empresariales y en la nube.

## Patrones

En esta sección, se enumera un conjunto de patrones y escenarios asociados que establecimos en nuestros compromisos con los clientes. Estos patrones representan clases de problemas que se pueden aplicar a una amplia gama de soluciones que nuestros clientes crean en Microsoft Azure. Aunque los escenarios se basan en casos reales, hemos eliminado la mayoría de los problemas específicos de dominio para que los patrones sean más claros. Verá que gran parte del código de ejemplo es simple u obvio. Hemos incluido ese código para que el ejemplo sea completo y no porque sea algo especialmente ingenioso o impresionante.

Los patrones presentados aquí no pretenden ser globales o canónicos. Algunos desarrolladores podrían solucionar los mismos problemas o patrones de formas distintas a las que se presentan.

[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

### Actores, una breve historia

El [documento](http://dl.acm.org/citation.cfm?id=1624804) de Carl Hewitt y sus coautores que originó el modelo de actor se publicó en 1973. Pero no ha sido hasta hace poco cuando el modelo de actor ha despertado interés como un recurso para abordar la simultaneidad y complejidad en los sistemas distribuidos.

El modelo de actor admite objetos individuales detallados (actores) que están aislados entre sí. Se comunican mediante el intercambio de mensajes asincrónicos, lo que permite la comunicación directa entre actores. Un actor se ejecuta con la semántica de subproceso único. Junto con la encapsulación del estado del actor y el aislamiento de otros actores, este enfoque simplifica la escritura de sistemas altamente paralelos. Lo hace mediante la eliminación de problemas de simultaneidad de un código de actor. Los actores se crean de forma dinámica en el grupo de recursos de hardware disponibles.

[Erlang](http://www.erlang.org/) es la implementación más conocida del modelo de actor. Los desarrolladores empezaron a redescubrir el modelo de actor, que fomentó un interés renovado en Erlang y en la creación de nuevas soluciones similares a Erlang, como actores [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.NET](http://getakka.net/) y [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Beneficios de Reliable Actors de Service Fabric

El modelo de programación de Reliable Actors es una implementación del modelo de actor que toma prestadas algunas ideas de Erlang y de sistemas de objetos distribuidos. Después agrega una capa de direccionamiento indirecto de actor y expone los actores en un modelo de programación integrado que usa la plataforma de Service Fabric.

Las ventajas principales de Reliable Actors son la **productividad del desarrollador**, incluso para los programadores inexpertos; y la **escalabilidad transparente de forma predeterminada** sin ningún esfuerzo especial del programador. El modelo de programación de Reliable Actors usa una biblioteca .NET que se ejecuta sobre Service Fabric. Proporciona herramientas que facilitan mucho el desarrollo de aplicaciones distribuidas complejas. Estas herramientas también permiten que las aplicaciones resultantes sean escalables por diseño. Se ofrece más información sobre cada una de estas ventajas a continuación. El modelo de programación aumenta la productividad de los programadores expertos e inexpertos, al ofrecer las siguientes abstracciones clave, garantías y servicios del sistema:

* *Paradigma de programación orientada a objetos (OOP) familiar*. Los actores son clases de .NET que implementan interfaces de actor de .NET declaradas con propiedades y métodos asincrónicos. Por lo tanto, los actores aparecen ante el programador como objetos remotos cuyos métodos y propiedades se pueden invocar directamente. Esto ofrece a los programadores el paradigma de programación orientada a objetos familiar al convertir las llamadas a métodos en mensajes, enrutarlos a los puntos de conexión correctos, invocar los métodos del actor de destino y tratar los errores y los casos excepcionales de una forma completamente transparente.

* *Ejecución de subproceso único de actores*. El tiempo de ejecución de Reliable Actors garantiza que un actor nunca se ejecuta en más de un subproceso a la vez. Debido a esto y al aislamiento de cada actor de otros actores, los programadores nunca se enfrentan a la simultaneidad al nivel de actor. Los programadores nunca necesitan usar bloqueos u otros mecanismos de sincronización para controlar el acceso a los datos compartidos. Esta característica por sí sola permite que los desarrolladores inexpertos puedan controlar el desarrollo de aplicaciones distribuidas.

* *Activación transparente.* El tiempo de ejecución activa un actor según sea necesario, solo cuando hay un mensaje que debe procesar. Esto separa claramente la noción de creación lógica de un actor, que es visible para el código de la aplicación que la controla, de la activación física del actor en memoria, que es transparente para la aplicación. El enfoque de Reliable Actors es similar a la memoria virtual en cuanto a que decide cuándo "quitar" (desactivar) o "agregar" (activar) un actor. La aplicación tiene acceso ininterrumpido a todo el "espacio de memoria" de actores creados lógicamente, independientemente de que estén o no en la memoria física en un momento dado. La activación transparente habilita el equilibrio de cargas adaptativo y dinámico mediante la ubicación y la migración de los actores en el grupo de recursos de hardware.

* *Transparencia de ubicación*. Una referencia de actor (objeto proxy) que el programador usa para invocar los métodos de actor o para pasarlos a otros componentes solo contiene la identidad lógica del actor. El tiempo de ejecución de Reliable Actors realiza de manera transparente la traducción de la identidad lógica del actor a su ubicación física y el correspondiente enrutamiento de mensajes. El código de la aplicación se comunica con actores que desconocen su ubicación física, que puede cambiar con el tiempo debido a errores o a la administración de recursos. También puede cambiar la ubicación de un actor porque el actor está desactivado en el momento en que se llama.

* *Integración transparente con el almacén persistente*. El modelo de programación de Reliable Actors permite la asignación declarativa del estado en memoria de los actores al almacén persistente. Sincroniza las actualizaciones y garantiza de forma transparente que los llamadores reciben resultados solo después de que el estado persistente se haya actualizado correctamente.

* *Alta disponibilidad, compatibilidad con la conmutación por error y administración del ciclo de vida de las aplicaciones*. La plataforma administra el estado de los actores y se replica de manera que se pueda restaurar si, por ejemplo, se produce un error en un nodo del clúster. Service Fabric también administra el ciclo de vida de las aplicaciones y permite las actualizaciones de las aplicaciones sin tiempo de inactividad. El modelo de programación Reliable Actors está diseñado para guiar a los programadores por el camino correcto en el escalado de sus aplicaciones y servicios a través de varios órdenes de magnitud. Esto se consigue con la incorporación de procedimientos y patrones recomendados y con una implementación eficaz de funcionalidad de sistema de menor nivel. A continuación, se muestran algunos de los factores clave que permiten la escalabilidad y el rendimiento de las aplicaciones de Service Fabric:

  * *Partición implícita específica del estado de la aplicación*. Mediante el uso de los actores como entidades directamente direccionables, los programadores dividen implícitamente el estado general de sus aplicaciones. Aunque el modelo de programación de Reliable Actors no restringe lo grande o pequeño que debe ser un actor, en la mayoría de los casos, tiene sentido disponer de un número relativamente grande de actores (millones o más). Cada uno de estos millones de actores representa una entidad natural de la aplicación, como una cuenta de usuario o un pedido de compra. Dado que los actores son direccionables de forma individual y sus ubicaciones físicas se abstraen mediante el tiempo de ejecución, hay una gran flexibilidad para equilibrar la carga y trabajar con zonas activas. Esto se realiza de forma transparente y genérica sin pensar en los desarrolladores de aplicaciones.

  * *Administración de recursos adaptativa*. Los actores no hacen ninguna suposición sobre la localidad de los otros actores con los que interactúan. Debido a la transparencia de la ubicación, el tiempo de ejecución puede administrar y ajustar la asignación de recursos de hardware disponibles de una manera muy dinámica. Para ello, toma decisiones específicas sobre la ubicación y la migración de actores en el clúster de proceso como reacción a los patrones de comunicación y carga, sin que las solicitudes entrantes generen errores. Mediante la creación de varias réplicas de un actor determinado, el tiempo de ejecución puede aumentar el rendimiento del actor, sin realizar cambios en el código de la aplicación.

  * *Comunicación multiplexada*. Los actores en Service Fabric tienen extremos lógicos y la mensajería entre ellos se multiplexa a través de un conjunto fijo de conexiones físicas de tipo de todos a todos (sockets TCP). Esto permite que el tiempo de ejecución hospede un gran número (millones) de entidades direccionables sin sobrecarga del sistema operativo por actor. Además, la activación o desactivación de un actor no incurre en el costo de registrar o anular el registro de un punto de conexión físico, como un puerto TCP o una dirección URL HTTP.

  * *Programación eficaz*. El tiempo de ejecución de Reliable Actors programa la ejecución de un gran número de actores de subproceso único en un grupo de subprocesos personalizado con un subproceso por cada núcleo de procesador físico. El código de actor está escrito en el estilo basado en la continuación sin bloqueo (un requisito del modelo de programación), por lo que el código de la aplicación se ejecuta de una manera multiproceso “cooperativa” muy eficaz, sin contención. Esto permite que el sistema consiga un alto rendimiento y se ejecute con un uso de CPU muy elevado (con un 90 % o más) con gran estabilidad. Un aumento del número de actores en el sistema y la carga no da lugar a subprocesos adicionales u otras primitivas de sistema operativo, lo que favorece la escalabilidad de los nodos individuales y de todo el sistema.

  * *Asincronía explícita*. El modelo de programación de Reliable Actors hace que la naturaleza asincrónica de una aplicación distribuida sea explícita y ayuda a los programadores a escribir código asincrónico sin bloqueo. Esto permite un alto grado de paralelismo distribuido y de rendimiento general sin el uso explícito de multithreading.

<!---HONumber=AcomDC_0121_2016-->