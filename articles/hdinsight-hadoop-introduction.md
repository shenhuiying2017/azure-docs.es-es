<properties 
	pageTitle="Introducción a Hadoop en HDInsight: Análisis de grandes volúmenes de datos en la nube | Azure" 
	description="Obtenga información sobre cómo HDInsight de Azure usa clústeres de Hadoop de Apache en la nube con el fin de proporcionar un marco de software para realizar tareas de administración, análisis y generación de informes sobre grandes volúmenes de datos." 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# Introducción a Hadoop en HDInsight: procesamiento y análisis de grandes volúmenes de datos en la nube

Obtenga información general sobre componentes de HDInsight, terminología común y escenarios, y consulte recursos para el uso de Hadoop en HDInsight.

Azure HDInsight implementa y aprovisiona clústeres de Apache Hadoop en la nube con el fin de proporcionar un marco de software diseñado para realizar tareas de administración, análisis y generación de informes en relación con grandes volúmenes de datos. El núcleo de Hadoop proporciona almacenamiento de datos confiable con el sistema de archivos distribuido Hadoop (HDFS, Hadoop Distributed File System) y un sencillo modelo de programación MapReduce para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido. 

### ¿Qué son grandes volúmenes de datos?
Grandes volúmenes de datos se refiere a datos que se recopilan en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos no estructurados y contextos semánticos variables. 

Los grandes volúmenes de datos describen cualquier conjunto de gran tamaño de información digital, desde el texto de una fuente de Twitter, hasta información del sensor de un equipamiento industrial e información sobre la actividad de exploración y compras en un catálogo en línea. Los grandes volúmenes de datos pueden ser históricos (es decir, datos almacenados) o en tiempo real (es decir, transferidos directamente desde el origen). 

Para que los grandes volúmenes de datos proporcionen conocimientos o información de inteligencia procesables, no solo es necesario formular las preguntas correctas y recopilar datos relevantes para los problemas en cuestión, sino que los datos también deben ser accesibles y poderse limpiar y analizar para presentarlos de forma útil. Y ahí es donde Hadoop en HDInsight puede resultar práctico.


## En este artículo

En este artículo se ofrece información general sobre Hadoop en HDInsight, que incluye:

* **[Información general sobre el ecosistema de Hadoop en HDInsight:](#overview)**  HDInsight es la solución de Hadoop en Azure y ofrece implementaciones de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, etc. HDInsight se integra también con herramientas de Business Intelligence (BI) como, por ejemplo, Excel, SQL Server Analysis Services y SQL Server Reporting Services.
* **[Ventajas de Hadoop en la nube:](#advantage)** Motivos que deben tenerse en cuenta para la implementación de Hadoop en la nube de HDInsight.
* **[Soluciones de HDInsight para el análisis de grandes volúmenes de datos:](#solutions)** algunas formas prácticas en que puede usar HDInsight para responder a preguntas sobre su organización, desde el análisis de la opinión de Twitter hasta el análisis de la eficacia del sistema HVAC.
* **[Recursos para obtener más información sobre el análisis de grandes volúmenes de datos, Hadoop y HDInsight:](#resources)** Vínculos a información adicional.


## <a name="overview"></a>Información general sobre el ecosistema de Hadoop en HDInsight

Hadoop es el componente tecnológico en rápida expansión que constituye la solución imprescindible para el análisis de grandes volúmenes de datos. HDInsight es el marco para la implementación de Hadoop en la nube de Microsoft Azure.
 
Los clústeres de Hadoop en HDInsight usan versiones de la distribución de Hortonworks Data Platform (HDP) y el conjunto de componentes de Hadoop en dicha distribución. Para obtener información sobre los componentes y sus versiones en clústeres de HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][component-versioning]

Apache Hadoop es un marco de software para la administración y el análisis de grandes volúmenes de datos. El núcleo de Apache Hadoop proporciona almacenamiento de datos fiable con el sistema de archivos distribuidos Hadoop (HDFS, Hadoop Distributed File System) y un modelo de programación MapReduce sencillo para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido. HDFS utiliza la replicación de datos para resolver los problemas ocasionados por errores de hardware que surgen al implementar dichos sistemas altamente distribuidos.

A continuación se indican las tecnologías de Hadoop en HDInsight:

* **[Ambari](#ambari):** aprovisionamiento, administración y supervisión de clústeres 
* **[Avro](#avro)** (biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno de Microsoft .NET 
* **[HBase](#hbase):** base de datos no relacional para tablas de gran tamaño
* **[HDFS](#hdfs):** sistema de archivos distribuido de Hadoop
* **[Hive](#hive):** consultas de tipo SQL
* **[Mahout](#mahout):** aprendizaje automático
* **[MapReduce y YARN](#mapreduce):** procesamiento distribuido y administración de recursos
* **[Oozie](#oozie):** administración de flujos de trabajo
* **[Pig](#pig):** scripts más sencillos para transformaciones de MapReduce
* **[Sqoop](#sqoop):** importación y exportación de datos 
* **[Storm](#storm):** procesamiento en tiempo real de secuencias rápidas de datos de gran tamaño
* **[Zookeeper](#zookeeper):** procesos coordinados en sistemas distribuidos
 
Además, descubra las **[herramientas de inteligencia empresarial](#bi)** que puede utilizar con HDInsight.

###<a name="ambari"></a>Ambari

Apache Ambari sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Consulte  [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](../hdinsight-monitor-use-ambari-api/) y <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referencia de API de Apache Ambari</a>.

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)

La biblioteca de Microsoft .NET para Avro implementa el formato compacto de intercambio de datos binarios de Apache Avro para la serialización del entorno de Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">JSON</a> para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes, lo que se traduce en que los serializados en un lenguaje se pueden leer en otro. Puede encontrar información detallada sobre el formato en la <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">especificación de Apache Avro</a>. 
El formato de los archivos de Avro admite el modelo de programación distribuida de MapReduce. Los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto. Para descubrir cómo, consulte [Serialización de datos con la biblioteca de Microsoft .NET para Avro](../hdinsight-dotnet-avro-serialization/).

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">HBase Apache</a> es una base de datos no relacional basada en Hadoop y diseñada para grandes cantidades de datos no estructurados y semiestructurados: posiblemente miles de millones de filas multiplicados por millones de columnas. Los clústeres de HBase en HDInsight están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, con una latencia baja y una mayor elasticidad. Consulte [Información general sobre Hadoop en HDInsight](../hdinsight-hbase-overview/).

### <a name="hdfs"></a>HDFS

El sistema de archivos distribuido de Hadoop (HDFS) es un sistema de archivos distribuido que, junto con MapReduce y YARN, conforma el núcleo del ecosistema Hadoop. HDFS es el sistema de archivos estándar para los clústeres de Hadoop en HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> es el software de almacén de datos basado en Hadoop que le permite consultar y administrar grandes conjuntos de datos en almacenamiento distribuido con un lenguaje de tipo SQL denominado HiveQL. Hive, al igual que Pig, es una abstracción situada en un nivel superior a MapReduce y, al ejecutarse, traduce las consultas en una serie de trabajos de MapReduce. Hive se encuentra conceptualmente más cerca de un sistema de administración de bases de datos relacionales que Pig y, por lo tanto, es más adecuado para su uso con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Consulte [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> es una biblioteca escalable de algoritmos de aprendizaje automático que se ejecuta en Hadoop. Mediante principios de estadísticas, las aplicaciones de aprendizaje automático enseñan a los sistemas a aprender de los datos y a usar los resultados obtenidos en el pasado para determinar el comportamiento en el futuro. Consulte [Generación de recomendaciones de películas mediante Mahout en Hadoop](../hdinsight-mahout/).

### <a name="mapreduce"></a>MapReduce y YARN
MapReduce de Hadoop es un marco de software para la escritura de aplicaciones con el fin de procesar en paralelo conjuntos de grandes volúmenes de datos. Un trabajo de MapReduce divide conjuntos de gran tamaño y organiza los datos en pares clave-valor para su procesamiento. 

Apache YARN es la última generación de MapReduce (MapReduce 2.0 o MRv2) que divide las dos tareas principales de la herramienta de seguimiento de trabajos, la administración de recursos y la programación y supervisión de trabajos, en entidades independientes.

Para obtener más información sobre MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> en el sitio Wiki de Hadoop. Para obtener información acerca de YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java. Consulte [Uso del coordinador de Oozie de tiempo con Hadoop en HDInsight](../hdinsight-use-oozie-coordinator-time/).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> es una plataforma de alto nivel que permite realizar transformaciones complejas de MapReduce en conjuntos de datos de gran tamaño con un sencillo lenguaje de scripting denominado Pig Latin. Pig traduce los scripts de Pig Latin para que se ejecuten en Hadoop. Se pueden crear funciones definidas por el usuario (UDF) para ampliar Pig Latin. Consulte [Uso de Pig con Hadoop para analizar un archivo de registro de Apache](../hdinsight-use-pig/).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL u otros almacenes de datos estructurados, de la forma más eficiente posible. Consulte [Uso de Sqoop con Hadoop](../hdinsight-use-sqoop/).

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a> es un sistema distribuido de cálculo en tiempo real para el procesamiento rápido de grandes secuencias de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop](../hdinsight-storm-sensor-data-analysis/).

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> coordina procesos en grandes sistemas distribuidos por medio de un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contiene pequeñas cantidades de metadatos necesarios para la coordinación de procesos: estado, ubicación, configuración, entre otros. 

### <a name="bi"></a>Herramientas de Business Intelligence 
Las herramientas de Business Intelligence habituales (como Excel, PowerPivot, SQL Server Analysis Services y Reporting Services) recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver. 

Estas herramientas de BI pueden ayudarle en el análisis de grandes volúmenes de datos:

* <a target="_blank" href="http://www.microsoft.com/es-es/download/details.aspx?id=39379">Descarga de Microsoft Power Query para Excel</a> 
* <a target="_blank" href="http://www.microsoft.com/es-es/download/details.aspx?id=40886">Descarga de Microsoft Hive ODBC Driver</a>
* <a target="_blank" href="http://www.microsoft.com/es-es/server-cloud/solutions/business-intelligence/analysis.aspx">Más información sobre SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Más información sobre SQL Server Reporting Services</a>

## <a name="advantage"></a>Ventajas de Hadoop en la nube

Como parte del ecosistema de la nube de Azure, Hadoop en HDInsight ofrece varias ventajas, entre otras:

* Componentes de Hadoop de última generación. Para obtener información más detallada, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][component-versioning]
* Alta disponibilidad y confiabilidad de los clústeres. Si desea información más detallada, consulte [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](../hdinsight-high-availability/).
* Almacenamiento de datos eficiente y económico con el almacenamiento de blobs de Azure, opción compatible con Hadoop. Si desea información más detallada, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight](../hdinsight-use-blob-storage/).
* Integración con otros servicios de Azure, como [Sitios web](../documentation/services/websites/) y [Base de datos SQL](../documentation/services/sql-database/).
* Coste inicial bajo. Inicie una [prueba gratuita](/pricing/free-trial/) o consulte [Detalles de precios de HDInsight](../pricing/details/hdinsight/).

Para obtener más información sobre las ventajas de Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight][marketing-page].

## <a name="solutions"></a>Prueba de soluciones de HDInsight para el análisis de grandes volúmenes de datos

Analice los datos de su organización para obtener mayor conocimiento sobre su negocio. A continuación se muestran algunos ejemplos: 

* [Análisis de datos del sensor de HVAC](../hdinsight-hive-analyze-sensor-data/): Aprenda a analizar los datos del sensor mediante Hive con HDInsight (Hadoop) y visualice a continuación los datos en Microsoft Excel. En este ejemplo, usará Hive para procesar datos históricos producidos por sistemas HVAC para ver los sistemas que pueden mantener de forma confiable una temperatura establecida.
* [Uso de Hive con HDInsight para analizar registros de sitios web](../hdinsight-hive-analyze-website-log/): A prenda a usar HiveQL in HDInsight para analizar los registros del sitio web con objeto de conocer la frecuencia de las visitas diarias al mismo desde sitios web externos y obtener un resumen de los errores del sitio web que los usuarios experimentan.
* [Análisis de datos de sensor en tiempo real con Storm y HBase en HDInsight (Hadoop)](../hdinsight-storm-sensor-data-analysis/): Aprenda a compilar una solución que use un clúster de Storm en HDInsight con el fin de procesar datos de sensor desde centros de eventos de Azure y visualice los datos de sensor procesados como información prácticamente en tiempo real en un panel basado en web.

Para probar Hadoop en HDInsight, consulte artículos sobre "Introducción" en la sección de exploración de la [página de documentación de HDInsight](../documentation/services/hdinsight/). Para probar ejemplos más avanzados, desplácese hasta la sección de análisis.



## <a name="resources"></a>Recursos para obtener más información sobre el análisis de grandes volúmenes de datos, Hadoop y HDInsight 

### Hadoop en HDInsight	

* [Documentación de HDInsight](../documentation/services/hdinsight/) Página de documentación de Azure HDInsight con vínculos a artículos, vídeos y otros recursos.

* [Mapa de aprendizaje para Hadoop en HDInsight](../hdinsight-learn-map): Paseo guiado por la documentación de Hadoop para HDInsight.

* [Introducción a HDInsight de Azure](../hdinsight-get-started/) Tutorial de inicio rápido sobre el uso de Hadoop en HDInsight.

* [Ejecución de muestras de HDInsight](../hdinsight-run-samples/): Tutorial sobre la ejecución de ejemplos incluidos en HDInsight.
	
* [SDK de Azure HDInsight](http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx): Documentación de referencia de HDinsight SDK.


### Base de datos SQL en Azure	
		
* [Base de datos SQL de Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): Documentación de MSDN sobre Base de datos SQL.
	
* [Portal de administración de Base de datos SQL](http://msdn.microsoft.com/library/windowsazure/gg442309.aspx) Herramienta de administración de bases de datos ligera y fácil de usar para administrar el modelo de ejecución Base de datos SQL en la nube.

* [Adventure Works for Base de datos SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): Página de descarga de bases de datos de ejemplo para el modelo de ejecución Base de datos SQL.	

### Microsoft Business Intelligence		

* [Conexión de Excel a Hadoop con Power Query](../hdinsight-connect-excel-power-query/): Aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster HDInsight mediante Microsoft Power Query para Excel. 

* [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver](../hdinsight-connect-excel-hive-ODBC-driver/): Aprenda a importar datos desde HDInsight con Microsoft Hive ODBC Driver.

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/es-es/server-cloud/solutions/business-intelligence/default.aspx): Obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Obtenga más información acerca de la biblioteca de software Apache Hadoop, un marco que permite efectuar el procesamiento distribuido de grandes conjuntos de datos en clústeres de equipos.	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>: Obtenga más información acerca de la arquitectura y el diseño del sistema de archivos distribuidos Hadoop (HDFS), el sistema de almacenamiento principal de las aplicaciones de Hadoop.		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: Obtenga más información acerca de este marco de programación para escribir aplicaciones de Hadoop con el fin de procesar enormes cantidades de datos, en paralelo, en grandes clústeres de nodos de proceso.	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 
<!--HONumber=42-->
