<properties 
   pageTitle="Escenarios y diseño de aplicación con Service Fabric" 
   description="Información general de las categorías de las aplicaciones de nube en Service Fabric. Describe el diseño de la aplicación mediante servicios con estado y sin estado."
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
   ms.date="11/30/2015"
   ms.author="mfussell"/>

# Escenarios de aplicación de Service Fabric

Microsoft Azure Service Fabric ofrece una plataforma confiable y flexible que le permite escribir y ejecutar muchos tipos de aplicaciones y servicios empresariales. Estas aplicaciones y microservicios pueden tener estado o no tenerlo, y se equilibran mediante recursos en las máquinas virtuales para maximizar su eficacia. La arquitectura única de Service Fabric le permite realizar análisis de datos casi en tiempo real, cálculo en memoria, transacciones paralelas y procesamiento de eventos en sus aplicaciones. Puede escalar o reducir verticalmente sus aplicaciones con facilidad (en realidad hacia dentro o hacia fuera), dependiendo de sus requisitos de recursos variables.

La plataforma Service Fabric de Azure es ideal para las siguientes categorías de aplicaciones y servicios:

- **Servicios de alta disponibilidad**: los servicios de Service Fabric proporcionan una conmutación por error muy rápida. Service Fabric le permite crear varias réplicas de servicio secundario. Si un nodo, proceso o servicio individual deja de funcionar por un error de hardware u otro fallo, una de las réplicas secundarias se amplía inmediatamente a una réplica principal con una pérdida insignificante de servicio para los clientes. 

- **Servicios escalables**: los servicios individuales pueden tener particiones, permitiendo que el estado se escale horizontalmente en el clúster. Además, pueden crearse y eliminarse servicios individuales sobre la marcha. Los servicios se pueden escalar horizontalmente de forma rápida y fácil en unas pocas instancias en unos cuantos nodos hasta miles de instancias en muchos nodos y, a continuación, reducir verticalmente de manera inmediata otra vez, dependiendo de sus necesidades de recursos. Puede usar Service Fabric para crear estos servicios y administrar su ciclo de vida completo.
 
- **Cálculo de datos no estáticos**: Service Fabric le permite crear E/S de datos y calcular aplicaciones con estado de uso intensivo. Service Fabric permite la colocación de los datos y el procesamiento (cálculo) en las aplicaciones. Hoy en día normalmente cuando la aplicación requiere acceso a datos hay una latencia de red que está asociada con un nivel de caché o almacenamiento de datos externos. Con servicios Service Fabric con estado, esa latencia se elimina, lo que permite unas lecturas y escrituras de mayor rendimiento. Por ejemplo, supongamos que tiene una aplicación que realiza una selección de recomendaciones en tiempo real para los clientes con un requisito de tiempo de ida y vuelta inferior a 100 ms. Las características de latencia y rendimiento de los servicios Service Fabric (en el que el cálculo de la selección de la recomendación se combina con los datos y las reglas) proporciona una experiencia con capacidad de respuesta al usuario en comparación con el modelo de implementación estándar de tener que recuperar los datos necesarios del almacenamiento remoto.
 
- **Aplicaciones interactivas basadas en sesión**: Service Fabric es útil si sus aplicaciones, como juegos en línea o mensajería instantánea, requieren lecturas y escrituras de baja latencia. Service Fabric le permite crear estas aplicaciones con estado interactivas sin tener que crear un almacén o memoria caché independientes necesarios para las aplicaciones sin estado (aumentando la latencia y provocando posiblemente problemas de coherencia).
 
- **Procesamiento gráfico distribuido**: el crecimiento de las redes sociales ha aumentado en gran medida la necesidad de analizar gráficos a gran escala en paralelo. El escalado rápido y el procesamiento de carga paralelo convierten Service Fabric en una plataforma natural para el procesamiento de gráficos a gran escala. Service Fabric le permite crear servicios altamente escalables para grupos como redes sociales, inteligencia empresarial e investigación científica.
 
- **Análisis y flujos de trabajo de datos**: las lecturas y escrituras rápidas de Service Fabric permiten las aplicaciones que deben procesar de forma confiable eventos o secuencias de datos. Service Fabric también permite las aplicaciones que describen una canalización de procesamiento, donde los resultados deben ser confiables y, a continuación, transferirse sin pérdida a la siguiente fase de procesamiento, incluidos los sistemas transaccionales y financieros, donde la coherencia y el cálculo de los datos son fundamentales.

## Diseño de aplicaciones compuestas por microservicios sin y con estado
La creación de aplicaciones con roles de trabajo del servicio en la nube de Azure es un ejemplo de servicios sin estado. Por el contrario, los microservicios con estado mantienen el estado autoritativo más allá de la solicitud y su respuesta, proporcionando una alta disponibilidad y coherencia de dicho estado a través de API sencillas que proporcionan garantías transaccionales respaldadas por la replicación. Los servicios con estado de Service Fabric democratizan la alta disponibilidad (HA), llevándola a todos los tipos de aplicaciones, no solo a las bases de datos y otros almacenes de datos. Se trata de una progresión natural: las aplicaciones ya han pasado de usar bases de datos puramente relacionales para la alta disponibilidad a bases de datos NoSQL; ahora las propias aplicaciones pueden administrar su estado y datos "activos" dentro de ellos para obtener aumentos adicionales del rendimiento sin sacrificar la confiabilidad, la coherencia o la disponibilidad.

Al crear aplicaciones que constan de microservicios, suele tener una combinación de aplicaciones web sin estado (ASP.NET, node.js, etc.) que llaman a servicios de nivel intermedio de negocio sin y con estado, todos ellos implementados en el mismo clúster de Service Fabric mediante los comandos de implementación de Service Fabric. Cada uno de estos servicios es independiente en lo relacionado con la escala, la confiabilidad y el uso de recursos, mejorando significativamente la agilidad en el desarrollo y la administración del ciclo de vida.
  
Los microservicios con estado simplifican los diseños de la aplicación al acabar con la necesidad de colas y memorias caché adicionales tradicionalmente necesarias para cumplir los requisitos de disponibilidad y latencia de una aplicación puramente sin estado. Como los servicios con estado son de alta disponibilidad y de baja latencia de forma natural, hay menos partes en movimiento que administrar en su aplicación en su conjunto. En los siguientes diagramas se muestran las diferencias entre diseñar una aplicación sin estado y otra con estado. Al aprovecharse los modelos de programación de [Reliable Services](service-fabric-reliable-services-introduction.md) y [Reliable Actors](service-fabric-reliable-actors-introduction.md), los servicios con estado reducen la complejidad de la aplicación, al mismo tiempo que se obtienen un alto rendimiento y una baja latencia.

## Una aplicación compilada con servicios sin estado##
![Aplicación que hace uso del servicio sin estado][Image1]

## Una aplicación compilada con servicios con estado##
![Aplicación que hace uso del servicio sin estado][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes


Empiece a crear servicios sin y con estado con los modelos de programación de [servicios fiables](service-fabric-reliable-services-quick-start.md) y [actores fiables](service-fabric-reliable-actors-get-started.md) de Service Fabric.

Vea también los siguientes temas:

[Quiero información acerca de los microservicios](service-fabric-overview-microservices.md)

[Definición y administración del estado del servicio](service-fabric-concepts-state.md)

[Disponibilidad de servicios](service-fabric-availability-services.md)

[Escalabilidad de servicios de Service Fabric](service-fabric-concepts-scalability.md)

[Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=AcomDC_1203_2015-->