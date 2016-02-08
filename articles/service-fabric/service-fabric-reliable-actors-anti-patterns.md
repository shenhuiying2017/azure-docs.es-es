<properties
   pageTitle="Algunos antipatrones de Azure Service Fabric Actors | Microsoft Azure"
   description="Algunos obstáculos potenciales para los clientes que están aprendiendo sobre los actores de Service Fabric de Azure"
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

# Patrón de diseño de Reliable Actors: algunos antipatrones

Hemos identificado los siguientes obstáculos potenciales que se encuentran los clientes que están aprendiendo a utilizar Reliable Actors de Azure Service Fabric:

* Tratar Actores confiable como un sistema transaccional. Reliable Actors de Service Fabric no es un sistema de dos fases basado en confirmación, que ofrece atomicidad, coherencia, aislamiento y durabilidad (ACID). Si no implementa la persistencia opcional, y la máquina en que se ejecuta el actor sufre un problema, su estado actual se perderá con ella. El actor aparecerá en otro nodo muy rápido, pero a menos que se haya implementado la persistencia de respaldo, el estado se perderá. Sin embargo, si aprovecha los reintentos, el filtrado de duplicados o el diseño idempotente, puede conseguir un alto nivel de confiabilidad y coherencia.

* Bloquear. Todo lo que se hace en Reliable Actors debe ser asincrónico. Normalmente, esto es fácil porque las API asincrónicas son ahora prolíficas en la plataforma de Microsoft. Pero si es necesario interactuar con un sistema que solo ofrezca una API de bloqueo, debe colocarla en un contenedor que use explícitamente el grupo de subprocesos de .NET.

* Diseñe en exceso y deje que el entorno trabaje. Puede ser difícil para los desarrolladores que están acostumbrados a preocuparse por los bloqueos y las colecciones simultáneas o que usan herramientas para compilar objetos de XML dedicarse simplemente a programar una clase que haga cosas sencillas, como asignar un valor a una variable o programar un trabajo. Las tareas programadas están integrada, y los bloqueos no son necesarios. El estado no es un enemigo mortal. Esto conlleva un período de adaptación para los desarrolladores que han realizado mucho trabajo del lado servidor en entornos de gran escala.

* Hacer que un solo actor sea el cuello de botella. Resulta demasiado fácil caer en este, que consiste en tener millones de actores que pasan por una única instancia de otro actor. Use el enfoque de agregación que se muestra en el [patrón de diseño de cálculo distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md).

* Asignar los modelos de entidad ciegamente. Esto suele pasarle a los desarrolladores que proceden de un universo relacional en el que los problemas se modelan mediante entidades y sus relaciones. Aunque este enfoque sigue siendo útil para entender el dominio del asunto, debe combinarse con un pensamiento orientado a servicios y mezclado con el comportamiento.

<!---HONumber=AcomDC_0128_2016-->