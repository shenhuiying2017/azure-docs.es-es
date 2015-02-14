<properties 
	pageTitle="Obtenga información acerca de Apache Storm en HDInsight (hadoop)" 
	description="Aprenda a usar Apache Storm en HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Información general de Apache Storm en HDinsight

## ¿Qué es Storm?

[Apache Storm][apachestorm] es un sistema de cálculo de código abierto, distribuido y con tolerancia a errores que permite procesar los datos en tiempo real. Las soluciones de Storm pueden proporcionar también procesamiento de datos garantizado, con la posibilidad de reproducir los datos que no se han procesado correctamente la primera vez.

## ¿Qué es Storm en HDInsight?

Storm se ofrece como un clúster administrado integrado en el entorno de Azure, donde se puede usar como parte de una solución de Azure más amplia. Por ejemplo, Storm puede consumir datos de servicios como colas de bus de servicio o centros de eventos, y usar Sitios web o Servicios en la nube para proporcionar visualización de los datos. Los clústeres de Storm se pueden configurar también en una red virtual de Azure, lo cual reduce la latencia en la comunicación con otros recursos de la misma red virtual y puede permitir también la comunicación segura con los recursos de un centro de datos privado.

Para empezar a usar Storm, consulte [Introducción al uso de Storm con HDInsight][gettingstarted].

## ¿Cómo se procesan los datos de Storm en HDInsight?

Un clúster de Storm procesa **topologías** en lugar de los trabajos de MapReduce con los que podría estar familiarizado en HDInsight o Hadoop. Un clúster de Storm contiene dos tipos de nodos: nodos principales que ejecutan **Nimbus** y nodos de trabajo que ejecutan **Supervisor**.

* **Nimbus**: parecido a JobTracker de Hadoop, es responsable de distribuir código a través del clúster, de asignar tareas a las máquinas y de supervisar los errores. HDInsight proporciona dos nodos Nimbus, así que no hay un solo punto de error en un clúster de Storm.

* **Supervisor**: el supervisor de cada nodo de trabajo es responsable de iniciar y detener los **procesos de trabajo** en el nodo.

* **Proceso de trabajo**: un proceso de trabajo ejecuta un subconjunto de una **topología**. Una topología en ejecución se distribuye entre muchos procesos de trabajo a través del clúster.

* **Topología**: define un gráfico de cálculo que procesa **secuencias** de datos. A diferencia de los trabajos de MapReduce, las topologías se ejecutan hasta que las detiene.

* **Secuencia**: una colección independiente de **tuplas**. Los **spouts** y **bolts** producen secuencias y los **bolts** las consumen.

* **Tupla**: una lista con nombres de valores escritos de forma dinámica.

* **Spout**: consume datos de un origen de datos y emite una o varias **secuencias**.

	> [AZURE.NOTE] En muchos casos, los datos se leen de una cola como Kafka, las colas de bus de servicio de Azure o los centros de eventos. La cola garantiza que los datos persistan en caso de una interrupción.

* **Bolt**: consume **secuencias**, realiza el procesamiento en **tuplas** y puede emitir **secuencias**. Los bolts son responsables también de escribir datos en el almacenamiento externo, como una cola, HBase de HDInsight, un blob u otro almacén de datos.

* **Thrift**: Apache Thrift es un marco de software para el desarrollo de servicios escalables en distintos lenguajes Así, permite crear servicios que funcionan entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk y otros lenguajes.

	* **Nimbus** es un servicio de Thrift y una **topología** es una definición de Thrift, por lo que es posible desarrollar topologías mediante diversos lenguajes de programación. 

Para obtener más información sobre los componentes de Storm, consulte el [tutorial de Storm][apachetutorial] en apache.org.

## Escenarios: ¿Cuáles son los casos de uso de Storm?

A continuación se indican algunos de los supuestos comunes en los que podría usar Apache Storm. Para obtener información sobre escenarios del mundo real, lea [cómo las empresas usan Storm][poweredby].

### Análisis en tiempo real

Dado que Storm procesa secuencias de datos en tiempo real, resulta muy adecuado para análisis de datos que implican la búsqueda de eventos o patrones específicos, y la reacción a estos, en las secuencias de datos a medida que llegan. Por ejemplo, una topología de Storm podría supervisar los datos de sensor para determinar el estado del sistema, y generar mensajes SMS para avisarle cuando se produzca un patrón específico.

### Extract Transform Load (Extraer, transformar y cargar, ETL)

ETL se puede considerar casi como un efecto secundario del procesamiento de Storm. Por ejemplo, si está realizando la detección de fraudes a través de análisis en tiempo real, ya está ingiriendo y transformando datos. Puede que también desee tener un bolt para almacenar los datos en HBase, Hive u otro almacén de datos para su uso en futuros análisis.

### RPC distribuido

RPC distribuido es un patrón que se puede crear con Storm. Se pasa una solicitud a Storm, el cual luego distribuye el cálculo entre varios nodos y finalmente devuelve una secuencia de resultados al cliente que espera.

Para obtener más información sobre RPC distribuido y el cliente DRPC que se proporciona con Storm, consulte [RPC distribuido](https://storm.incubator.apache.org/documentation/Distributed-RPC.html).

### Aprendizaje automático en línea

Storm se puede usar con soluciones de aprendizaje automático que previamente se han entrenado mediante procesamiento por lotes, como puede ser una solución basada en Mahout. Sin embargo, su modelo de cálculo distribuido genérico también abre la puerta a soluciones de aprendizaje automático basadas en secuencias. Por ejemplo, el [proyecto Scalable Advanced Massive Online Analysis (SAMOA)][samoa] es una biblioteca de aprendizaje automático que emplea el procesamiento por secuencias y puede funcionar con Storm.

## ¿Qué lenguajes de programación se pueden usar?

El clúster de Storm en HDInsight proporciona compatibilidad inmediata con .NET, Java y Python. Si bien Storm [es compatible con otros lenguajes](https://storm.incubator.apache.org/about/multi-language.html), para muchos de ellos será necesario instalar un lenguaje de programación adicional en el clúster de HDInsight, además de otros cambios en la configuración. 

### .NET

SCP es un proyecto que permite a los desarrolladores de .NET diseñar e implementar una topología (lo que incluye spouts y bolts). La compatibilidad con SCP se proporciona de forma predeterminada con los clústeres de Storm.

Para obtener más información sobre el desarrollo con SCP, consulte [Desarrollo de aplicaciones de procesamiento de datos de transmisión por secuencias con SCP.NET y C# en Storm en HDInsight](/es-es/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application).

### Java

La mayoría de los ejemplos de Java que encuentre serán Java normal o Trident. Trident es una abstracción de alto nivel que facilita la creación de elementos como uniones, agregaciones, agrupaciones o filtrados. Sin embargo, Trident actúa sobre lotes de tuplas, donde una solución de Java sin procesar procesará una secuencia una tupla cada vez.

Para obtener más información sobre Trident, consulte el [tutorial de Trident](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) en apache.org.

Para obtener ejemplos de topologías de Java y Trident sin procesar, consulte el directorio **%storm_home%\contrib\storm-starter** en su clúster de Storm.

## ¿Cuáles son algunos de los patrones de desarrollo comunes?

### Procesamiento de mensajes garantizado

Storm puede proporcionar diferentes niveles de procesamiento de mensajes garantizado. Por ejemplo, una aplicación básica de Storm puede garantizar un procesamiento una vez al menos, mientras que Trident puede garantizar exactamente un procesamiento una vez.

Para obtener más información, consulte las [garantías en el procesamiento de los datos](https://storm.apache.org/about/guarantees-data-processing.html) en apache.org

### BasicBolt

El patrón de leer una tupla de entrada, emitir cero o más tuplas y luego confirmar la tupla de entrada inmediatamente al final del método ejecutado es tan común que Storm proporciona la interfaz [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizarlo.

### Combinaciones

La unión de dos secuencias de datos variará según la aplicación. Por ejemplo, puede unir cada tupla de varias secuencias en una nueva, o unir únicamente lotes de tuplas de una ventana específica. De cualquier modo, la unión se puede efectuar mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que es una forma de definir cómo las tuplas se enrutan a los bolts.

En el siguiente ejemplo de Java, se usa fieldsGrouping para enrutar las tuplas que se originan desde los componentes "1", "2" y "3" hasta el bolt **MyJoiner**.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### Procesamiento por lotes

El procesamiento por lotes se puede efectuar de varias formas. Con una topología básica de Java de Storm, podría usar un simple contador para procesar por lotes un número X de tuplas antes de emitirlas, o bien usar un mecanismo de temporización interno conocido como tupla de señalización para emitir un lote cada X segundos.

Para ver un ejemplo de uso de tuplas de señalización, consulte [Análisis de los datos de sensor con Storm y HDInsight](/es-es/documentation/articles/hdinsight-storm-sensor-data-analysis.md).

Si está usando Trident, se basa en el procesamiento por lotes de tuplas.

### Almacenamiento en caché

El almacenamiento en caché de memoria interna se usa a menudo como mecanismo para acelerar el procesamiento, dado que mantiene en memoria los activos usados con mayor frecuencia. Dado que una topología se distribuye entre varios nodos y varios procesos dentro de cada nodo, debe considerar el uso de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para garantizar que las tuplas que contienen los campos que se usan para la búsqueda en caché se enruten siempre al mismo proceso. De esta forma se evita la duplicación de las entradas de la caché entre los procesos.

### Transmisión por secuencias del N principal

Cuando la topología depende del cálculo de un valor N  'top', como los 5 principales trending topics en Twitter, debe calcular el valor N principal en paralelo y luego combinar el resultado de esos cálculos en un valor global. Esto se puede hacer mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para enrutar por campo a los bolts paralelos, lo cual particiona los datos por el valor de campo y finalmente enruta a un bolt que determina de forma global el valor N principal.

Para ver un ejemplo de este procedimiento, consulte el ejemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## Pasos siguientes

* [Introducción al uso de Storm con HDInsight][gettingstarted]

* [Análisis de los datos de sensor con Storm y HDInsight](/es-es/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Desarrollo de aplicaciones de procesamiento de datos de transmisión por secuencias con SCP.NET y C# en Storm en HDInsight](/es-es/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /es-es/documentation/articles/hdinsight-storm-getting-started
<!--HONumber=42-->
