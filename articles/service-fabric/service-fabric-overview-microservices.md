<properties
   pageTitle="Descripción de los microservicios | Microsoft Azure"
   description="Información general sobre por qué la creación de aplicaciones de nube con un enfoque de microservicios es importante para el desarrollo de aplicaciones modernas y cómo Azure Service Fabric proporciona una plataforma para lograrlo"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/18/2015"
   ms.author="mfussell"/>

# ¿Por qué usar un enfoque de microservicios para crear aplicaciones?
Como desarrolladores de software, la forma en que nos planteamos la factorización de una aplicación en sus distintos componentes no es ninguna novedad. Se trata del paradigma central de la orientación a objetos, las abstracciones de software y la creación de componentes. En la actualidad, esta factorización tiende a adoptar la forma de clases e interfaces entre bibliotecas compartidas y niveles de tecnología, normalmente mediante un enfoque por niveles con un almacén back-end, lógica de negocios en el nivel intermedio y una interfaz de usuario front-end. Lo que cambió en los últimos años es que, como desarrolladores, estamos creando aplicaciones distribuidas para la nube impulsados por las empresas.

Las necesidades cambiantes de las empresas son:

- Necesidad de crear y operar un servicio a escala para permitir que el cliente disponga de mayor alcance, por ejemplo, hacia nuevas regiones geográficas o sin tener que realizar la implementación en ubicaciones de los clientes.
- Entrega más rápida de características y funciones para poder responder a las demandas de los clientes de forma ágil.
- Mejor utilización de los recursos para reducir los costos

Todas estas necesidades empresariales afectan a *cómo* creamos aplicaciones.

## Enfoque de diseño monolítico en comparación con el de microservicios
Todas las aplicaciones evolucionan con el tiempo. Las aplicaciones que triunfan evolucionan por ser útiles a sus usuarios. Las que fracasan no evolucionan y terminan por entrar en desuso. La pregunta relevante es: ¿Cuánto sabe acerca de los requisitos actuales y cuáles cree que serán en el futuro? Por ejemplo, si está creando una aplicación de informes para un departamento y sabe que permanecerá dentro del ámbito de su empresa y que los informes serán de corta vigencia, el enfoque elegido será diferente al aplicable cuando, por ejemplo, se crea un servicio para entregar contenido de vídeo a decenas de millones de clientes. A veces, terminar un servicio como prueba de concepto es un factor determinante, ya que se es consciente de que el diseño de la aplicación se puede modificar más adelante. No tiene mucho sentido volcarse en la ingeniería de algo que no se va a usar nunca. Es el equilibrio habitual en ingeniería. Por otra parte, cuando las empresas hablan sobre la creación de aplicaciones para la nube, lo que esperan es crecimiento y uso. El problema es que el crecimiento y la escala son impredecibles, así que nos gustaría poder crear un prototipo rápidamente pero, al mismo tiempo, sabemos que en el futuro tendremos que enfrentarnos al éxito de la aplicación. Este es el enfoque Lean Startup: compilar, medir, aprender e iterar.

En la época del cliente-servidor, tendíamos a centrarnos en la creación de aplicaciones en niveles empleando tecnologías específicas en cada nivel. A partir de esto, surgió el término aplicación "monolítica", ya que las interfaces solían estar entre los niveles y normalmente, dentro del nivel, se usaba un diseño más estrechamente acoplado entre los componentes, es decir, los desarrolladores diseñaban y factorizaban con clases compiladas en bibliotecas y las vinculaban entre sí en algunos archivos EXE y DLL. Existen ventajas para este enfoque de diseño monolítico. Las aplicaciones pueden ser más fáciles de diseñar, las llamadas entre componentes son más rápidas porque se suelen realizar a través de IPC, todo el mundo prueba un único producto y esto es por lo general más eficaz con los recursos personales. La desventaja es que la aplicación está estrechamente acoplada dentro de los niveles por capas, no se pueden escalar los componentes individuales, si necesita realizar correcciones o actualizaciones hay que esperar a que otras personas finalicen las pruebas y la agilidad se consigue con mayor dificultad.

Los microservicios abordan estas desventajas y se adaptan mejor a los requisitos empresariales que describí antes, aunque también presentan tanto ventajas como inconvenientes. Las ventajas de los microservicios son que cada uno suele encapsular funcionalidades empresariales más simples y se pueden escalar o reducir verticalmente, probar, implementar y administrar de forma independiente. Una importante ventaja del enfoque de microservicios es que los equipos de desarrollo suelen estar más orientados hacia el escenario empresarial que hacia la tecnología, algo que el enfoque por niveles fomentaba. En la práctica esto significa que los equipos más pequeños desarrollan el microservicio partiendo de un escenario del cliente, usando cualquier tecnología de su elección. Es decir, la organización no necesita estandarizar la tecnología para mantener sus monolitos y los equipos individuales propietarios de servicios pueden hacer lo que más les convenga en función de la experiencia del equipo o lo que sea más adecuado para el problema que se está intentando resolver con el servicio. Por supuesto, en la práctica, tener un conjunto de tecnologías recomendadas es preferible, por ejemplo, al elegir un almacén NoSQL determinado o un marco de trabajo de aplicación web. La desventaja de los microservicios es tener que administrar mayor número de entidades independientes, trabajar con implementaciones más complejas y realizar el control de versiones, tener más tráfico de red entre los microservicios y las latencias de red correspondientes. Tener una gran cantidad de servicios muy granulares con mucho tráfico entre ellos se puede convertir en una pesadilla de rendimiento y, sin herramientas que ayuden a ver estas dependencias, resulta difícil obtener una visión general de todo el sistema. En última instancia, los estándares son lo que permite que el enfoque de microservicios funcione, pues establecen un acuerdo sobre cómo llevar a cabo la comunicación y tolerar únicamente lo que se necesita de un servicio, al contrario que los contratos rígidos. Es importante definir estos contactos en el diseño por adelantado porque los servicios se van a actualizar de forma independiente entre sí. Otra descripción acuñada al diseñar con un enfoque de microservicios es "SOA específica".


***En su forma más simple, el enfoque de diseño de microservicios consiste en una federación desacoplada de servicios, con cambios independientes para cada uno y estándares acordados para la comunicación.***


A medida que se producen más aplicaciones de nube, más personas están descubriendo que esta descomposición de la aplicación completa en servicios independientes centrados en el escenario es un mejor enfoque a largo plazo.
## Comparación entre los enfoques de desarrollo de aplicaciones

![Plataforma de Service Fabric][Image1]

(1) Una aplicación monolítica contiene funcionalidad específica del dominio y normalmente se divide en niveles funcionales como Web, negocios y datos.

(2) Para escalar las aplicaciones monolíticas, se clonan en varios servidores, máquinas virtuales o contenedores.

(3) Una aplicación de microservicio separa la funcionalidad entre distintos servicios más pequeños.

(4) Este enfoque se escala horizontalmente mediante la implementación independiente de cada servicio, con la creación de instancias de estos servicios en servidores, máquinas virtuales y contenedores.


El diseño según un enfoque de microservicios no es una panacea para todos los proyectos, aunque se adapta mejor a los objetivos de negocio descritos antes. Además, comenzar con un enfoque monolítico puede ser aceptable si se sabe que después habrá oportunidad de modificar el código según un diseño con microservicios, si es necesario. Es más frecuente contar ya con una aplicación monolítica y poder dividirla lentamente en fases empezando por las áreas funcionales que deban ser más escalables o ágiles.

En resumen, el enfoque de microservicios consiste en crear su aplicación con muchos servicios más pequeños que se ejecutan en contenedores implementados en un clúster de máquinas en el que cada servicio es desarrollado por un equipo más reducido que se concentra un escenario. Además, las pruebas, el control de versiones, la implementación y el escalado de cada servicio se realizan de forma independiente, por lo que la aplicación puede evolucionar como un todo.

## ¿Qué es un microservicio?

Existen diferentes definiciones de los microservicios y las búsquedas en Internet proporcionan muchos recursos de calidad que explican su punto de vista y la definición. Sin embargo, la mayoría coincide en las siguientes características:

- Encapsulan un escenario de cliente o negocio. ¿Cuál es el problema que va a solucionar?
- Los desarrolla un pequeño equipo de ingeniería.
- Se pueden escribir en cualquier lenguaje de programación y pueden usar cualquier marco. 
- Constan de código y, opcionalmente, de un estado cuyo control de versiones, implementación y escalado se realizan de forma independiente.
- Interactúan con otros microservicios por medio de protocolos e interfaces bien definidos. 
- Tienen un nombre único (URL) que puede usarse para resolver su ubicación.
- Siguen siendo coherentes y estando disponibles en el caso de errores.

Todo esto se puede resumir en:

***Las aplicaciones de microservicios están compuestas por servicios pequeños centrados en el cliente, escalables y con control de versiones independiente que se comunican entre sí mediante protocolos estándar con interfaces bien definidas***


Ya tratamos los dos primeros puntos en la sección anterior y ahora ampliaremos y aclararemos los demás.

### Se pueden escribir en cualquier lenguaje de programación y pueden usar cualquier marco.
Como desarrolladores, deberíamos disponer de la libertad de elegir cualquier lenguaje o marco que queramos según nuestras habilidades o las necesidades del servicio. En algunos servicios, se podrían valorar los beneficios de rendimiento de C++ por encima de todo, mientras que en otros la facilidad del desarrollo administrado en C# o Java podría ser más importante. En algunos casos, es posible que se tenga que usar una biblioteca de terceros, una tecnología de almacenamiento de datos o medios para exponer el servicio a los clientes.

Una vez seleccionada la tecnología, pasamos a la administración operativa o del ciclo de vida y el escalado del servicio.

### Código y estado cuyo control de versiones, implementación y escalado se realizan de forma independiente  

Decida como decida escribir sus microservicios, cada uno se debería implementar, actualizar y escalar de forma independiente, tanto para el código como para, opcionalmente, el estado. Se trata en realidad de uno de los problemas más difíciles de resolver porque depende de la selección de tecnologías y, en lo que respecta al escalado, de comprender cómo particionar el código y el estado. Cuando el código y el estado usan tecnologías diferentes (algo frecuente en la actualidad), los scripts de implementación para su microservicio necesitan poder hacer frente al escalado de ambos. También tienen relevancia la agilidad y la flexibilidad, para que sea posible actualizar algunos de los microservicios sin necesidad de actualizarlos todos a la vez. Si volvemos un momento a la comparación entre el enfoque monolítico y el de microservicios, el diagrama siguiente muestra las diferencias entre enfoques en cuanto al almacenamiento del estado.

### Almacenamiento de estado en los distintos estilos de aplicación
![Plataforma de Service Fabric][Image2]

***A la izquierda, el enfoque monolítico con una base de datos única y niveles de tecnologías específicas.***

***A la derecha, el enfoque de microservicios: un gráfico de microservicios interconectados donde el estado normalmente tiene como ámbito el microservicio y se usan diversas tecnologías.***

En un enfoque monolítico, normalmente hay una base de datos única que la aplicación usa. La ventaja es que esta es la única ubicación y resulta fácil de implementar. Cada componente puede tener una única tabla para almacenar su estado. La dificultad reside en que los equipos deben ser estrictos en la separación del estado y es inevitable que exista la tentación de simplemente agregar una nueva columna a una tabla existente del cliente, realizar una combinación entre tablas y, por lo general, crear dependencias en el nivel de almacenamiento. Una vez hecho esto, cualquier cosa es posible cuando se escalen los componentes individuales. En el enfoque de microservicios, cada servicio administra y almacena su propio estado, lo que significa que se encarga de escalar tanto el código como el estado para las demandas del servicio. La desventaja surge cuando es necesario crear vistas, o consultas, de datos de sus aplicaciones, ya que ahora tendrá que consultar estos almacenes de estado dispares. Por lo general, esto se resuelve teniendo un microservicio independiente que genera una vista de una colección de microservicios. Si debe realizar varias consultas ad hoc en los datos, cada microservicio debería escribir sus datos en un servicio de almacenamiento de datos para el análisis sin conexión.


El control de versiones es específico de la versión implementada del microservicio y es necesario para poder implantar y ejecutar en paralelo varias versiones diferentes. El control de versiones sirve para los escenarios en que una versión más reciente de un microservicio produce un error durante la actualización y se necesita revertir a la versión anterior. El otro escenario para el control de versiones es la realización de pruebas de estilo A/B en las que distintos usuarios experimentan diferentes versiones del servicio. Por ejemplo, es habitual actualizar un microservicio para un conjunto específico de clientes para probar nueva funcionalidad, antes de lanzarlo más ampliamente. Después de la administración del ciclo de vida de los microservicios, pasamos a la comunicación entre ellos.


### Interactúan con otros microservicios por medio de protocolos e interfaces bien definidos.

Sobre este tema, basta fundamentalmente con leer la amplia documentación sobre SOA que se publicó en los últimos diez años, ya que en gran parte estuvo orientada a los patrones de comunicación. Por lo general, en la actualidad, esto básicamente consiste en usar un enfoque de REST con protocolos HTTP y TCP, y XML o JSON como formato de serialización. Desde el punto de vista de la interfaz, esto supone adoptar el enfoque de diseño web. Pero no hay nada que impida usar protocolos binarios o sus propios formatos de datos; simplemente tiene que estar preparado para que sus microservicios sean más difíciles de usar si están disponibles abiertamente.

### Tienen un nombre único (URL) que puede usarse para resolver su ubicación

Recuerde que repetimos varias veces que el enfoque de microservicios es como la Web. Al igual que la Web, su microservicio debe ser direccionable siempre que se esté ejecutando. Si está pensando en equipos y cuál está ejecutando un determinado microservicio, va muy descaminado. De la misma manera que DNS resuelve una dirección URL determinada en un equipo concreto, su microservicio debe tener un nombre único para que se pueda detectar dónde se encuentra actualmente. Por lo tanto, los microservicios necesitan nombres direccionables que los hagan independientes de la infraestructura en que se ejecutan. Por supuesto, esto implica que existe una interacción entre cómo se implementa el servicio y cómo se detecta, ya que debe haber un Registro de servicio. También debe haber una interacción entre el momento en que se producen errores en el equipo y lo que sucede con el microservicio para que el servicio de Registro pueda indicar dónde se está ejecutando en ese momento. Esto nos lleva al siguiente tema, sobre la resistencia y la coherencia.
 
### Siguen siendo coherentes y estando disponibles en el caso de errores.

Enfrentarse a errores inesperados es uno de los problemas más difíciles de resolver, especialmente en un sistema distribuido. Gran parte del código que escribimos como desarrolladores sirve para controlar excepciones; también dedicamos gran parte de nuestro tiempo a probarlo. Pero el problema es más complicado que escribir código para controlar errores; ¿qué sucede cuando se produce un error en el equipo donde se ejecuta el microservicio? No solo se debe detectar este error de microservicio (un problema difícil en sí), sino que se necesita algo para reiniciar el microservicio. Es necesario que el microservicio sea resistente a los errores y posea la capacidad de reiniciarse con frecuencia en otro equipo por motivos de disponibilidad. También es importante qué estado se guardó en nombre del microservicio, desde dónde se puede recuperar este estado y si es capaz de reiniciarse correctamente. Es decir, se precisa resistencia en el proceso (es decir, el proceso se reinicia) y también en el estado o los datos (no se pierden datos y se conserva su coherencia).

Los problemas de resistencia se agravan durante otros escenarios, como cuando se producen errores durante la actualización de una aplicación. El microservicio, en colaboración con el sistema de implementación, necesita no solo recuperarse, sino también decidir si puede proseguir a la versión más reciente o, en su lugar, revertir a las versiones anteriores para mantener un estado coherente. Se deben tener en cuenta cuestiones tales como si hay equipos suficientes para seguir adelante o cómo recuperar versiones anteriores del microservicio. Para esto, se requiere que el microservicio emita información de estado que permita tomar estas decisiones.

### Notifica el estado y diagnósticos

Aunque pueda resultar obvio y se pase por alto a menudo, es esencial que un microservicio notifique su estado y diagnósticos; en caso contrario, hay poca información desde una perspectiva de las operaciones. La dificultad reside en ver la correlación de eventos de diagnóstico en un conjunto de servicios independientes en que cada uno registra de forma independiente y tener en cuenta el sesgo de los relojes de los equipos para entender el orden de eventos. De la misma manera que se interactúa con un microservicio por medio de protocolos y formatos de datos acordados, surge la necesidad de estandarizar la forma en que se registran los eventos de estado y diagnóstico que al final terminan en un almacén de eventos que se puede consultar y ver. En un enfoque de microservicios, resulta esencial que los distintos equipos se pongan de acuerdo en un formato de registro único, ya que se necesita un enfoque coherente para ver los eventos de diagnóstico en la aplicación como un todo.

El estado es diferente de los diagnósticos. Con estado, me refiero a que el microservicio notifica su estado actual para que se pueden realizar acciones. La más evidente es colaborar con los mecanismos de actualización e implementación para mantener la disponibilidad. Por ejemplo, un servicio puede tener un estado incorrecto debido a un bloqueo de proceso o al reinicio del equipo y aún así seguir operativo. Lo último que necesita es empeorar la situación realizando una actualización cuando lo mejor es investigar en primer lugar o dejar tiempo al microservicio para que se recupere. Por tanto, los eventos de estado de un microservicio nos permiten tomar decisiones informadas y de hecho ayudan a crear servicios de recuperación automática.

## Service Fabric como plataforma de microservicios

Service Fabric se originó en la transición de Microsoft, que pasó de ofrecer productos empaquetados, que solían ser de estilo monolítico, a prestar servicios, y se basa principalmente en la experiencia de crear y operar servicios grandes como Base de datos SQL de Azure, DocumentDB y otros servicios principales de Azure. Abordamos enteramente las necesidades de los negocios en cuanto a escalabilidad, agilidad y equipos independientes, y dejamos que la plataforma evolucionara con el tiempo a medida que la fueron adoptando cada vez más servicios. Algo importante es que Service Fabric tenía que ejecutarse en cualquier lugar, no solo en Azure, sino también en implementaciones independientes de Windows Server.

***El objetivo de Service Fabric es resolver los problemas dificultosos que conlleva crear y ejecutar un servicio, como los errores o las actualizaciones, usando de forma eficiente los recursos de infraestructura para que los equipos puedan resolver problemas de negocios con un enfoque de microservicios.***

Service Fabric proporciona dos amplias áreas para ayudarlo a crear aplicaciones con un enfoque de microservicios.

- Una plataforma compuesta por un conjunto de servicios del sistema que se ocupan de las implementaciones, las actualizaciones, la detección y el reinicio de servicios con errores, la detección de dónde se están ejecutando actualmente los servicios , la administración del estado, la supervisión del estado, etc. De hecho, estos servicios del sistema hacen posibles muchas de las características de microservicios descritas antes.

-  API de programación, o marcos, que lo ayudan a crear aplicaciones como microservicios. Las API de programación proporcionadas se denominan [Reliable Actors y Reliable Services](service-fabric-choose-framework.md). Por supuesto, puede usar cualquier código de su elección para generar el microservicio pero, con estas API, no solo facilita el trabajo, sino que se integran con la plataforma en un nivel más profundo, por lo que, por ejemplo, puede obtener información de estado y diagnósticos o puede aprovechar la alta disponibilidad integrada.

***Service Fabric es independiente de la forma en que cree el servicio y puede usar cualquier tecnología. A cambio, proporciona API integradas de programación que facilitan enormemente la compilación de microservicios.***

### ¿Son los microservicios adecuados para mi aplicación?

Es posible. En nuestra experiencia, a medida que se encargaba a más y más equipos de Microsoft que crearan para la nube por razones empresariales, muchos de ellos constataron las ventajas de adoptar el enfoque de microservicios. Por ejemplo, Bing lleva años haciendo esto con la búsqueda. Para otros equipos, esto fue muy novedoso y se toparon con problemas complicados que necesitaban solución, algo que no estaba entre sus fortalezas principales. Por esta razón, Service Fabric ganó popularidad como tecnología preferida para la creación de servicios.

El objetivo de Service Fabric es reducir las complejidades de la creación de aplicaciones con un enfoque de microservicios, para que no haya que realizar tantos cambios costosos de diseño. Comience poco a poco, escale cuando sea necesario, deje de usar servicios, agregue otros nuevos, evolucione con el uso del cliente, esa es la idea. Somos conscientes, de todos modos, de que hay muchos otros problemas que aún hay que resolver para que los microservicios sean más accesibles para la mayoría de los desarrolladores. Los contenedores y el modelo de programación de actor son ejemplos de pequeños pasos en esa dirección y estamos seguros de que surgirán más innovaciones para facilitar esta tarea. <!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

* Para obtener más información: 
	* [Información general de Service Fabric](service-fabric-overview.md)
	* [Introducción técnica](service-fabric-technical-overview.md)
* Configuración del [entorno de desarrollo](service-fabric-get-started.md) de Service Fabric
* Selección de un [marco de modelo de programación](service-fabric-choose-framework.md) para el servicio

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png

<!---HONumber=Nov15_HO4-->