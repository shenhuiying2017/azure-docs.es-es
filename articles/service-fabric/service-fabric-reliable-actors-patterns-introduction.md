<properties
   pageTitle="Introducción a los patrones y antipatrones de los actores de Service Fabric de Azure"
   description="patrones de diseño que funcionan bien con los actores de Service Fabric"
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
El modelo de programación Actores confiables de Service Fabric es una plataforma que se basa en el modelo de actor para solucionar problemas del mundo real en la escala de la nube. Service Fabric de Azure es una plataforma para crear aplicaciones escalables altamente confiables de forma local y en la nube que son fáciles de desarrollar y administrar. Este artículo pretende ser un artículo práctico sobre problemas prácticos. Después de leer sobre los diversos patrones, debe entender cómo puede usar el modelo de actor de Service Fabric para crear soluciones "empresariales" o "en la nube".

## Patrones
En esta sección, se enumera un conjunto de patrones y escenarios asociados que usamos durante nuestros compromisos con los clientes. Estos patrones representan clases de problemas que se pueden aplicar a una amplia gama de soluciones que nuestros clientes crean en Microsoft Azure. Aunque los escenarios se basan en casos reales, hemos eliminado la mayoría de los problemas específicos de dominio para que los patrones sean más claros para el lector. Verá que gran parte del código de ejemplo es simple u obvio. Incluiremos ese código para que el ejemplo sea completo y no porque sea algo especialmente ingenioso o impresionante.

Los patrones que se presentan en este documento no pretenden ser globales o canónicos, algunos desarrolladores podrían solucionar el mismo problema o patrón de forma distinta.

[Patrón: Caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

### Más información sobre los actores, una breve historia
El [artículo](http://dl.acm.org/citation.cfm?id=1624804) de Hewitt et al que es el origen del modelo de actor se publicó en 1973, pero hace relativamente poco que el modelo de actor empezó a recibir más atención y a usarse como una forma de tratar con la simultaneidad y la complejidad en los sistemas distribuidos. El modelo de actor admite objetos individuales detallados (actores) que están aislados entre sí. Se comunican mediante el intercambio de mensajes asincrónicos, lo que permite la comunicación directa entre actores. Un actor se ejecuta con la semántica de subproceso único. Junto con la encapsulación del estado del actor y el aislamiento de otros actores, esto simplifica la escritura de sistemas altamente paralelos al eliminar los problemas de simultaneidad del código del actor. Los actores se crean dinámicamente en el grupo de recursos de hardware disponibles.

[Erlang](http://www.erlang.org/) es la implementación más conocida del modelo de actor. Los desarrolladores empezaron a redescubrir el modelo de actor, que fomentó un interés renovado en Erlang y la creación de nuevas soluciones similares a Erlang: actores [Scala](http://www.scala-lang.org/), [Akka](http://akka.io), [Akka.net](http://getakka.net/), [DCell](http://research.microsoft.com/pubs/75988/dcell.pdf).

## Breve descripción de Service Fabric de Azure
Actores de Fabric de Azure es una implementación del modelo de actor que toma prestadas algunas ideas de Erlang y de los sistemas de objetos distribuidos, agrega una capa de direccionamiento indirecto de actores y los expone en un modelo de programación integrado que aprovecha la plataforma Service Fabric de Azure.

Las principales ventajas de los actores de Fabric de Azure son: 1) **la productividad del desarrollador**, incluso para los programadores inexpertos; y 2) **la escalabilidad transparente de forma predeterminada** sin ningún esfuerzo especial del programador. Actores de Fabric de Azure es una biblioteca de .NET que se ejecuta sobre Fabric de Azure y herramientas que facilitan en gran medida el desarrollo de aplicaciones distribuidas complejas y aumenta la escalabilidad de las aplicaciones resultantes por diseño. Se ofrece más información sobre cada una de estas ventajas a continuación. El modelo de programación de actores de Fabric de Azure aumenta la productividad de los programadores expertos e inexpertos, al ofrecer las siguientes abstracciones clave, garantías y servicios del sistema.

* *Paradigma de programación orientada a objetos (OOP) familiar*. Los actores son clases de .NET que implementan interfaces de actor de .NET declaradas con propiedades y métodos asincrónicos. Por lo tanto, los actores aparecen ante el programador como objetos remotos cuyos métodos y propiedades se pueden invocar directamente. Esto ofrece al programador el paradigma de programación orientada a objetos familiar al convertir las llamadas a métodos en mensajes, enrutarlos a los extremos correctos, invocar los métodos del actor de destino y tratar los errores y los casos excepcionales de una forma completamente transparente.

* *Ejecución de subproceso único de actores.* El tiempo de ejecución de Fabric de Azure garantiza que un actor nunca se ejecuta en más de un subproceso a la vez. Junto con el aislamiento de los otros actores, el programador nunca trata la concurrencia en el nivel de actor y, por tanto, nunca debe usar bloqueos u otros mecanismos de sincronización para controlar el acceso a los datos compartidos. Esta característica por sí sola consigue el desarrollo de aplicaciones distribuidas sea manejable para los programadores inexpertos.

* *Activación transparente.* El tiempo de ejecución de Fabric de Azure activa un actor según sea necesario, solo cuando hay un mensaje que debe procesar. Esto separa claramente la noción de creación lógica de un actor, que es visible para el código de la aplicación que la controla, de la activación física del actor en memoria, que es transparente para la aplicación. Actores de Fabric de Azure es similar a la memoria virtual que decide cuándo desactivar (page out) o activar (page in) un actor; la aplicación tiene acceso ininterrumpido a todo el "espacio de memoria" de los actores creados lógicamente, estén o no en la memoria física en un momento determinado en el tiempo. La activación transparente habilita el equilibrio de cargas adaptativo y dinámico mediante la ubicación y la migración de los actores en el grupo de recursos de hardware.

* *Transparencia de ubicación.* Una referencia de actor (objeto proxy) que el programador usa para invocar los métodos de actor o que pasa a otros componentes que solo contiene la identidad lógica del actor. El tiempo de ejecución de Fabric de Azure realiza de manera transparente la traducción de la identidad lógica del actor a su ubicación física y el correspondiente enrutamiento de mensajes. El código de la aplicación se comunica con actores que desconocen su ubicación física, que puede cambiar con el tiempo debido a errores o a la administración de recursos, o porque un actor está desactivado en el momento en que se llama.

* *Integración transparente con el almacén persistente.* Actores de Fabric de Azure permite la asignación declarativa del estado en memoria de los actores al almacén persistente. Sincroniza las actualizaciones y garantiza de forma transparente que los llamadores reciben resultados solo después de que el estado persistente se haya actualizado correctamente.

* *Alta disponibilidad, compatibilidad con la conmutación por error y administración del ciclo de vida de las aplicaciones.* La plataforma administra el estado de los actores de Fabric de Azure y se replica de manera que pueda restaurar si, por ejemplo, se produce un error en un nodo del clúster. Service Fabric de Azure también administra el ciclo de vida de las aplicaciones y permite las actualizaciones de las aplicaciones sin tiempo de inactividad. El modelo de programación de los actores de Fabric de Azure está diseñado para guiar a los programadores por el camino probablemente correcto en el escalado de su aplicación o servicio a través de varias órdenes de magnitud. Esto se consigue con la incorporación de prácticas y patrones recomendados y con una implementación eficaz de funcionalidad de sistema de menor nivel. A continuación se muestran algunos de los factores clave que permiten la escalabilidad y el rendimiento de las aplicaciones de Fabric de Azure.

* *Partición implícita específica del estado de la aplicación.* Mediante el uso de los actores como entidades directamente direccionables, los programadores dividen implícitamente el estado general de sus aplicaciones. Aunque el modelo de programación de los actores de Fabric de Azure no restringe lo grande o pequeño que debe ser un actor, en la mayoría de casos tiene sentido disponer de un número relativamente grande de actores (millones o más), cada uno una representación de una entidad natural de la aplicación, como una cuenta de usuario, un pedido de compra, etc. Debido a que los actores son direccionables de forma individual y a que el tiempo de ejecución abstrae su ubicación física, los actores de Fabric de Azure tienen una gran flexibilidad para equilibrar la carga y trabajar con zonas activas de una manera transparente y genérica, sin que el programador de aplicaciones deba preocuparse.

* *Administración de recursos adaptativa.* Puesto que los actores no hacen ninguna suposición sobre la localidad de otros actores con los que interactúan y debido a la transparencia de ubicación, el tiempo de ejecución de Fabric de Azure puede administrar y ajustar la asignación de recursos de hardware disponibles de manera muy dinámica, mediante la toma de decisiones concretas sobre la ubicación o migración de actores en el clúster de procesamiento, como respuesta a los patrones de carga y comunicación, sin producir errores de solicitudes entrantes. Mediante la creación de varias réplicas de un actor determinado, el tiempo de ejecución puede aumentar el rendimiento del actor si es necesario, sin realizar cambios en el código de la aplicación.

* *Comunicación multiplexada.* Los actores en Fabric de Azure tienen extremos lógicos y la mensajería entre ellos se multiplexa a través de un conjunto fijo de conexiones físicas de tipo de todos a todos (sockets TCP). Esto permite que el tiempo de ejecución de los actores de Fabric de Azure hospeden un gran número (millones) de entidades direccionables sin sobrecarga del sistema operativo por actor. Además, la activación o desactivación de un actor no incurre en el costo de registrar o anular el registro de un extremo físico, como un puerto TCP o una dirección URL HTTP.

* *Programación eficaz.* El tiempo de ejecución de Fabric de Azure programa un gran número de actores de subproceso único en un grupo de subprocesos personalizado con un subproceso por cada núcleo de procesador físico. Dado que el código de actor está escrito en el estilo basado en la continuación sin bloqueo (un requisito del modelo de programación de los actores de Fabric de Azure), el código de la aplicación se ejecuta de una manera multiproceso (cooperativa) muy eficaz, sin contención. Esto permite que el sistema consiga un alto rendimiento y se ejecute con un uso de CPU muy elevado (hasta una cifra superior al 90 %) con gran estabilidad. El hecho de que un aumento del número de actores en el sistema y la carga no dé lugar a subprocesos adicionales u otras primitivas de sistema operativo ayuda a la escalabilidad de los nodos individuales y de todo el sistema.

* *Asincronía explícita.* El modelo de programación de los actores de Fabric de Azure hace que la naturaleza asincrónica de una aplicación distribuida sea explícita y ayuda a los programadores a escribir código asincrónico sin bloqueo. Esto permite un alto grado de paralelismo distribuido y de rendimiento general sin el uso explícito de subprocesamiento múltiple.

<!---HONumber=Oct15_HO2-->