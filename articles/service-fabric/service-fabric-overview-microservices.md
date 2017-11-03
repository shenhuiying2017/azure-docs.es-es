---
title: "Introducción a los microservicios en Azure | Microsoft Docs"
description: "Información general sobre por qué la creación de aplicaciones de nube con un enfoque de microservicios es importante para el desarrollo de aplicaciones modernas y cómo Azure Service Fabric proporciona una plataforma para lograrlo."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: f69f594d058ba061cec116f87435c96280e19f93
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="why-a-microservices-approach-to-building-applications"></a>¿Por qué usar un enfoque de microservicios para crear aplicaciones?
La forma en que los desarrolladores de software nos planteamos la factorización de una aplicación en sus distintos componentes no es nueva en absoluto. Es el paradigma central de la orientación a objetos, las abstracciones de software y la componentización. En la actualidad, esta factorización suele adoptar la forma de clases e interfaces entre bibliotecas compartidas y niveles de tecnología. Normalmente, se emplea un enfoque con niveles con un almacén en el back-end, lógica de negocios en el nivel intermedio y una interfaz de usuario (IU) en el front-end. Lo que *ha* cambiado en los últimos años es que los desarrolladores ahora crean aplicaciones distribuidas para la nube impulsados por las empresas.

Las cambiantes necesidades empresariales son:

* Creación y operación de un servicio a escala para llegar a clientes en nuevas regiones geográficas (por ejemplo).
* Entrega más rápida de características y funciones para poder responder a las demandas de los clientes de forma ágil.
* Mejora de la utilización de los recursos para reducir costos.

Todas estas necesidades afectan a la *forma* en que se crean las aplicaciones.

Para más información sobre el enfoque de Azure de los microservicios, vea [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservicios: una revolución en las aplicaciones con la tecnología de la nube).

## <a name="monolithic-vs-microservice-design-approach"></a>Enfoque de diseño monolítico en comparación con el de microservicios
Todas las aplicaciones evolucionan con el tiempo. Las aplicaciones que triunfan evolucionan por ser útiles a sus usuarios. Las que fracasan no evolucionan y terminan por entrar en desuso. Por tanto, la pregunta que debe hacerse es: ¿cuánto sabe acerca de los requisitos actuales y cuáles serán en el futuro? Por ejemplo, supongamos que está creando una aplicación de informes para un departamento. Está seguro de que la aplicación permanece dentro del ámbito de la compañía y que los informes son relevantes por un tiempo limitado. El enfoque elegido es diferente al adoptado para crear un servicio que entrega contenido de vídeo a decenas de millones de clientes. 

A veces, terminar un servicio como prueba de concepto es el factor determinante, con la certeza de que el diseño de la aplicación podrá modificarse más adelante. No tiene mucho sentido volcarse en la ingeniería de algo que no se va a usar nunca. Es el equilibrio habitual en ingeniería. Por otra parte, cuando las empresas hablan sobre la creación de aplicaciones para la nube, lo que esperan es crecimiento y uso. El problema es que el crecimiento y la escala son imprevisibles. Nos gustaría poder crear prototipos rápidamente y, al mismo tiempo, saber que estamos bien encaminados para tratar el futuro éxito. Este es el enfoque Lean Startup: compilar, medir, aprender e iterar.

En la época de cliente-servidor, tendíamos a centrarnos en la creación de aplicaciones en capas mediante el uso de tecnologías específicas en cada nivel. El término aplicación *monolítica* ha surgido de estos enfoques. Las interfaces tendían a estar entre los niveles y normalmente utilizaban un diseño más estrechamente acoplado entre los componentes de cada nivel. Los desarrolladores diseñaban y generaban clases compiladas en bibliotecas y las vinculaban entre sí en algunos archivos ejecutables y DLL. 

Dicho enfoque de diseño monolítico tiene varias ventajas. A menudo es más fácil de diseñar y las llamadas entre los componentes son más rápidas, ya que generalmente se realizan a través de comunicación entre procesos (IPC). Además, todo el mundo prueba un único producto, lo que tiende a ser más eficiente en la relación entre personas y recursos. La desventaja es que se produce un acoplamiento estrecho entre las capas en niveles y no se pueden escalar los componentes individuales. Si necesita realizar actualizaciones o correcciones, tendrá que esperar hasta que otros usuarios finalicen sus pruebas, lo que dificulta la agilidad.

Los microservicios solucionan estas desventajas y se adaptan mejor a los anteriores requisitos empresariales, pero también tienen ventajas y desventajas. Las ventajas de los microservicios son que cada uno suele encapsular funcionalidades empresariales más simples, las cuales se pueden escalar o reducir verticalmente, probar, implementar y administrar de forma independiente. Una ventaja importante del enfoque de microservicios es que los equipos están más condicionados por los escenarios empresariales que por la tecnología, que es lo que fomenta el enfoque con niveles. En la práctica, los equipos más pequeños desarrollan un microservicio en función de un escenario del cliente y usan las tecnologías que prefieren. 

Es decir, no es necesario que la organización normalice su tecnología para mantener aplicaciones de microservicio. Los equipos individuales con servicios propios pueden hacer lo más lógico en función de sus conocimientos o de lo que sea más adecuado para resolver el problema. En la práctica, es preferible tener un conjunto de tecnologías recomendadas, como un almacén NoSQL o un marco de aplicaciones web concretos.

La desventaja de los microservicios surge al tener que administrar un mayor número de entidades independientes y trabajar con la mayor complejidad de las implementaciones y el control de versiones. Aumentan tanto el tráfico de red entre los microservicios como las latencias de red correspondientes. Una gran cantidad de servicios granulares comunicándose entre sí no trae nada bueno para el rendimiento. Sin herramientas que ayuden a ver estas dependencias, es difícil “ver” todo el sistema. 

Los estándares hacen que el enfoque de microservicios funcione al establecer un acuerdo sobre cómo llevar a cabo la comunicación y tolerar únicamente lo que se necesita de un servicio, en lugar de usar contratos rígidos. Es importante definir estos contratos por adelantado en el diseño, ya que los servicios se actualizan de forma independiente entre sí. Otra descripción acuñada para el diseño con el enfoque de microservicios es "arquitectura orientada a servicios (SOA) específica".

***En su forma más simple, el enfoque de diseño de microservicios consiste en una federación desacoplada de servicios, con cambios independientes en cada uno, y estándares acordados para la comunicación.***

A medida que se producen más aplicaciones en la nube, se descubre que la descomposición de la aplicación global en servicios independientes centrados en escenarios es a largo plazo un mejor enfoque.

## <a name="comparison-between-application-development-approaches"></a>Comparación entre los enfoques de desarrollo de aplicaciones
![Desarrollo de aplicaciones de la plataforma Service Fabric][Image1]

1) Las aplicaciones monolíticas contienen funciones específicas del dominio y normalmente se dividen en capas funcionales, como Web, negocios y datos.

2) Para escalar aplicaciones monolíticas, es preciso clonarlas en varios servidores, máquinas virtuales o contenedores.

3) Las aplicaciones de microservicios separan las funciones en servicios más pequeños independientes.

4) Este enfoque de microservicios se escala horizontalmente mediante la implementación de cada servicio de manera independiente, con la creación de instancias de estos servicios en servidores, máquinas virtuales y contenedores.

La realización del diseño con un enfoque de microservicios no es la panacea para todos los proyectos, pero se adapta mejor a los objetivos de negocio descritos. Comenzar con un enfoque monolítico sería aceptable si se sabe que después no habrá oportunidad de reprocesar el código en un diseño con microservicios. Es más frecuente comenzar con una aplicación monolítica y, poco a poco, dividirla en fases, empezando por las áreas funcionales que deban ser más escalables o ágiles.

En resumen, el enfoque de microservicios consiste en componer la aplicación de muchos servicios pequeños. Los servicios se ejecutan en contenedores implementados en un clúster de máquinas. Los equipos más pequeños desarrollan un servicio que se centra en un escenario y, de forma independiente, prueban, controlan versiones, implementan y escalan cada servicio para que toda la aplicación pueda evolucionar.

## <a name="what-is-a-microservice"></a>¿Qué es un microservicio?
Hay distintas definiciones de microservicios. Si busca en Internet, encontrará muchos recursos útiles que proporcionan sus propios puntos de vista y definiciones. Sin embargo, la mayoría coincide en la mayor parte de las siguientes características de los microservicios:

* Encapsulan un escenario de cliente o negocio. ¿Cuál es el problema que va a solucionar?
* Desarrollados por un pequeño equipo de ingenieros.
* Escritos en cualquier lenguaje de programación y usando cualquier marco.
* Constan de código y, opcionalmente, de un estado, ambos de los cuales se controlan para versiones, implementan y escalan de forma independiente.
* Interactúan con otros microservicios a través de protocolos e interfaces bien definidos.
* Tienen nombres únicos (direcciones URL) que se usan para resolver su ubicación.
* Siguen siendo coherentes y estando disponibles en caso de errores.

Puede resumir estas características en:

***Las aplicaciones de microservicios se componen de servicios pequeños centrados en el cliente, escalables y con control de versiones independiente que se comunican entre sí mediante protocolos estándar con interfaces bien definidas.***

Ya tratamos los dos primeros puntos en la sección anterior y ahora vamos a ampliar y aclarar los restantes.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Escritos en cualquier lenguaje de programación y usando cualquier marco
Como desarrolladores, deberíamos disponer de la libertad de elegir un lenguaje o marco que queramos según nuestras habilidades o las necesidades del servicio. En algunos servicios, podría valorar las ventajas de rendimiento que C++ aporta por encima de todo. En otros servicios, la facilidad del desarrollo administrado en C# o Java podría ser lo más importante. En algunos casos, es posible que se tenga que usar una biblioteca de un asociado, una tecnología de almacenamiento de datos o medios para exponer el servicio a los clientes.

Una vez que haya seleccionado la tecnología, pasamos a la administración operativa o del ciclo de vida y al escalado del servicio.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Permite que el control de versiones, la implementación y el escalado del código y del estado se realicen de forma independiente
Independientemente de la forma en que elija escribir los microservicios, el código y, opcionalmente, el estado se deben implementar, actualizar y escalar de forma independiente. De hecho, este problema es uno de los más difíciles de resolver, ya que depende de la tecnología que se elija. Para el escalado, comprender cómo particionar tanto el código como el estado presenta dificultades. Si el código y el estado usan tecnologías independientes (algo que hoy es bastante habitual), es preciso que los scripts de implementación del microservicio puedan escalar ambos. También tienen relevancia la agilidad y la flexibilidad, por lo que puede actualizar algunos de los microservicios sin que sea preciso de actualizar todos a la vez.

Si volvemos un momento a la comparación entre el enfoque monolítico y el de microservicios, el diagrama siguiente muestra las diferencias en lo que respecta al almacenamiento del estado.

#### <a name="state-storage-between-application-styles"></a>Almacenamiento de estado en los distintos estilos de aplicación
![Almacenamiento de estados de la plataforma Service Fabric][Image2]

***El enfoque monolítico de la izquierda tiene una base de datos única y niveles de tecnologías específicas.***

***El enfoque de microservicios de la derecha tiene un gráfico de microservicios interconectados donde el estado normalmente tiene como ámbito el microservicio y se usan diversas tecnologías.***

En un enfoque monolítico, normalmente la aplicación usa una base de datos única. La ventaja es que está en una única ubicación, lo que facilita la implementación. Cada componente puede tener una única tabla para almacenar su estado. Los equipos tienen que separar el estado estrictamente, lo cual supone un reto. Inevitablemente, existe la tentación de agregar una nueva columna a una tabla de clientes existente, realizar una combinación entre tablas y crear dependencias en la capa de almacenamiento. Tras esto, no es posible escalar los componentes individuales. 

En el enfoque de microservicios, cada servicio administra y almacena su propio estado. Cada servicio se encarga de escalar el código y el estado juntos para satisfacer las demandas del servicio. La desventaja es que, cuando sea necesario crear vistas o consultas de los datos de la aplicación, tendrá que consultar almacenes de estado dispares. Por lo general, esto se resuelve teniendo un microservicio independiente que genera una vista de una colección de microservicios. Si necesita realizar varias consultas espontáneas en los datos, cada microservicio debería considerar la posibilidad escribir sus datos en un servicio de almacenamiento de datos para poder realizar análisis sin conexión.

El control de versiones es específico de la versión implementada de un microservicio, de forma que se implementan y ejecutan en paralelo varias versiones diferentes. El control de versiones sirve para los escenarios en que una versión más reciente de un microservicio produce un error durante la actualización y es preciso revertir a una anterior. El otro escenario para el control de versiones es la realización de pruebas de estilo A/B en las que distintos usuarios experimentan versiones diferentes del servicio. Por ejemplo, es común actualizar un microservicio para que un conjunto específico de clientes prueben probar nuevas funcionalidades, antes de distribuirlas más ampliamente. Después de la administración del ciclo de vida de los microservicios, pasamos a la comunicación entre ellos.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interactúan con otros microservicios por medio de protocolos e interfaces bien definidos.
No necesitamos centrarnos aquí en este tema, ya que en los últimos diez años se ha publicado amplia documentación sobre la arquitectura orientada a servicios en la que se describen los patrones de comunicación. Por lo general, la comunicación en los servicios usa un enfoque de REST con los protocolos HTTP y TCP, y XML o JSON como formato de serialización. Desde la perspectiva de la interfaz, esto supone adoptar el enfoque de diseño web. No obstante, no hay nada que le impida usar protocolos binarios o sus propios formatos de datos. Tenga en cuenta que la gente puede tener problemas para usar sus microservicios si estos protocolos y formatos no están disponibles públicamente.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Tiene un nombre único (dirección URL) que se usa para resolver su ubicación
¿Recuerda que repetimos varias veces que el enfoque de microservicios es como la Web? Al igual que la Web, el microservicio debe ser direccionable, independientemente de dónde se ejecute. Si piensa en las máquinas y en cuál de ellas se ejecuta un microservicio concreto, las cosas empeoran rápidamente. 

De la misma manera que DNS resuelve una dirección URL determinada en una máquina concreta, su microservicio necesita un nombre único para que la ubicación actual sea detectable. Los microservicios necesitan nombres direccionables que los hagan independientes de la infraestructura en que se ejecutan. Esto implica que existe una interacción entre la forma en que se implementa el servicio y cómo se detecta, ya que debe haber un Registro del servicio. Igualmente, cuando se produce un error en una máquina, el servicio de Registro debe indicar dónde se está ejecutando el servicio. 

Esto nos lleva al siguiente tema: la resistencia y la coherencia.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Siguen siendo coherentes y estando disponibles en el caso de errores.
Enfrentarse a errores inesperados es uno de los problemas más difíciles de resolver, sobre todo en un sistema distribuido. Una gran parte del código que escribimos los desarrolladores sirve para administrar las excepciones y en las pruebas, dedicamos gran parte de nuestro tiempo a esta tarea. No obstante, el problema requiere algo más que escribir código para controlar los errores. ¿Qué ocurre cuando se produce un error en la máquina en que se ejecuta el microservicio? No solo es preciso detectar el error del microservicio (un problema difícil en sí), sino que se necesita algo para reiniciar el microservicio. 

Es necesario que el microservicio sea resistente a los errores y se reinicie con frecuencia en otra máquina por motivos de disponibilidad. También es importante qué estado se guardó en nombre del microservicio, desde dónde puede el microservicio recuperar dicho estado y si puede reiniciarlo correctamente. Es decir, es preciso que haya resistencia en el proceso (el proceso se reinicia), así como en el estado o en los datos (no se pierden datos y estos mantienen su coherencia).

Los problemas de resistencia se agravan en otros escenarios, como cuando se producen errores en la actualización de una aplicación. El microservicio, si funciona con el sistema de implementación, no necesita recuperarse. También es preciso decidir si puede pasar a la versión más reciente o es preciso revertirlo a una versión anterior para mantener la coherencia del estado. Se deben tener en cuenta cuestiones tales como si hay máquinas suficientes como para pasar a la versión más reciente y cómo recuperar las versiones anteriores del microservicio. Para esto, se requiere que el microservicio emita información de estado que permita tomar estas decisiones.

### <a name="reports-health-and-diagnostics"></a>Notifica el estado y diagnósticos
Aunque pueda parecer obvio y a menudo se pase por alto, un microservicio debe informar sobre su mantenimiento y diagnóstico. De lo contrario, hay poca información desde la perspectiva de las operaciones. Correlacionar eventos de diagnóstico en un conjunto de servicios independientes y enfrentarse a sesgos del reloj de las máquinas para entender el orden de eventos presenta dificultades. De la misma manera que se interactúa con un microservicio por medio de protocolos y formatos de datos acordados, surge la necesidad de normalizar la forma en que se registran los eventos de mantenimiento y diagnóstico que, en último término, terminan en un almacén de eventos donde se pueden consultar y ver. En un enfoque de microservicios, es fundamental que los diferentes equipos acuerden un único formato de registro. Debe haber un enfoque coherente para poder ver los eventos de diagnóstico en la aplicación como un todo.

El estado es diferente de los diagnósticos. El mantenimiento hace referencia a que el microservicio notifica su estado actual para que se puedan llevar a cabo las acciones adecuadas. Un buen ejemplo es colaborar con los mecanismos de actualización e implementación para mantener la disponibilidad. Aunque un servicio puede tener un estado incorrecto debido a un bloqueo de un proceso o al reinicio de la máquina, puede seguir operativo. En este caso, lo menos recomendables es realizar una actualización. El mejor enfoque es investigar primero, o bien dejar tiempo para que el microservicio se recupere. Los eventos de mantenimiento de un microservicio permiten tomar decisiones fundamentadas y, de hecho, ayudan a crear servicios de recuperación automática.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric como plataforma de microservicios
Azure Service Fabric surgió cuando Microsoft pasó de ofrecer productos embalados, cuyo estilo era habitualmente monolítico, a ofrecer servicios. La experiencia de crear y operar servicios grandes, como Azure SQL Database y Azure Cosmos DB, dio forma a Service Fabric. La plataforma evolucionó con el tiempo a medida que la fueron adoptando cada vez más servicios. Algo importante era que Service Fabric tenía que ejecutarse no solo en Azure, sino también en implementaciones independientes de Windows Server.

***El objetivo de Service Fabric es resolver las complicaciones que conlleva crear y ejecutar un servicio y usar de forma eficiente los recursos de infraestructura para que los equipos puedan resolver problemas de negocios con un enfoque de microservicios.***

Service Fabric proporciona tres amplias áreas que le ayudan a crear aplicaciones que usan un enfoque de microservicios:

* Una plataforma que proporciona servicios del sistema para implementar, actualizar, detectar y reiniciar servicios con errores, detectar servicios, enrutar mensajes, administrar el estado y supervisar el mantenimiento. De hecho, estos servicios del sistema hacen posibles muchas de las características de microservicios descritas antes.
* La posibilidad de implementar aplicaciones que se ejecuten en contenedores o como procesos. Service Fabric es un organizador de contenedores y proceso.
* API de programación productivas que ayudan a crear aplicaciones como microservicios: [ASP.NET Core, Reliable Actors y Reliable Services](service-fabric-choose-framework.md). Puede usar cualquier código para generar el microservicio. Sin embargo, estas API facilitan el trabajo y se integran con la plataforma a un nivel más profundo. Por ejemplo, con ellas es posible obtener información de estado y de diagnóstico, o se puede aprovechar la alta disponibilidad integrada.

***Service Fabric es independiente de la forma en que cree el servicio y permite usar cualquier tecnología. Sin embargo, proporciona API de programación integradas que facilitan la compilación de microservicios.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migración de aplicaciones existentes a Service Fabric
Un enfoque clave en Service Fabric es reutilizar código existente, que luego puede modernizarse con nuevos microservicios. Hay cinco fases para la modernización de aplicaciones. Puede empezar y detenerse en cualquiera de ellas. Las fases son:

1) Adopción de una aplicación monolítica tradicional  
2) Levantamiento y movimiento: use contenedores o ejecutables invitados para hospedar código existente en Service Fabric.  
3) Modernización: se agregan nuevos microservicios con código existente en contenedores.  
4) Innovación: divida la aplicación monolítica en microservicios únicamente en función de las necesidades.  
5) Transformación en microservicios: transforme aplicaciones monolíticas existentes o cree aplicaciones "vírgenes".

![Migración a microservicios][Image3]

Es importante destacar de nuevo que se puede **comenzar y detenerse en cualquiera de estas fases**. No nos vemos obligados a avanzar a la siguiente fase. Veamos ahora ejemplos de cada una de las fases.

**Levantamiento y movimiento**: gran cantidad de empresas están levantando y moviendo las aplicaciones monolíticas existentes a contenedores por dos motivos:

- Reducción de costos debidos a la consolidación y eliminación de hardware existente o a la ejecución de aplicaciones de mayor densidad. 
- Contrato de implementación coherente entre el desarrollo y las operaciones.

La reducción de costos es comprensible. Además, en Microsoft, se están usando contenedores en una gran cantidad de aplicaciones existentes con el único fin de ahorrar millones de dólares. La implementación coherente es difícil de evaluar, pero también es muy importante. Se dice que los desarrolladores tienen libertad para elegir la tecnología que mejor se ajuste a sus necesidades, pero las operaciones solo aceptarán una única forma de implementar y administrar esas aplicaciones. Evita que las operaciones se vean afectadas por la complejidad de muchas tecnologías diferentes o que obliguen a los desarrolladores a elegir solo algunas. Básicamente, cada aplicación se convierte en un contenedor de imágenes de implementación independientes.

Muchas organizaciones se detienen aquí. Ya tienen las ventajas de los contenedores y Service Fabric ofrece la experiencia de administración completa de implementación, actualizaciones, control de versiones, reversiones, supervisión de estado, etc.

**Modernización**: es la incorporación de nuevos servicios junto a código existente en contenedores. Si va a escribir otro código, lo mejor es optar por dar pequeños pasos en la ruta a los microservicios. Esto podría ser agregar un nuevo punto de conexión de la API de REST o una nueva lógica de negocios. De ese modo, inicia el camino de creación de microservicios y practica su desarrollo e implementación.

**Innovación**: ¿recuerda las cambiantes necesidades empresariales originales al principio de este artículo que determinan el enfoque de los microservicios? En esta fase, lo que hay que preguntarse es si estas necesidades se producen en la aplicación actual y, de ser así, si es necesario empezar a dividir el enfoque monolítico o innovar. Un ejemplo sería una base de datos que se convierte en un cuello de botella de procesamiento porque se usa como cola de flujo de trabajo. A medida que las solicitudes de flujo de trabajo aumentan es necesario distribuir el trabajo a escala. Por lo que para esa parte concreta de la aplicación que no se escala o hay que actualizar con más frecuencia, divídala en un microservicio e innove. 

**Transformación en microservicios**: aquí es dónde la aplicación se compone de (o descompone en) microservicios. Para llegar aquí, ha hecho el camino de los microservicios. Puede empezar aquí, pero hacerlo sin una plataforma de microservicios que le ayude resulta una importante inversión. 

### <a name="are-microservices-right-for-my-application"></a>¿Son los microservicios adecuados para mi aplicación?
Es posible. En nuestra experiencia, a medida que más y más equipos de Microsoft empezaron a crear para la nube por razones empresariales, muchos de ellos constataron las ventajas de adoptar el enfoque de microservicios. Por ejemplo, Bing lleva años desarrollando microservicios para búsqueda. Para otros equipos, el enfoque de microservicios era nuevo. Estos equipos descubrieron que se planteaban problemas difíciles de resolver fuera de sus principales campos de especialización. Por esta razón, Service Fabric ganó popularidad como tecnología preferida para la creación de servicios.

El objetivo de Service Fabric es reducir las complejidades que conlleva crear aplicaciones con un enfoque de microservicios, con el fin de que no haya que realizar muchos cambios costosos de diseño. Este enfoque consiste en comenzar poco a poco, escalar cuando sea necesario, dejar de usar algunos servicios, agregar otros nuevos y evolucionar con el uso del cliente. También sabemos que hay muchos otros problemas que aún deben resolverse para que los microservicios sean más accesibles para la mayoría de los desarrolladores. Los contenedores y el modelo de programación de actor son ejemplos de pasos pequeños tomados en esa dirección y estamos seguros de que surgirán más innovaciones para facilitar esta tarea.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Pasos siguientes
* [Información general sobre la terminología de Service Fabric](service-fabric-technical-overview.md)
* [Microservices: An application revolution powered by the cloud (Microservicios: una revolución en las aplicaciones con la tecnología de la nube)](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
