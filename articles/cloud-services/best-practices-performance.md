<properties 
	pageTitle="Prácticas recomendadas de rendimiento - Azure" 
	description="Obtenga información acerca de las prácticas recomendadas de rendimiento en Azure." 
	services="cloud-services, sql-database, storage, service-bus, virtual-network" 
	documentationCenter=".net" 
	authors="Rboucher" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2014" 
	ms.author="robb"/>

# Procedimientos recomendados para optimizar el rendimiento de las aplicaciones de Azure  #

En esta guía se incluyen recomendaciones acerca de qué procedimientos y técnicas se deben seguir para optimizar el rendimiento de las aplicaciones de Azure.

Tenga en cuenta que la utilización de Azure ofrece muchas ventajas: el rendimiento es solo una de ellas. Las recomendaciones de este documento se centran básicamente en el rendimiento. Existen otros en los que el rendimiento no es un factor tan importante: por ejemplo, es posible que desee aprovechar las ventajas que ofrece la descarga de la administración de hardware físico a Azure o la característica de "pago por uso". Este documento no pretende evaluar aquellas situaciones en las que el rendimiento no es prioritario.

## Información general ##
 El rendimiento puede definirse como ["la cantidad de trabajo útil llevado a cabo en comparación con el tiempo y los recursos empleados".](http://go.microsoft.com/fwlink/?LinkId=252650)


La definición tiene dos vertientes: las métricas y los recursos. Las métricas de rendimiento son cifras que se deben alcanzar para satisfacer los requisitos empresariales. Incluyen aspectos como el tiempo de respuesta, el rendimiento, la disponibilidad, etc. El rendimiento también incluye el nivel del uso de recursos necesario para alcanzar un nivel determinado de métricas de rendimiento. Dado que el coste suele ser un requisito empresarial y los recursos cuestan dinero, el rendimiento implica usar los recursos con la mayor eficiencia posible.

### Ciclo de vida del rendimiento
Es posible influir en el rendimiento en dos momentos diferentes del ciclo de vida de las aplicaciones:

- durante el diseño, cuando se toman decisiones fundamentales sobre la arquitectura que pueden afectar al rendimiento; y
- en el momento de la ejecución, cuando se identifican cuellos de botella, se lleva a cabo la supervisión y medición, etc.

Ambas fases son necesarias, aunque este documento se centra principalmente en la de diseño porque los errores de la arquitectura son difíciles de arreglar durante la ejecución.

#### Modelado de la aplicación
Es importante crear un modelo de las situaciones de cliente más importantes de la aplicación. Aquí “importante” quiere decir con mayor impacto en el rendimiento. Gracias a la identificación de estas situaciones y de las actividades necesarias de la aplicación, podrá realizar pruebas de concepto.

#### Pruebas de concepto del rendimiento ###

Es esencial llevar a cabo una comprobación exhaustiva del rendimiento durante el diseño y la implementación de la aplicación. Las aplicaciones de Azure constan de muchos componentes, algunos diseñados a medida y otros suministrados por Microsoft. Microsoft no puede probar el rendimiento de todas las combinaciones posibles de componentes. Por lo tanto, es fundamental que realice una comprobación exhaustiva y apropiada del rendimiento de su aplicación durante la implementación.

A continuación, teniendo en cuenta el modelo de la aplicación, debe realizar una prueba de concepto de la misma lo antes posible, así como una prueba de carga para validar la arquitectura, a fin de garantizar que la aplicación cumpla los requisitos de rendimiento en términos de escalabilidad y latencia. Es muy importante validar la arquitectura y suposiciones iniciales, dado que no es agradable implementar la aplicación y luego descubrir que esta no puede soportar la carga prevista. Visual Studio ofrece funcionalidad para llevar a cabo pruebas de carga descritas en [Prueba de carga de Visual Studio en la información general de Azure](http://www.visualstudio.com/get-started/load-test-your-app-vs).

### Diferencias del rendimiento en Azure ###

Las mejoras más drásticas del rendimiento que se pueden lograr en las aplicaciones de Azure pasan por el escalado horizontal y la división en particiones de los recursos. La creación de aplicaciones escalables en Azure requiere el aprovechamiento del escalado horizontal de los recursos por sus particiones físicas: bases de datos SQL, almacenamiento, nodos de proceso, etc. Esta creación de particiones permite la ejecución paralela de tareas de aplicación y es, por tanto, la base para el alto rendimiento, ya que Azure tiene los recursos de un centro de datos completo disponible y administra la creación de particiones físicas para usted. Para lograr este nivel de rendimiento global se requiere el uso de patrones de diseño de escalado horizontal adecuados.


Paradójicamente, a la vez que se logra un alto rendimiento para la aplicación en su conjunto, cada operación individual es menos eficaz en Azure que en su equivalente local, debido al aumento de la latencia de red, a la mayor fiabilidad derivada de las operaciones de conmutación por error, etc. Pero el paralelismo, habilitado por el uso correcto de los recursos de creación de particiones, compensa con creces el déficit de rendimiento individual.

### Actividades de diseño necesarias ###

Algunos factores de rendimiento cambian en función de la situación de la aplicación. El siguiente capítulo trata estos aspectos: ajuste de escala y creación de particiones de recursos, localización correcta de datos y optimización del uso de los servicios de Azure.


Más adelante, el capítulo aborda los factores de rendimiento que pertenecen a cualquier aplicación de Azure: latencia de red, conexiones transitorias, etc.

## Diseño para mejorar el rendimiento en un entorno en la nube ##

Debe tener en cuenta los siguientes aspectos dependientes de la situación a la hora de diseñar una aplicación de Azure o de migrar una aplicación local a Azure:

- Escalado horizontal y creación de particiones de recursos. Este es el mecanismo fundamental para lograr el paralelismo y, por lo tanto, la mejora de rendimiento. 
* Arquitectura de datos: el tipo de almacenamiento de datos que se va a utilizar para los distintos componentes de los datos de la aplicación 
* Optimizaciones de servicios de Azure concretos 

La principal ventaja de Azure en términos de rendimiento es que es posible escalar horizontalmente y dividir en particiones los recursos, lo que permite un gran paralelismo de las actividades. Esto resulta obvio si pensamos en una gran base de datos SQL de Azure, pero también se aplica a cualquier recurso que pueda convertirse en un cuello de botella.

Azure ofrece las siguientes opciones de almacenamiento de datos, y optar por la correcta tiene una gran repercusión sobre el rendimiento:

* Base de datos SQL de Azure 
* Almacenamiento en caché de Azure 
* Almacenamiento de tablas de Azure 
* Almacenamiento de blobs de Azure 
* Unidades de Azure 
* Colas de Azure 
* Mensajería asíncrona de Bus de servicio de Azure 
* Soluciones de almacenamiento de datos de gran tamaño como Hadoop 

Ya que la información específica varía, veremos cómo se utilizan estos tipos de almacenamiento en las siguientes situaciones:

* Servicio en la nube de Azure que utiliza una base de datos SQL 
* Servicio en la nube de Azure que hace un uso intensivo de colas de almacenamiento 
* Sitio web de Azure que utiliza MySQL como base de datos de back-end 
* Aplicaciones de datos de gran tamaño 
* Aplicaciones que utilizan una base de datos de back-end MySQL 

### Escenario: Base de datos SQL en un servicio en la nube ###

La mayoría de los principios que rigen el correcto diseño de una base de datos también se aplican a las Bases de datos SQL de Azure. Existe una cantidad ingente de documentos que describen cómo diseñar de manera efectiva una Base de datos SQL Server o de SQL Azure. Algunos de ellos son:

* [Aspectos básicos de modelado y diseño de base de datos](http://go.microsoft.com/fwlink/?LinkId=252675) 
* [Escaleras al diseño de base de datos](http://go.microsoft.com/fwlink/?LinkId=252676) 
* [Diseño de base de datos](http://go.microsoft.com/fwlink/?LinkId=252677) 

Hay dos actividades de diseño fundamentales que son diferentes en Azure:

* Localización correcta de los datos: esto puede requerir mover datos relacionales de blobs de Azure o tablas de Azure según corresponda. 
* Garantía de la máxima escalabilidad: deberá decidir si debe crear particiones de los datos y cómo se van a crear dichas particiones. 

#### Arquitectura de datos: Traslado de datos de una base de datos SQL ####

Algunos datos que normalmente se almacenan en un servidor local SQL Server deben moverse a otra ubicación de Azure.

##### Migración de datos a blobs de Azure ####

Los datos de blobs como imágenes o documentos no deben almacenarse en una base de datos SQL, sino en el almacenamiento de blobs de Azure. Aunque estos datos suelen almacenarse en un servidor local SQL Server, en la nube resulta mucho más económico utilizar el almacenamiento de blobs. Normalmente se reemplazan las claves externas que apuntan a los datos de blobs con identificadores del almacenamiento de blobs, a fin de preservar la capacidad de recuperar estos datos; asimismo, las consultas que hacen referencia a los datos deben modificarse.

##### Migración de tablas SQL al almacenamiento de tablas de Azure #####

A la hora de decidir si va a utilizar el almacenamiento de tablas de Azure, debe tener en cuenta el coste y el rendimiento. El almacenamiento de tablas resulta mucho más económico que almacenar los mismos datos en una base de datos SQL. Sin embargo, debe pensar con detenimiento hasta qué punto los datos usan las características relacionales de SQL como las uniones, el filtrado, las consultas, etc. Si los datos usan poco estas características, es un buen candidato para el almacenamiento en una tabla de Azure.

Un patrón de diseño habitual compatible con el almacenamiento de tablas consta de una tabla con muchas filas, como la tabla Customer de la base de datos de ejemplo AdventureWorks, en la que algunas columnas no las utilizan la mayoría de clientes, sino solo unos cuantos. También es un patrón de diseño habitual dividir las columnas para formar una segunda tabla (quizá denominada CustomerMiscellany) estableciendo una relación opcional 1 a 0 con la primera. Podría considerar la posibilidad de mover la segunda tabla al almacenamiento de tablas. Para ello, tendría que valorar la rentabilidad de esta migración, que depende del tamaño de la tabla y de los patrones de acceso.

Para obtener más información acerca del almacenamiento de tablas, consulte:

* [Almacenamiento de tablas de Azure y base de datos SQL de Azure: comparación y diferencias](http://msdn.microsoft.com/library/jj553018.aspx)
* [Aspectos de rendimiento del almacenamiento de las tablas de Azure que se deben tener en cuenta](http://go.microsoft.com/fwlink/?LinkId=252663) 
* [Almacenamiento de tablas de Azure y bases de datos SQL](http://go.microsoft.com/fwlink/?LinkId=252664) 
* [ del rendimiento mediante el procesamiento por lotes de las inserciones de almacenamiento de tablas de Azure](http://go.microsoft.com/fwlink/?LinkID=252665), donde se comentan algunos resultados de rendimiento. 
* [Guía de elasticidad y rendimiento de la Base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=221876) 

#### División de los datos en particiones ####

Uno de los recursos que con mayor frecuencia se dividen en particiones son los datos. Si va a crear un servicio en la nube de Azure, debe considerar la posibilidad de utilizar el particionamiento integrado de las bases de datos SQL, disponible a través de federaciones.

Para obtener información general acerca de las federaciones de las bases de datos SQL, consulte [Federaciones en Base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=252668).

##### Tareas de diseño de las federaciones SQL #####

Para utilizar las federaciones de las bases de datos SQL en un servicio en la nube de Azure, es necesario realizar algunos cambios en los principios de diseño clásicos. Sin embargo, la mayoría de los principios de diseño de las bases de datos locales de SQL Server son un punto de partida necesario para diseñar federaciones de Base de datos SQL. Existen dos tareas de diseño principales:

* decidir en qué basar las federaciones y 

* decidir dónde colocar las tablas sin federar.

Para decidir en qué basar las federaciones, debe examinar el esquema de la base de datos e identificar las agregaciones de tablas relacionadas. Una agregación es un conjunto de tablas entre las que se establecen relaciones 1 a varios a través de sus claves externas, excepto en el caso de la raíz de la agregación.

Por ejemplo, en la conocida base de datos de ejemplo AdventureWorks, una posible agregación sería el conjunto {Customer, Order, OrderLine y puede que algunas tablas más}. Otra agregación posible sería {Supplier, Product, OrderLine, Order}.

Cada agregación es un candidato para la federación. Debe evaluar dónde se prevé un aumento de tamaño y también examinar la carga de trabajo de la aplicación: las consultas que se "alineen correctamente" con el esquema de federación, es decir, que no requieran datos de más de un miembro de federación, se ejecutarán correctamente. Por el contrario, aquellas que no se ajusten bien a dicho esquema necesitarán lógica en el nivel de aplicación, ya que las bases de datos SQL actualmente no admiten las uniones entre bases de datos.

Para consultar un análisis de diseño de ejemplo en el que se examina la base de datos AdventureWorks para su posible federación y se muestran paso a paso las consideraciones implicadas en el diseño, vea [Enfoque de primero escala para el diseño de base de datos con federaciones: Parte 1 - Selección de federaciones y selección de la clave de federación](http://go.microsoft.com/fwlink/?LinkId=252671).

Una vez que decida qué tablas federar, debe agregar la clave principal de la tabla raíz de la agregación en forma de columna a cada una de las tablas relacionadas.

Asimismo, una vez decidido en qué tablas basar la federación, surge el problema de dónde colocar las tablas de referencia y otros objetos de base de datos. En el siguiente documento podrá encontrar información exhaustiva sobre este tema [Enfoque de primero escala para el diseño de base de datos con federaciones: Parte 2 - Anotación e implementación del esquema para federaciones](http://go.microsoft.com/fwlink/?LinkId=252672). El modo de realizar consultas más avanzadas se describe en la [Parte 2](http://go.microsoft.com/fwlink/?LinkId=252673).

                                            
##### División en particiones por parte del usuario #####

Hay varios ejemplos que muestran maneras de dividir los datos en particiones. Si decide no utilizar las federaciones para dividir la instancia de Base de datos SQL en particiones, debe elegir un método de división en particiones apropiado para la aplicación. A continuación se muestran algunos ejemplos:

* Encontrará información exhaustiva en [Creación de particiones con bases de datos SQL](http://go.microsoft.com/fwlink/?LinkId=252678), cuyo contenido se escribió antes del lanzamiento de las federaciones. 
* [SQL Server y biblioteca de particiones de Base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=252679) 

##### División de otros recursos en particiones #####

Es posible dividir en particiones otros recursos además de las bases de datos SQL. Por ejemplo, puede que desee dividir en particiones servidores de aplicaciones para asignarlos a bases de datos concretas. Asumamos que la aplicación contiene N servidores de aplicaciones y N bases de datos. Si cada servidor de aplicaciones tuviese acceso a todas las bases de datos, se producirían N al cuadrado conexiones de base de datos y en algunos casos podrían superarse los límites de Azure. Sin embargo, si restringe cada servidor de aplicaciones a unas cuantas bases de datos, se reducirá considerablemente el número de conexiones.

Dependiendo de la aplicación, es posible que pueda aplicar un razonamiento similar a otros recursos.


#### Almacenamiento en caché ####

El servicio de almacenamiento en caché de Azure ofrece memoria elástica distribuida para almacenar en caché información como el estado de la sesión de ASP.net o valores a los que se hace referencia habitualmente en las tablas de referencia de Base de datos SQL. Debido a que los objetos se almacenan en memoria distribuida, el rendimiento puede aumentar considerablemente. Dado que Azure administra la infraestructura de almacenamiento en caché, el coste de desarrollo que hay que asumir para implementarla es mínimo.

Planifique el aprovisionamiento de una capacidad de almacenamiento en caché suficiente para poder almacenar los objetos de acceso frecuente. En Base de datos SQL existen tablas de referencia frecuente que se utilizan para convertir los códigos numéricos en cadenas de caracteres descriptivas más largas. Estas tablas suelen incluir datos como nombres de países y ciudades, valores válidos de código postal, nombres de departamentos dentro de la empresa, etc. Para las tablas más pequeñas, puede que tenga sentido almacenar toda la tabla en memoria caché, para otras puede que solo almacene los valores usados con mayor frecuencia. La mejora de rendimiento se obtiene en las consultas multiunión relativas a estos datos: para cada valor que se encuentra en la caché, se guardan varios accesos al disco. El siguiente documento contiene información general y datos más detallados acerca del rendimiento y del almacenamiento en caché en Azure: [Introducción del servicio de almacenamiento en caché de Azure](http://go.microsoft.com/fwlink/?LinkId=252680). Una entrada de blog más reciente sobre este tema es [Aspectos de rendimiento que se deben tener en cuenta acerca del almacenamiento en caché de Windows #Azure](http://go.microsoft.com/fwlink/?LinkId=252681).

#### Escenario: Uso de colas en las aplicaciones de Azure ####

Un ejemplo de esta situación es el uso de StreamInsight para crear colas de mensajes que se procesarán más tarde.

Las colas de Azure se utilizan para enviar mensajes, desacoplar temporalmente los subsistemas y ofrecer equilibrio y nivelación de carga.

Azure ofrece dos tecnologías de colas alternativas: colas de almacenamiento de Azure y Bus de servicio.

Las colas de almacenamiento de Azure ofrecen un gran tamaño de cola, seguimiento del progreso y otras muchas características. Bus de servicio ofrece una función de publicación/suscripción, total integración con Windows Communication Foundation (WCF), detección automática de duplicados, garantía de entrega por orden de entrada, etc.

En el siguiente documento podrá encontrar una comparación completa y detallada de ambas tecnologías: [Colas de Azure y colas del Bus de servicio en Azure: comparación y diferencias](http://go.microsoft.com/fwlink/?LinkId=252682).

En el documento [Prácticas recomendadas para las mejoras de rendimiento mediante la mensajería asíncrona establecida del Bus de servicio](http://go.microsoft.com/fwlink/?LinkID=252683) encontrará información acerca del rendimiento de Bus de servicio.

#### Escenario: Aplicaciones de "Big Data" ####

Los datos de gran tamaño suelen ser un derivado de otro sistema o aplicación. Algunos ejemplos son:

* Registros web 

* Otros archivos de diagnóstico, auditoría y supervisión

* Registros sísmicos de empresas petroleras

* Datos de visitas y otra información dejada por los usuarios de Internet

Los datos de gran tamaño pueden identificarse mediante los criterios siguientes:

* Tamaño (normalmente cientos de terabytes o más) 

* Tipo: no relacionales, esquema variable, archivos de un sistema de archivos

Estos datos no suelen ser adecuados para procesarlos en una base de datos relacional.

Existen cuatro tipos principales de almacenamiento de datos NoSQL:

* Clave-valor 

* Documento

* Gráfico

* Columna-familia

Azure ofrece compatibilidad directa con Hadoop, además de permitir el uso de otras tecnologías. Para obtener información acerca del servicio HDInsight de Azure, consulte:

* [Big Data](../big-data.md) 
* [Servicio HDInsight de Azure](/documentation/services/hdinsight/)
* [Introducción al servicio HDInsight de Azure](../hdinsight-get-started.md)

En los siguientes documentos podrá encontrar información sobre los problemas relacionados con los diferentes métodos de almacenamiento NoSQL:

* [Familiarización con NoSQL en Azure](http://go.microsoft.com/fwlink/?LinkId=252729) 
* [AggregateOrientedDatabase](http://go.microsoft.com/fwlink/?LinkID=252731)
* [PolyglotPersistence](http://go.microsoft.com/fwlink/?LinkId=252732) 

#### Otras optimizaciones del rendimiento de servicios de Azure concretos ####

Muchos de los servicios de Azure presentan funciones y configuraciones propias que pueden afectar considerablemente al rendimiento, por lo que es necesario analizarlas.

##### Unidades de Azure #####

Puede simular el uso en una aplicación existente de la unidad de disco duro por parte de una unidad de Azure, que está respaldada por un blob de Windows y, por tanto, persiste en caso de error de la máquina.

##### Almacenamiento local #####

Aunque no persiste en caso de error de la máquina, puede utilizarse para almacenar información de acceso frecuente, o bien resultados intermedios que se utilizarán en otro lugar. El almacenamiento local resulta rentable, ya que su uso está libre de coste.

##### Servicio de control de acceso de Azure (ACS) #####

Los dos factores principales que afectan al uso de los recursos por parte del ACS, y en consecuencia al rendimiento, son el tamaño de token y el cifrado. Encontrará más información en el documento [Instrucciones de rendimiento de ACS.](http://go.microsoft.com/fwlink/?LinkId=252747).

##### Serialización #####

La serialización no es un componente obvio de la optimización del rendimiento, pero reducir el tráfico de red puede resultar significativo en algunos casos. En el documento [Aplicaciones web y serialización de Azure](http://go.microsoft.com/fwlink/?LinkId=252749) podrá encontrar demostraciones de reducciones que muestran cómo el tamaño puede variar el tamaño de la serialización según el protocolo.

Si la cantidad de datos que se van a mover supone un problema para el rendimiento, utilice la serialización más pequeña disponible. En caso de que el rendimiento de la serialización sea insuficiente, puede utilizar formatos de serialización personalizados o ajenos a Microsoft. Como siempre, es esencial realizar una prueba de concepto.


### Sitios web de Azure que utiliza MySQL ###

En los siguientes vínculos encontrará consejos sobre rendimiento para MySQL:

* Si busca *rendimiento* en [http://mysql.com](http://go.microsoft.com/fwlink/?LinkId=252775), aparecerán muchos recursos. 
* Los foros situados en el vínculo [http://forums.mysql.com/list.php?24](http://go.microsoft.com/fwlink/?LinkId=252776) son otro recurso interesante. 



## Diseño para sistemas compartidos ##

Azure está diseñado para ejecutar simultáneamente varias aplicaciones, que se replican en múltiples máquinas en caso de conmutación por error. Esto afecta al rendimiento de las aplicaciones de varias maneras:

* Conexiones transitorias 

* Limitaciones de recursos que restringen el uso máximo

* Latencia de red

* Ubicación física de los servicios

Estas consideraciones se aplican a todas las arquitecturas de aplicación, que están determinadas por la infraestructura física de los centros de datos de Azure. Si desea obtener información detallada, consulte la [Guía sobre rendimiento y elasticidad de las bases de datos SQL](http://go.microsoft.com/fwlink/?LinkID=252666).

### Latencia de red ###

Azure es una plataforma de recursos compartidos basada en servicios, lo que implica que normalmente se producen dos tipos de latencias o interrupciones. La primera es el tiempo necesario para realizar una solicitud y recibir una respuesta a través de Internet. Dado que esas solicitudes y respuestas pueden viajar por innumerables enrutadores antes de volver al cliente, los casos de agotamiento del tiempo de espera y desconexión son más frecuentes que en las redes fijas locales. La segunda es el tiempo que tarda un sistema de recursos compartidos como Azure en crear copias de seguridad de los datos para garantizar su durabilidad y en reemplazar y volver a enrutar las solicitudes dirigidas a instancias eliminadas. Estas latencias y errores nos ayudan a comprender qué compensaciones podemos hacer para lograr los requisitos de rendimiento de la aplicación. Las pruebas de carga en situaciones reales le proporcionarán más información sobre las latencias observadas.

Tenga en cuenta que probablemente haya más, ya que el centro de datos en la nube suele encontrarse más lejos que el servidor local.

### Ubicación física de los servicios ###

Si es posible, coloque diferentes niveles de nodo o capas de aplicación en el mismo centro de datos. De lo contrario, la latencia de red y el coste serán mayores.

Por ejemplo, coloque la aplicación web en el mismo centro de datos que la instancia de Base de datos SQL a la que obtiene acceso, en lugar de en un centro de datos diferente o en las instalaciones.

### Conexiones transitorias ###

La aplicación DEBE ser capaz de soportar que se descarten conexiones. Esto es algo inevitable e intrínseco a la arquitectura en la nube (p. ej., operaciones como reemplazar un nodo inactivo, dividir un miembro de una federación en una Base de datos SQL, etc.). Ahora mismo, el mejor marco para realizar estas conexiones es el [bloque de aplicación que controla los errores transitorios (The Transient Fault Handling Application Block)](http://go.microsoft.com/fwlink/?LinkID=236901).

### Limitaciones ###

En el ámbito de los servicios, los recursos pueden ser muy granulares, de tal forma que solo tiene que pagar por el uso que hace de los mismos. Sin embargo, todos los recursos tienen un tamaño, velocidad o capacidad mínimos garantizados (lo cual es importante si desea una base de datos más grande de un cierto tamaño, por ejemplo), aunque en algunos casos también existen límites externos para los servicios importantes. Dado que las aplicaciones de Azure se ejecutan en un entorno compartido con otras aplicaciones, Azure aplica varias limitaciones de recursos que se deben tener en cuenta. Si supera el límite de un recurso, toda solicitud posterior relativa a ese recurso dará lugar a una excepción.

### Capacidad física ###

Una parte necesaria de la planeación de rendimiento es la capacidad de planificación: si no proporciona suficiente almacenamiento de distintos tipos, es posible que la aplicación no se pueda ejecutar. Del mismo modo, un aprovisionamiento inadecuado de capacidad de memoria o de procesamiento puede ralentizar enormemente la ejecución de la aplicación.


Azure reduce drásticamente el esfuerzo que conlleva la planificación de la capacidad, ya que muchas actividades clásicas (en especial, la obtención y el aprovisionamiento de equipos) han cambiado radicalmente. En Azure la planificación de la capacidad ya no se centra en los elementos físicos de la informática, sino que alcanza un nivel mayor de abstracción y analiza qué cantidad se necesita de lo siguiente:

* Nodos informáticos 
* Almacenamiento de blobs 
* Almacenamiento de tablas 
* Colas, etc. 

Debido a la escalabilidad de Azure, las decisiones de capacidad inicial no pueden considerarse inmutables: incrementar y reducir los recursos de Azure es relativamente fácil. A pesar de ello, es importante planificar con precisión la capacidad, a fin de evitar que cuando se publique la aplicación se produzca un período de prueba y error en lo referente a la capacidad.

En el caso de aplicaciones cuyas necesidades de recursos varían drásticamente con el tiempo, se puede utilizar [el bloque de aplicación autoescalable (The Autoscaling Application Block)](http://go.microsoft.com/fwlink/?LinkId=252873). Este bloque permite establecer reglas para escalar y reducir verticalmente las instancias de rol. Hay dos tipos de reglas definidas:

* Reglas de restricción, que establecen la cantidad máxima/mínima de instancias según la hora del día 

* Reglas reactivas, que surten efecto cuando se produce alguna condición como el porcentaje de uso de la CPU

También es posible definir reglas personalizadas. Para obtener más información, consulte [The Autoscaling Application Block](http://go.microsoft.com/fwlink/?LinkId=252873).



La planificación de la capacidad es una especialidad completa en sí misma, y en este documento se asume que tiene experiencia en la materia. Para obtener información detallada acerca de la planificación de la capacidad en Azure, consulte [Planeación de la capacidad para colas y temas de Service Bus](http://go.microsoft.com/fwlink/?LinkId=252875).



## Supervisión y ajuste del rendimiento durante la ejecución ##

Incluso el diseño más cuidadoso no puede garantizar que no se produzca ningún problema de rendimiento durante la ejecución, por lo que es necesario supervisar el rendimiento de la aplicación de manera regular para comprobar que se alcancen las métricas de rendimiento requeridas y realizar correcciones en aquellos casos en que no se alcancen dichas métricas. Las aplicaciones bien diseñadas también están sujetas a imprevistos, como un aumento exponencial del uso o posibles cambios en el entorno de tiempo de ejecución, que pueden provocar problemas de rendimiento y requerir ajustes del diseño. A menudo, la identificación y solución de los cuellos de botella constituye una parte esencial de este proceso.


Para poder solucionar los problemas de rendimiento durante la ejecución es necesario un trabajo previo consistente en integrar funciones de registro y administración apropiada de las excepciones, de tal manera que siempre que surja una dificultad se pueda aplicar el proceso de solución de problemas. Para obtener información exhaustiva acerca de este tema, consulte [Procedimientos recomendados de solución de problemas para desarrollar aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkID=252876).

Existen herramientas disponibles para supervisar el rendimiento continuo de cada servicio de Azure. Además, las aplicaciones deben integrar funciones de registro que ofrezcan información detallada con la que poder solucionar los problemas generales y los relacionados con el rendimiento.

### Base de datos SQL ###

Tenga en cuenta que el Analizador SQL actualmente no se encuentra disponible en Azure. Existen otras maneras de obtener la información necesaria sobre el rendimiento, por ejemplo, una alternativa durante el desarrollo es realizar una prueba inicial en una versión local de la base de datos, donde el Analizador SQL está disponible.

También puede utilizar el comando de Transact-SQL SET STATISTICS y recurrir a SQL Server Management Studio para ver el plan de ejecución generado por una consulta, dado que las consultas eficientes sobre codificación son esenciales para el rendimiento. Para ver una discusión detallada, y una explicación paso a paso de cómo hacerlo, vea [Obtener información de rendimiento de la base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=252877). Otro enfoque interesante consiste en analizar el rendimiento entre [Base de datos SQL y SQL Server local](http://go.microsoft.com/fwlink/?LinkId=252878).

Estos dos documentos tratan sobre las vistas de administración dinámica:

* [Supervisión de la base de datos SQL mediante vistas de administración dinámica](http://go.microsoft.com/fwlink/?LinkId=236195) 
* [DMV útiles para que la base de datos SQL analice si pierde el SQL Profiler](http://go.microsoft.com/fwlink/?LinkId=252879) 

### Recursos y herramientas de análisis ###

Existen diversas herramientas ajenas a Microsoft que se pueden utilizar para analizar el rendimiento de Azure:

- [Cerebrata](http://go.microsoft.com/fwlink/?LinkId=252880) 
- [Pruebas de rendimiento de SQL Server y Base de datos SQL: Enzo SQL Baseline](http://enzosqlbaseline.codeplex.com/) 

Otros recursos

* [Guía de elasticidad y rendimiento de la Base de datos SQL](http://go.microsoft.com/fwlink/?LinkID=252666) 
* [Base de datos SQL](http://go.microsoft.com/fwlink/?LinkId=246930) 
* [Almacenamiento](http://go.microsoft.com/fwlink/?LinkId=246933) 
* [Redes](http://go.microsoft.com/fwlink/?LinkId=252882) 
* [Bus de servicio](http://go.microsoft.com/fwlink/?LinkId=246934) 
* [Planificación de Azure:un guía posterior a la decisión para la integración de Azure en su entorno](http://go.microsoft.com/fwlink/?LinkId=252884) 
 

<!---HONumber=62-->