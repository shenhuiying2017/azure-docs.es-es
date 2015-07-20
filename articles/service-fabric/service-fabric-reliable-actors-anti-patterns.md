<properties
   pageTitle="Algunos antipatrones de los actores de Service Fabric de Azure"
   description="Algunos obstáculos potenciales para los clientes que están aprendiendo sobre los actores de Service Fabric de Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="claudioc"/>

# Patrón de diseño de los actores de Service Fabric: algunos antipatrones

Identificamos los siguientes obstáculos potenciales para los clientes que están aprendiendo sobre los actores de Service Fabric de Azure:

* Tratar los actores de Service Fabric de Azure como un sistema transaccional. Actores de Service Fabric de Azure no es un sistema basado en confirmaciones de dos fases que ofrece ACID. Si no se implementa la persistencia opcional y la máquina en que se ejecuta el actor sufre un problema, su estado actual se perderá con ella. El actor aparecerá en otro nodo muy rápido, pero a menos que se haya implementado la persistencia de respaldo, el estado se perderá. Sin embargo, si aprovecha los reintentos, el filtrado de duplicados o el diseño idempotente, puede conseguir un alto nivel de confiabilidad y coherencia.

* Bloquear. Todo lo que se hace en actores de Service Fabric de Azure debe ser asincrónico. Normalmente, esto es fácil porque las API asincrónicas son ahora prolíficas en la plataforma de Microsoft. Pero si, por algún motivo, es necesario interactuar con un sistema que solo ofrezca una API de bloqueo, será necesario colocarla en un contenedor que use explícitamente el grupo de subprocesos de .NET.

* Diseñar en exceso. Deje que el entorno trabaje. Puede ser difícil para los desarrolladores que están acostumbrados a preocuparse por los bloqueos y las colecciones simultáneas o que usan herramientas para compilar objetos de XML, dedicarse simplemente a programar una clase que haga cosas sencillas, como asignar un valor a una variable o programar un trabajo. Las tareas de programación están integradas. Los bloqueos no son necesarios. El estado no es un enemigo mortal. Esto conlleva un período de adaptación para los que han realizado mucho trabajo del lado servidor en entornos de gran escala.

* Hacer que un solo actor sea el cuello de botella. A menudo resulta demasiado fácil caer en este, que consiste en tener millones de actores que pasan por una única instancia de otro actor. Use el enfoque de agregación que se muestra en el [patrón de diseño de cálculo distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Asignar los modelos de entidad ciegamente. Esto suele pasarle a los desarrolladores que proceden de un universo relacional en el que los problemas se modelan mediante entidades y sus relaciones. Aunque este enfoque sigue siendo útil para entender el dominio del asunto, debe combinarse con un pensamiento orientado a servicios y mezclado con el comportamiento.
 

<!---HONumber=July15_HO2-->