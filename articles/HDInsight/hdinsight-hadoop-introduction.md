<properties 
	pageTitle="Hadoop en HDInsight: análisis de Big Data en la nube | Microsoft Azure" 
	description="Una introducción a los componentes de Hadoop en HDInsight en la nube. Sepa cómo HDInsight usa clústeres de Hadoop para administrar, analizar y generar informes sobre Big Data." 
	keywords="big data,big data analysis,hadoop,introduction to hadoop,what is hadoop"
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="cgronlun"/>


# Introducción a Hadoop en HDInsight: análisis y procesamiento de Big Data en la nube

Obtenga una introducción a Hadoop, su ecosistema y Big Data en HDInsight de Azure: Hadoop en HDInsight y conozca cuáles son los componentes de Hadoop, su terminología común y los escenarios para el análisis de Big Data. Además, obtenga información acerca de la documentación, los tutoriales y los recursos para utilizar Hadoop en HDInsight.

## Hadoop en HDInsight

Azure HDInsight implementa y aprovisiona clústeres de Apache Hadoop en la nube con el fin de proporcionar un marco de software diseñado para realizar tareas de administración, análisis y generación de informes en relación con grandes volúmenes de datos. El núcleo de Hadoop proporciona almacenamiento de datos confiable con el sistema de archivos distribuido Hadoop (HDFS, Hadoop Distributed File System) y un sencillo modelo de programación MapReduce para procesar y analizar, en paralelo, los datos almacenados en este sistema distribuido.


## ¿Qué son grandes volúmenes de datos?
Grandes volúmenes de datos se refiere a datos que se recopilan en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos no estructurados y contextos semánticos variables.

Big Data describe a cualquier conjunto de gran tamaño de información digital, desde el texto de una fuente de Twitter hasta información del sensor de un equipamiento industrial e información sobre la actividad de exploración y compras en un catálogo en línea de un cliente. Los grandes volúmenes de datos pueden ser históricos (es decir, datos almacenados) o en tiempo real (es decir, transferidos directamente desde el origen).

Para que Big Data proporcione conocimientos o información de inteligencia procesables, no solo es necesario formular las preguntas correctas y recopilar datos relevantes para los problemas en cuestión, sino que los datos también deben ser accesibles y poderse limpiar y analizar para presentarlos de forma útil. Y ahí es donde el análisis de Big Data de Hadoop en HDInsight puede resultar útil.


## <a name="overview"></a>Información general sobre el ecosistema de Hadoop en HDInsight

HDInsight es una implementación en la nube en Microsoft Azure del componente tecnológico de Apache Hadoop en rápida expansión que constituye la solución imprescindible para el análisis de Big Data. Incluye implementaciones de Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, entre otros. HDInsight se integra también con herramientas de Business Intelligence (BI) como, por ejemplo, Excel, SQL Server Analysis Services y SQL Server Reporting Services.


* HDInsight de Azure implementa y aprovisiona clústeres de Hadoop en la nube, mediante el uso de **Linux** o **Windows** como el SO subyacente.

	* **HDInsight en Linux (vista previa)**: un clúster de Hadoop en Ubuntu. Utilícelo si conoce Linux o Unix, si realiza una migración desde una solución de Hadoop basada en Linux existente o si desea una integración simple con los componentes del ecosistema de Hadoop creados para Linux.

	* **HDInsight en Windows**: un clúster de Hadoop en Windows Server. Utilícelo si conoce Windows, si realiza una migración desde una solución de Hadoop basada en Windows existente o si desea una integración con .NET u otras funcionalidades de Windows.

	En la tabla siguiente se presenta una comparación entre ambos:

	Categoría | HDInsight en Linux | HDInsight en Windows 
	---------| -------------------| --------------------
	**SO del clúster** | Ubuntu 12.04 Long Term Support (LTS) | Windows Server 2012 R2
	**Tipo de clúster** | Hadoop | Hadoop, HBase, Storm
	**Implementación** | Portal de administración de Azure, línea de comandos multiplataforma, Azure PowerShell | Portal de administración de Azure, línea de comandos multiplataforma, Azure PowerShell, HDInsight, SDK de .NET
	**Interfaz de usuario del clúster** | Ambari | Panel del clúster
	**Acceso remoto** | Shell seguro (SSH) | Protocolo de escritorio remoto (RDP)
	

* HDInsight utiliza la distribución **Hortonworks Data Platform (HDP)** de Hadoop.

* Apache Hadoop es un marco de software para la administración y el análisis de Big Data. HDInsight proporciona varias configuraciones para cargas de trabajo específicas, o bien puede <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">personalizar clústeres mediante el uso de acciones de script</a>.

	* **Hadoop**: proporciona almacenamiento de datos confiables con [HDFS](#HDFS) y un modelo de programación de [MapReduce](#mapreduce) simple para procesar y analizar datos en paralelo.
	
	* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: una base de datos no SQL en Hadoop que proporciona un acceso aleatorio y una coherencia fuerte para grandes cantidades de datos no estructurados y semiestructurados; potencialmente miles de millones de filas multiplicadas por millones de columnas. Consulte [Información general de HBase en HDInsight](hdinsight-hbase-overview.md).
	
	* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: un sistema distribuido de cálculo en tiempo real para el procesamiento de grandes flujos de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop](hdinsight-storm-sensor-data-analysis.md).

## ¿Cuáles son los componentes de Hadoop?

Además de las configuraciones generales mencionadas, los clústeres de HDInsight también incluyen los componentes siguientes.

* **[Ambari](#ambari)**: aprovisionamiento, administración y supervisión de clústeres. 

* **[Avro](#avro)** (biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno de Microsoft .NET.

* **[Hive](#hive)**: consultas de tipo lenguaje de consulta estructurado (SQL).

* **[Mahout](#mahout)**: aprendizaje automático.

* **[MapReduce y YARN](#mapreduce)**: procesamiento distribuido y administración de recursos.

* **[Oozie](#oozie)**: administración de flujo de trabajo.

* **[Pig](#pig)**: scripts más sencillos para transformaciones de MapReduce.

* **[Sqoop](#sqoop)**: importación y exportación de datos.

* **[ZooKeeper](#zookeeper)**: coordinación de procesos en sistemas distribuidos.

> [AZURE.NOTE]Para obtener información sobre los componentes específicos y sus versiones, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][component-versioning].

###<a name="ambari"></a>Ambari

Apache Ambari sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md) (solo Linux), [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md) y la <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referencia de API de Apache Ambari</a>.

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)

La biblioteca de Microsoft .NET para Avro implementa el formato compacto de intercambio de datos binarios de Apache Avro para la serialización del entorno de Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">notación de objetos JavaScript (JSON)</a> para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes, lo que se traduce en que los serializados en un lenguaje se pueden leer en otro. Encontrará información detallada sobre el formato en las <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificaciones de Apache Avro</a>. El formato de los archivos de Avro admite el modelo de programación distribuida de MapReduce. Los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto. Para descubrir cómo, consulte [Serialización de datos con la biblioteca de Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

El sistema de archivos distribuido de Hadoop (HDFS) es un sistema de archivos distribuido que, junto con MapReduce y YARN, conforma el núcleo del ecosistema Hadoop. HDFS es el sistema de archivos estándar para los clústeres de Hadoop en HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> es el software de almacenamiento de datos basado en Hadoop que le permite consultar y administrar grandes conjuntos de datos en almacenamiento distribuido con un lenguaje de tipo SQL denominado HiveQL. Hive, al igual que Pig, es una abstracción situada en un nivel superior a MapReduce y, al ejecutarse, traduce las consultas a una serie de trabajos de MapReduce. Hive se encuentra conceptualmente más cerca de un sistema de administración de bases de datos relacionales que Pig y, por lo tanto, es más adecuado para su uso con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Consulte [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> es una biblioteca escalable de algoritmos de aprendizaje automático que se ejecuta en Hadoop. Mediante principios de estadísticas, las aplicaciones de aprendizaje automático enseñan a los sistemas a aprender de los datos y a usar los resultados obtenidos en el pasado para determinar el comportamiento en el futuro. Consulte [Generación de recomendaciones de películas mediante Mahout en Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce y YARN
MapReduce de Hadoop es un marco de software para la escritura de aplicaciones con el fin de procesar en paralelo conjuntos de Big Data. Un trabajo de MapReduce divide conjuntos de datos de gran tamaño y organiza los datos en pares clave-valor para su procesamiento.

Apache YARN es la última generación de MapReduce (MapReduce 2.0 o MRv2) que divide las dos tareas principales de la herramienta de seguimiento de trabajos, la administración de recursos y la programación y supervisión de trabajos, en entidades independientes.

Para obtener más información sobre MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> en el sitio Wiki de Hadoop. Para obtener información sobre YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">MapReduce de última generación de Apache Hadoop (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java. Consulte [Uso del coordinador de Oozie de tiempo con Hadoop en HDInsight](hdinsight-use-oozie-coordinator-time.md).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> es una plataforma de alto nivel que permite realizar transformaciones complejas de MapReduce en conjunto de datos de gran tamaño con un sencillo lenguaje de scripting denominado Pig Latin. Pig traduce los scripts de Pig Latin para que se ejecuten en Hadoop. Se pueden crear funciones definidas por el usuario (UDF) para ampliar Pig Latin. Consulte [Uso de Pig con Hadoop para analizar un archivo de registro de Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL, u otros almacenes de datos estructurados, de la manera más eficiente posible. Consulte [Uso de Sqoop con Hadoop](hdinsight-use-sqoop.md).


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina procesos en grandes sistemas distribuidos por medio de un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contiene pequeñas cantidades de metadatos necesarios para la coordinación de procesos: estado, ubicación, configuración, entre otros.

## <a name="advantage"></a>Ventajas de Hadoop en la nube

Como parte del ecosistema de la nube de Azure, Hadoop en HDInsight ofrece varias ventajas, entre otras:

* El aprovisionamiento automático de clústeres de Hadoop. La creación de los clústeres de HDInsight es mucho más simple que la configuración manual de los clústeres de Hadoop. Para obtener información detallada, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md).

* Componentes de Hadoop de última generación. Para obtener información detallada, consulte [
* Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][component-versioning].

* Alta disponibilidad y confiabilidad de los clústeres. Si desea obtener información detallada, consulte [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability.md).

* Almacenamiento de datos eficiente y económico con el almacenamiento de blobs de Azure, opción compatible con Hadoop. Si desea obtener información detallada, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight](../hdinsight-use-blob-storage.md).

* Integración con otros servicios de Azure, como [Sitios web](../documentation/services/websites/) y [Base de datos SQL](../documentation/services/sql-database/).

* Coste inicial bajo. Inicie una [prueba gratuita](/pricing/free-trial/) o consulte [Detalles de precios de HDInsight](/pricing/details/hdinsight/).


Para obtener más información sobre las ventajas de Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight][marketing-page].



## <a id="resources"></a>Recursos para obtener más información sobre el análisis de Big Data, Hadoop y HDInsight

Básese en esta introducción a Hadoop en HDInsight y en el análisis de Big Data con los recursos que aparecen a continuación.


### HDInsight en Linux (vista previa)

* [Introducción a HDInsight en Linux](../hdinsight-hadoop-linux-get-started.md): un tutorial de inicio rápido sobre el aprovisionamiento de clústeres de Hadoop para HDInsight en Linux y la ejecución de consultas de ejemplo de Hive.

* [Aprovisionamiento de HDInsight en Linux usando opciones personalizadas](hdinsight-hadoop-provision-linux-clusters.md): aprenda a aprovisionar un clúster de Hadoop para HDInsight en Linux mediante el uso de opciones personalizadas a través del Portal de administración de Azure, la línea de comandos multiplataforma de Azure o Azure PowerShell.

* [Trabajo con HDInsight en Linux](hdinsight-hadoop-linux-information.md): obtenga algunas sugerencias rápidas sobre cómo trabajar con clústeres de Hadoop en Linux aprovisionados en Azure.

* [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a supervisar y administrar su clúster de Hadoop en HDInsight basado en Linux con Ambari Web o la API de REST de Ambari.


### HDInsight en Windows

* [Documentación de HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/): la página de documentación de HDInsight de Azure con vínculos a artículos, vídeos y otros recursos.

* [Mapa de aprendizaje para HDInsight](hdinsight-learn-map.md): un paseo guiado por la documentación de Hadoop para HDInsight.

* [Introducción a HDInsight de Azure](../hdinsight-get-started.md): un tutorial de inicio rápido sobre el uso de Hadoop en HDInsight.

* [Ejecución de ejemplos de HDInsight](hdinsight-run-samples.md): un tutorial sobre cómo ejecutar los ejemplos que se incluyen en HDInsight.
	
* [SDK de HDInsight de Azure](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx): documentación de referencia para el SDK de HDInsight.


### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: obtenga más información acerca de la biblioteca de software Apache Hadoop, un marco que permite efectuar el procesamiento distribuido de grandes conjuntos de datos en clústeres de equipos.	

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a>: obtenga más información acerca de la arquitectura y el diseño del Sistema de archivos distribuidos Hadoop, el sistema de almacenamiento principal de las aplicaciones de Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial de MapReduce</a>: obtenga más información acerca del marco de programación para escribir aplicaciones de Hadoop con el fin de procesar rápidamente enormes cantidades de datos, en paralelo, en grandes clústeres de nodos de proceso.

### Base de datos SQL en Azure	
		
* [Base de datos SQL de Azure](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): documentación de MSDN para Base de datos SQL.
	
* [Portal de administración para Base de datos SQL](https://msdn.microsoft.com/library/azure/dn771027.aspx): una herramienta de administración ligera y fácil de usar para administrar el modelo de ejecución Base de datos SQL en la nube.

* [Adventure Works para Base de datos SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): página de descarga de una base de datos de ejemplo de Base de datos SQL.

### Microsoft Business Intelligence (para HDInsight en Windows)

Las herramientas de Business Intelligence (BI) habituales, como Excel, PowerPivot, SQL Server Analysis Services y SQL Server Reporting Services, recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver.

Estas herramientas de BI pueden ayudarle en el análisis de Big Data:
 
* [Conexión de Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster de HDInsight mediante Microsoft Power Query para Excel. 

* [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-ODBC-driver.md): aprenda a importar datos desde HDInsight con Microsoft Hive ODBC Driver.

* [Plataforma en la nube de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.

* <a target="_blank" https://msdn.microsoft.com/library/hh231701.aspx">Más información acerca de SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Más información acerca de SQL Server Reporting Services</a>.


### Pruebe soluciones de Hadoop para el análisis de Big Data (para HDInsight en Windows)

Utilice el análisis de Big Data sobre los datos de su organización para obtener información sobre su negocio. A continuación se muestran algunos ejemplos:

* [Análisis de datos de sensor de HVAC](hdinsight-hive-analyze-sensor-data.md): aprenda a analizar los datos del sensor mediante Hive con HDInsight (Hadoop) y, a continuación, visualice los datos en Microsoft Excel. En este ejemplo, usará Hive para procesar datos históricos producidos por sistemas HVAC para ver los sistemas que pueden mantener de forma confiable una temperatura establecida.

* [Uso de Hive con HDInsight para analizar registros de sitios web](hdinsight-hive-analyze-website-log.md): aprenda a usar HiveQL en HDInsight para analizar registros de sitios web con el objeto de obtener información sobre la frecuencia de las visitas diarias desde sitios web externos, además de un resumen de los errores que experimentan los usuarios de los sitios web.

* [Análisis de los datos de sensor en tiempo real con Storm y HBase en HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): aprenda a compilar una solución que utilice un clúster de Storm en HDInsight con el fin de procesar datos de sensor desde Centros de eventos de Azure y, a continuación, visualice los datos de sensor procesados como información prácticamente en tiempo real en un panel basado en web.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!--HONumber=54--> 