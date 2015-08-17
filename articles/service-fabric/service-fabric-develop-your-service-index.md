<properties
   pageTitle="Desarrollar un servicio de Service Fabric"
   description="Tutoriales e información de conceptos que le ayudarán a entender cómo desarrollar un servicio de Service Fabric con los modelos de programación de actor confiable o servicios de confianza."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/14/2015"
   ms.author="ryanwi"/>

# Desarrollar un servicio de Service Fabric
Esta página tiene vínculos a tutorial y artículos de información general y sobre conceptos para ayudarle a aprender a desarrollar un servicio de Service Fabric. Service Fabric ofrece dos modelos de programación de alto nivel para la creación de servicios: las API de actores confiables y las API de servicios de confianza. Aunque ambas se basan en el mismo núcleo de Service Fabric, hacen diferentes compromisos entre la simplicidad y la flexibilidad en términos de simultaneidad, creación de particiones y comunicación. Es útil comprender ambos modelos para que pueda elegir el marco adecuado para un servicio concreto dentro de la aplicación.

- [Elegir un modelo de programación](service-fabric-choose-framework.md)
- [Introducción al modelo de actor de Service Fabric](service-fabric-reliable-actors-introduction.md)
- [Introducción al modelo de programación de un servicio de confianza](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## Modelo de programación de actor confiable
 Los actores confiables ofrecen un modelo uniproceso y asincrónico. Los actores representan la unidad de estado y cálculo que se distribuyen en todo el clúster para lograr una alta escalabilidad. El modelo de actor confiable aprovecha el almacén distribuido ofrecido por la plataforma de Service Fabric subyacente para ofrecer la administración de estados coherente y de alta disponibilidad para los desarrolladores de aplicaciones. Para obtener más información, lea:

- [Introducción a los actores confiables](service-fabric-reliable-actors-get-started.md)
- [Ciclo de vida de actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)
- [Cómo usan los actores de tejido usan la plataforma de Service Fabric](service-fabric-reliable-actors-platform.md)
- [Notas sobre la serialización del tipo de actores de Service Fabric de Azure](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

La comunicación con los agentes se describe en:

- [Introducción al modelo de actor de Service Fabric](service-fabric-reliable-actors-introduction.md#actor-communication)
- [Comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md)

En estos artículos se analizan los escenarios y patrones de diseño útiles:

- [Patrones de diseño del modelo de actor](service-fabric-reliable-actors-patterns-introduction.md)  
- [Patrón: Caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)
- [Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)
- [Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

Se proporciona una simultaneidad simple basada en turnos para los métodos de actor confiable. En estos artículos se describen la simultaneidad, los temporizadores y la reentrada:

- [Simultaneidad](service-fabric-reliable-actors-introduction.md#concurrency)
- [Eventos y contadores de rendimiento relacionados con la simultaneidad](service-fabric-reliable-actors-diagnostics.md)
- [Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)
- [Temporizadores de actor](service-fabric-reliable-actors-timers-reminders.md)
 
Aquí encontrará la información sobre cómo configurar los actores confiables:

- [Configuración de KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [Configuración de actores confiables: ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

Los actores confiables emiten eventos y contadores de rendimiento, que se pueden usar para diagnosticar y supervisar su servicio:

- [Diagnósticos de actor](service-fabric-reliable-actors-diagnostics.md)
- [Eventos de actor](service-fabric-reliable-actors-events.md)


## Modelo de programación del servicio de confianza
Los servicios de confianza le ofrecen un modelo de programación de nivel superior, sencillo y eficaz para ayudarle a expresar lo que es importante para su aplicación. Para obtener más información, lea:

- [Introducción a los servicios de confianza](service-fabric-reliable-services-quick-start.md)
- [Información general del modelo de programación](../service-fabric-reliable-services-service-overview.md)  
- [Arquitectura](service-fabric-reliable-services-platform-architecture.md)
- [Colecciones confiables](service-fabric-reliable-services-reliable-collections.md)
- [Configuración de los servicios confiables con estado](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [Uso avanzado del modelo de programación de servicios de confianza](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

A continuación se describen la comunicación con los servicios de confianza y las abstracciones que los clientes pueden usar para detectar y comunicarse con los extremos de servicio:

- [Comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md)
- [Modelo de comunicación de servicio](service-fabric-reliable-services-communication.md)
- [Pila de comunicación predeterminada ofrecida por el marco de servicios de confianza](service-fabric-reliable-services-communication-default.md)
- [Pila de comunicación basada en WCF de servicios de confianza](service-fabric-reliable-services-communication-wcf.md)
- [Introducción a los servicios de la API web de Service Fabric de Microsoft Azure con el autohospedaje de OWIN (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

Los servicios de confianza emiten eventos y contadores de rendimiento, que se pueden usar para diagnosticar y supervisar su servicio:

- [Diagnóstico de servicios de confianza con estado](service-fabric-reliable-services-diagnostics.md)
 

<!---HONumber=August15_HO6-->