<properties 
   pageTitle="Escenarios y diseño de aplicación con Service Fabric" 
   description="Categorías de aplicaciones. Diseño de la aplicación mediante servicios con estado y sin estado" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/24/2015"
   ms.author="mfussell"/>

# Escenarios de aplicación de Service Fabric

Service Fabric y Azure ofrecen una plataforma de infraestructura en la nube confiable y flexible que le permite ejecutar muchos tipos de aplicaciones y servicios empresariales. Estas aplicaciones y microservicios pueden tener estado o no tenerlo, y se equilibran mediante recursos en las máquinas virtuales para maximizar su eficacia. La arquitectura única de Service Fabric le permite realizar análisis de datos casi en tiempo real, cálculo en memoria, transacciones paralelas y procesamiento de eventos en sus aplicaciones. Puede escalar o reducir verticalmente sus aplicaciones con facilidad, dependiendo de sus requisitos de recurso variables.

La plataforma Service Fabric de Azure es ideal para las siguientes categorías de aplicaciones y servicios:

- **Servicios de alta disponibilidad**: los servicios de Service Fabric proporcionan una conmutación por error muy rápida. Varias réplicas de servicio secundarias siguen estando disponibles. Si un nodo deja de funcionar por un error de hardware, una de las réplicas secundarias se amplía inmediatamente a una réplica principal con una pérdida insignificante de servicio para los clientes. Los servicios se pueden escalar verticalmente de forma rápida y fácil de unas pocas instancias a miles de instancias y, a continuación, reducir verticalmente a unas pocas instancias, dependiendo de sus necesidades de recursos. Puede usar Service Fabric para crear y administrar el ciclo de vida de estos servicios en la nube escalables.

- **Servicios escalables**: los servicios individuales pueden tener particiones, permitiendo que el estado se escale horizontalmente en el clúster. Además, los numerosos servicios de Service Fabric pueden crearse y escalarse horizontalmente.
 
- **Cálculo de datos no estáticos**: Service Fabric le permite crear E/S de datos y calcular aplicaciones con estado de uso intensivo. Los recursos de procesamiento y datos en las aplicaciones de Service Fabric se encuentran juntos, de modo que cuando su aplicación requiere lecturas y escrituras rápidas, la latencia de red asociada a una capa de almacenamiento o memoria caché de datos externa se elimina. Por ejemplo, supongamos que tuviera una aplicación que realiza una selección de anuncios casi en tiempo real con un tiempo de ida y vuelta inferior a 100 ms, donde el cálculo de las reglas de selección de anuncios proporciona una experiencia dinámica al usuario.
 
- **Aplicaciones interactivas basadas en sesión**: Service Fabric es útil si sus aplicaciones, como juegos en línea o mensajería instantánea, requieren lecturas y escrituras de baja latencia. Service Fabric le permite crear estas aplicaciones con estado interactivas sin tener que crear un almacén o memoria caché independientes necesarios para las aplicaciones sin estado.
 
- **Procesamiento gráfico distribuido**: el crecimiento de las redes sociales ha aumentado en gran medida la necesidad de analizar gráficos a gran escala en paralelo. El escalado rápido y el procesamiento de carga paralelo convierten Service Fabric en una plataforma natural para el procesamiento de gráficos a gran escala. Service Fabric le permite crear servicios altamente escalables para grupos como redes sociales, inteligencia empresarial e investigación científica.
 
- **Análisis y flujos de trabajo de datos**: las lecturas y escrituras rápidas de Service Fabric permiten las aplicaciones que deben procesar de forma confiable eventos o secuencias de datos. Service Fabric también permite las aplicaciones que describen una canalización de procesamiento, donde los resultados deben ser confiables y, a continuación, transferirse sin pérdida a la siguiente fase de procesamiento, incluidos los sistemas transaccionales y financieros, donde la coherencia y el cálculo de los datos son fundamentales.

## Diseño de aplicaciones compuestas por microservicios sin y con estado ##
La creación de aplicaciones con roles de trabajo del servicio en la nube de Azure es un ejemplo de servicios sin estado. Por el contrario, los microservicios con estado mantienen el estado autorizado más allá de la solicitud y su respuesta, pero proporcionando la replicación del estado a través de API sencillas. Los servicios con estado son la democratización de alta disponibilidad (HA) de todas las aplicaciones, no solo de las bases de datos y otros almacenes de datos. Esta es la progresión natural del diseño, pues las aplicaciones han pasado de usar bases de datos puramente relacionales para alta disponibilidad a bases de datos NoSQL y ahora a las propias aplicaciones con estado administrado en su interior para los datos "activos" que deben mantenerse cerca del proceso.

Al crear aplicaciones que constan de microservicios, suele tener una combinación de aplicaciones web sin estado (ASP.NET, node.js, etc.) que llaman a servicios de nivel intermedio de negocio sin y con estado, todos ellos implementados en el mismo clúster de Service Fabric mediante los comandos de implementación de Service Fabric. La independencia de escala, confiabilidad y uso de recursos de cada uno de estos microservicios proporciona esta agilidad en el desarrollo y la administración del ciclo de vida.
  
Los microservicios con estado simplifican los diseños de la aplicación al acabar con la necesidad de colas y memorias caché adicionales tradicionalmente necesarias para cumplir los requisitos de disponibilidad y latencia de una aplicación puramente sin estado. Como los servicios con estado son de alta disponibilidad y baja latencia de forma natural, hay menos partes en movimiento que administrar en su aplicación en su conjunto. En los siguientes diagramas se muestran las diferencias entre diseñar una aplicación sin estado y otra con estado. En el caso de las aplicaciones con estado, al aprovecharse los modelos de programación de [servicios fiables](../Service-Fabric/service-fabric-reliable-services-introduction.md) y [actores fiables](service-fabric-reliable-actors-introduction.md), los servicios con estado reducen la complejidad de la aplicación, al mismo tiempo que se obtienen un alto rendimiento y una baja latencia.

## Una aplicación compilada con servicios sin estado##
![Aplicación que hace uso del servicio sin estado][Image1]

## Una aplicación compilada con servicios con estado##
![Aplicación que hace uso del servicio sin estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes


Empiece a crear servicios sin y con estado con los modelos de programación de [servicios fiables](service-fabric-reliable-services-quick-start.md) y [actores fiables](service-fabric-reliable-actors-get-started.md) de Service Fabric.

Vea también los siguientes temas:

[Definición y administración del estado del servicio](service-fabric-concepts-state.md)

[Disponibilidad de servicios](../service-fabric-concepts-availability-services.md)

[Escalabilidad de servicios de Service Fabric](service-fabric-concepts-scalability.md)

[Creación de particiones de servicios de Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=July15_HO2-->