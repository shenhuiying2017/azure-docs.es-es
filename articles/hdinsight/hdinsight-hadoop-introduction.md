---
title: "¿Qué es Hadoop? Introducción a Azure HDInsight | Microsoft Docs"
description: "Obtenga una introducción a los componentes y el ecosistema de Hadoop en HDInsight. HDInsight incluye Hadoop, Spark, HBase y muchos más para el procesamiento y el análisis de macrodatos."
keywords: "análisis de macrodatos,introducción a hadoop,qué es hadoop,componente tecnológico de hadoop, ecosistema de hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: 10d684bd6c9408f2fbd48a0a8804b011f098d71c
ms.openlocfilehash: 8f38b9eaf8bcb498a7983756cbf57f3e372d7fe0


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Introducción al ecosistema de Hadoop en Azure HDInsight
 Este artículo proporciona una introducción a Hadoop en Azure HDInsight, su ecosistema y los macrodatos. Información acerca de los componentes de Hadoop, terminología común y escenarios para el análisis de macrodatos.

## <a name="what-is-hadoop-on-hdinsight"></a>¿Qué es Hadoop en HDInsight?
Hadoop se refiere a un ecosistema de software de código abierto que es un marco para el procesamiento distribuido, el almacenamiento y el análisis de macrodatos en clústeres de equipos. Azure HDInsight permite que los componentes de Hadoop de la distribución de **Hortonworks Data Platform (HDP)** estén disponibles en la nube, implementa clústeres administrados con gran confiabilidad y disponibilidad y proporciona seguridad y gobierno de nivel empresarial con Active Directory.  

Apache Hadoop era el proyecto de código abierto original de procesamiento de macrodatos. Después vino el desarrollo de software y utilidades relacionadas que se consideraron parte del componente tecnológico de Hadoop, como Apache Hive, Apache HBase, Apache Spark, Apache Kafka y muchos otros. Para más información, consulte [Información general sobre el ecosistema de Hadoop en HDInsight](#overview).

## <a name="what-is-big-data"></a>¿Qué son grandes volúmenes de datos?
Los macrodatos describen cualquier conjunto de gran tamaño de información digital, desde el texto de una fuente de Twitter hasta información del sensor de un equipamiento industrial e información sobre la actividad de exploración y compras en un sitio web. Los grandes volúmenes de datos pueden ser históricos (es decir, datos almacenados) o en tiempo real (es decir, transferidos directamente desde el origen). Los macrodatos se recopilan en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos.

Para que los macrodatos proporcionen conocimientos o información factibles, debe recopilar datos pertinentes y formular las preguntas adecuadas. También debe asegurarse de que los datos son accesibles, están limpios y se han analizado para presentarlos de forma útil. Y ahí es donde el análisis de Big Data de Hadoop en HDInsight puede resultar útil.

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>Información general sobre el ecosistema de Hadoop en HDInsight
HDInsight es una distribución en la nube en Microsoft Azure del componente tecnológico de Apache Hadoop en rápida expansión para el análisis de macrodatos. Incluye implementaciones de Apache Spark, HBase, Kafka, Storm, Pig, Hive, Interactive Hive, Sqoop, Oozie, Ambari, etc. HDInsight se integra también con herramientas de Business Intelligence (BI) como, por ejemplo, Power BI, Excel, SQL Server Analysis Services y SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-kafka-interactive-hive-storm-customized-and-other-clusters"></a>Hadoop, HBase, Spark, Kafka, Interactive Hive, Storm, clústeres personalizados y de otro tipo
HDInsight ofrece los siguientes tipos de clúster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: proporciona almacenamiento de datos confiable con [HDFS](#hdfs) y un modelo de programación de [MapReduce](#mapreduce) simple para procesar y analizar datos en paralelo.
* **[Apache Spark](http://spark.apache.org/)**: marco de procesamiento paralelo que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos, trabajos de Spark para SQL, datos de streaming y aprendizaje automático. Vea [Información general: ¿Qué es Apache Spark en HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: base de datos NoSQL en Hadoop que proporciona acceso aleatorio y gran coherencia para grandes cantidades de datos no estructurados y semiestructurados; potencialmente miles de millones de filas multiplicadas por millones de columnas. Consulte [Información general de HBase en HDInsight](hdinsight-hbase-overview.md).
* **[Microsoft R Server](https://msdn.microsoft.com/en-us/microsoft-r/rserver)**: un servidor de clase empresarial para hospedar y administrar procesos de R distribuidos en paralelo. Proporciona a los científicos de datos, estadísticos y programadores de R acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight. [Información general de R Server en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-overview).
* **[Apache Storm](https://storm.incubator.apache.org/)**: sistema distribuido de cálculo en tiempo real para el procesamiento rápido de grandes transmisiones de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Versión preliminar de Apache Interactive Hive (también conocido como Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: almacenamiento en caché en memoria para consultas de Hive interactivas y más rápidas. Consulte [Uso de Interactive Hive en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-interactive-hive).
* **[Versión preliminar de Apache Kafka](https://kafka.apache.org/)**: una plataforma de código abierto usada para crear canalizaciones y aplicaciones de datos de streaming. Kafka también proporciona funcionalidad de cola de mensajes que le permite publicar flujos de datos y suscribirse a ellos. Consulte [Introducción a Apache Kafka en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-kafka-introduction).
* **[Versión preliminar de clústeres unidos a dominio](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)**: un clúster unido a un dominio de Active Directory para que pueda controlar el acceso a los datos y manejarlos.
* **[Clústeres personalizados con acciones de script](hdinsight-hadoop-customize-cluster-linux.md)**: clústeres con scripts que se ejecutan durante el aprovisionamiento y que instalan componentes.

### <a name="example-customization-scripts"></a>Scripts de personalización de ejemplo
Las acciones de script son scripts de Bash en Linux que se ejecutan durante el aprovisionamiento del clúster y que se pueden usar para instalar componentes adicionales en el clúster.

Los siguientes scripts de ejemplo los ofrece el equipo de HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md): conjunto de aplicaciones web que se usan para interactuar con un clúster. Solo clústeres Linux.
* [Giraph](hdinsight-hadoop-giraph-install-linux.md): procesamiento de gráficos para modelar las relaciones entre las cosas o las personas.
* [R](hdinsight-hadoop-r-scripts-linux.md): es un entorno y lenguaje de código abierto para computación estadística que se usa en el aprendizaje automático.
* [Solr](hdinsight-hadoop-solr-install-linux.md): plataforma de búsqueda de escala empresarial que permite la búsqueda de texto completo en los datos.

Para obtener información sobre el desarrollo de sus propias acciones de script, consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="what-are-the-hadoop-components-and-utilities"></a>¿Cuáles son las utilidades y los componentes de Hadoop?
Se incluyen los siguientes componentes y utilidades en clústeres de HDInsight.

* **[Ambari](#ambari)**: aprovisionamiento, administración, supervisión y utilidades de clústeres.
* **[Avro](#avro)** (biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno de Microsoft .NET.
* **[Hive y HCatalog](#hive)**: consultas tipo SQL (lenguaje de consulta estructurado) y una capa de administración de almacenamiento y de tablas.
* **[Mahout](#mahout)**: para aplicaciones de aprendizaje automático escalables.
* **[MapReduce](#mapreduce)**: marco heredado para el procesamiento distribuido y la administración de recursos de Hadoop. Consulte [YARN](#yarn), el marco de recursos de última generación.
* **[Oozie](#oozie)**: administración de flujo de trabajo.
* **[Phoenix](#phoenix)**: capa de base de datos relacional sobre HBase.
* **[Pig](#pig)**: scripts más sencillos para transformaciones de MapReduce.
* **[Sqoop](#sqoop)**: importación y exportación de datos.
* **[Tez](#tez)**: permite a los procesos con muchos datos ejecutarse de forma eficaz a escala.
* **[YARN](#yarn)**: parte de la biblioteca principal de Hadoop y el marco de software de MapReduce de última generación.
* **[ZooKeeper](#zookeeper)**: coordinación de procesos en sistemas distribuidos.

> [!NOTE]
> Para más información sobre los componentes específicos y sus versiones, consulte [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?][component-versioning]
>
>

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
Apache Ambari sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Los clústeres de HDInsight en Linux proporcionan la interfaz de usuario web de Ambari y la API de REST de Ambari, mientras que los clústeres en Windows proporcionan un subconjunto de la API de REST. Las Vistas de Ambari en clústeres de HDInsight admiten funcionalidades de IU de complementos.

Consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md) (solo Linux), [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md) y la <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API reference</a> (Referencia de API de Apache Ambari).

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)
La biblioteca de Microsoft .NET para Avro implementa el formato compacto de intercambio de datos binarios de Apache Avro para la serialización del entorno de Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">notación de objetos JavaScript (JSON)</a> para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes, lo que se traduce en que los serializados en un lenguaje se pueden leer en otro. Encontrará información detallada sobre el formato en <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html"> Apache Avro Specification</a> (Especificaciones de Apache Avro).
El formato de los archivos de Avro admite el modelo de programación distribuida de MapReduce. Los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto. Para descubrir cómo, consulte [Serialización de datos con la biblioteca de Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
El sistema de archivos distribuido de Hadoop (HDFS) es un sistema de archivos distribuido que, junto con MapReduce y YARN, conforma el núcleo del ecosistema Hadoop. HDFS es el sistema de archivos estándar para los clústeres de Hadoop en HDInsight.

### <a name="a-namehiveahive--hcatalog"></a><a name="hive"></a>Hive y HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> es el software de almacenamiento de datos basado en Hadoop que le permite consultar y administrar grandes conjuntos de datos en almacenamiento distribuido con un lenguaje de tipo SQL denominado HiveQL. Hive, al igual que Pig, es una abstracción situada en un nivel superior a MapReduce y, al ejecutarse, traduce las consultas a una serie de trabajos de MapReduce. Hive se encuentra conceptualmente más cerca de un sistema de administración de bases de datos relacionales que Pig y, por lo tanto, es más adecuado para su uso con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Consulte el artículo sobre el [uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> es una capa de administración de almacenamiento y tablas para Hadoop que presenta una vista relacional de los datos. En HCatalog, se pueden leer y escribir archivos en cualquier formato para el que se pueda escribir un SerDe de Hive (serializador-deserializador).

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> es una biblioteca escalable de algoritmos de aprendizaje automático que se ejecuta en Hadoop. Mediante principios de estadísticas, las aplicaciones de aprendizaje automático enseñan a los sistemas a aprender de los datos y a usar los resultados obtenidos en el pasado para determinar el comportamiento en el futuro. Consulte [Generación de recomendaciones de películas mediante Mahout en Hadoop](hdinsight-mahout.md).

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
MapReduce es el marco de software heredado para permitir a Hadoop la escritura de aplicaciones con el fin de procesar en lote conjuntos de macrodatos en paralelo. Un trabajo de MapReduce divide conjuntos de datos de gran tamaño y organiza los datos en pares clave-valor para su procesamiento.

[YARN](#yarn) es el marco de aplicación y administrador de recursos de última generación, al que se conoce como MapReduce 2.0. Los trabajos de MapReduce se ejecutan en YARN.

Para obtener más información sobre MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> en el sitio Wiki de Hadoop.

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java. Consulte [Uso de Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight](hdinsight-use-oozie.md).

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> es una capa de base de datos relacional sobre HBase. Phoenix incluye un controlador JDBC que permite consultar y administrar tablas SQL directamente. Phoenix traduce consultas y otras instrucciones en llamadas nativas de la API NoSQL nativas en lugar de usar MapReduce, lo que permite aplicaciones más rápidas además de los almacenes NoSQL. Consulte [Usar Apache Phoenix y SQuirreL con clústeres de HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> es una plataforma de alto nivel que permite realizar transformaciones complejas de MapReduce en conjunto de datos de gran tamaño con un sencillo lenguaje de scripting denominado Pig Latin. Pig traduce los scripts de Pig Latin para que se ejecuten en Hadoop. Se pueden crear funciones definidas por el usuario (UDF) para ampliar Pig Latin. Consulte [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md).

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL, u otros almacenes de datos estructurados, de la manera más eficiente posible. Consulte [Uso de Sqoop con Hadoop](hdinsight-use-sqoop.md).

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> es un marco de aplicación basado en Hadoop YARN que ejecuta gráficos complejos y acíclicos general de procesamiento de datos generales. Es un sucesor más flexible y eficaz al marco de MapReduce que permite procesos intensivos de datos, como Hive, para ejecutar de manera más eficaz a escala. Consulte ["Usar Apache Tez para un rendimiento mejorado" en Usar Hive y HiveQL](hdinsight-use-hive.md#usetez).

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
Apache YARN es la siguiente generación de MapReduce (MapReduce 2.0, o MRv2) y es compatible con escenarios de procesamiento de datos más allá del procesamiento por lotes de MapReduce con mayor escalabilidad y procesamiento en tiempo real. YARN proporciona un marco de aplicación distribuida y administración de recursos. Los trabajos de MapReduce se ejecutan en YARN.

Para obtener información sobre YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">MapReduce de última generación de Apache Hadoop (YARN)</a>.

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina procesos en grandes sistemas distribuidos por medio de un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contiene pequeñas cantidades de metadatos necesarios para la coordinación de procesos: estado, ubicación, configuración, entre otros.

## <a name="programming-languages-on-hdinsight"></a>Lenguajes de programación en HDInsight
Los clústeres de HDInsight clusters—los clústeres de Hadoop, HBase, Storm y Spark— admiten varios lenguajes de programación, pero algunos de ellos no están instalados de manera predeterminada. En el caso de las bibliotecas, los módulos o los paquetes no instalados de manera predeterminada, instale el componente con una acción de script. Consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Compatibilidad con lenguajes de programación predeterminados
De forma predeterminada, los clústeres de HDInsight admiten lo siguiente:

* Java
* Python

Se pueden instalar lenguajes adicionales mediante acciones de script: [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Lenguajes de la máquina virtual de Java (JVM)
En una máquina virtual de Java se pueden utilizar muchos lenguajes distintos de Java; sin embargo, la ejecución de algunos de estos lenguajes puede requerir la instalación de componentes adicionales en el clúster.

Estos lenguajes basados en JVM son compatibles con clústeres de HDInsight:

* Clojure
* Jython (Python para Java)
* Scala

### <a name="hadoop-specific-languages"></a>Lenguajes específicos de Hadoop
Los clústeres de HDInsight son compatibles con los siguientes lenguajes específicos del ecosistema de Hadoop:

* Pig Latin para trabajos de Pig
* HiveQL para trabajos de Hive y SparkSQL

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>Ventajas de Hadoop en la nube
Como parte del ecosistema de la nube de Azure, Hadoop en HDInsight ofrece varias ventajas, entre otras:

* El aprovisionamiento automático de clústeres de Hadoop. La creación de los clústeres de HDInsight es mucho más simple que la configuración manual de los clústeres de Hadoop. Para obtener información detallada, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Componentes de Hadoop de última generación. Para más información, consulte [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?][component-versioning]
* Alta disponibilidad y confiabilidad de los clústeres. Si desea obtener información detallada, consulte [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md) .
* Almacenamiento de datos eficaz y económico con Azure Blob Storage o Azure Data Lake Store, ambas opciones de almacenamiento compatibles con Hadoop. Consulte [Uso de Azure Blob Storage con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md) o [Uso de Data Lake Store con un clúster de HDInsight](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) para más información.
* Integración con otros servicios de Azure, como [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/) y [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
* Tamaños y tipos de máquinas virtuales adicionales para la ejecución de clústeres de HDInsight. Para una información más detallada, consulte [¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?][component-versioning]
* Escalado de clústeres. El escalado de clústeres permite cambiar el número de nodos de un clúster de HDInsight en ejecución sin tener que eliminarlo o volverlo a crear.
* Compatibilidad con redes virtuales. Los clústeres de HDInsight se pueden usar con Red virtual de Azure para permitir el aislamiento de recursos en la nube o escenarios híbridos que vinculen recursos en la nube con los de su centro de datos.
* Coste inicial bajo. Inicie una [prueba gratuita](https://azure.microsoft.com/free/) o consulte el artículo sobre los [detalles de los precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Para más información sobre las ventajas de Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard y HDInsight Premium
HDInsight proporciona ofertas en la nube de macrodatos en dos categorías, Estándar y Premium. HDInsight Estándar proporciona un clúster de escala empresarial que las organizaciones pueden usar para ejecutar sus cargas de trabajo de macrodatos. HDInsight Premium va más allá y proporciona funcionalidades avanzadas de análisis y seguridad para un clúster de HDInsight. Para más información, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="a-idresourcesaresources-for-learning-more-about-big-data-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>Recursos para obtener más información sobre el análisis de Big Data, Hadoop y HDInsight
Básese en esta introducción a Hadoop en la nube y en el análisis de macrodatos con los recursos que aparecen a continuación.

### <a name="hadoop-documentation-for-hdinsight"></a>Documentación de Hadoop para HDInsight
* [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): la página de documentación para Hadoop en HDInsight de Azure con vínculos a artículos, vídeos y otros recursos.
* [Introducción a Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): un tutorial de inicio rápido sobre el aprovisionamiento de clústeres de Hadoop para HDInsight y la ejecución de consultas de ejemplo de Hive.
* [Introducción a Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): un tutorial de inicio rápido para crear un clúster Spark y ejecutar consultas Spark SQL interactivas.
* [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md): empiece a utilizar R Server en HDInsight Premium.
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md): aprenda a aprovisionar un clúster Hadoop de HDInsight a través de Azure Portal, la CLI de Azure o Azure PowerShell.

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: obtenga más información acerca de la biblioteca de software Apache Hadoop, un marco que permite efectuar el procesamiento distribuido de grandes conjuntos de datos en clústeres de equipos.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: obtenga más información acerca de la arquitectura y el diseño del Sistema de archivos distribuidos Hadoop, el sistema de almacenamiento principal de las aplicaciones de Hadoop.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial de MapReduce</a>: obtenga más información acerca del marco de programación para escribir aplicaciones de Hadoop con el fin de procesar rápidamente enormes cantidades de datos, en paralelo, en grandes clústeres de nodos de proceso.

### <a name="microsoft-business-intelligence"></a>Microsoft Business Intelligence
Las herramientas de Business Intelligence (BI) habituales, como Excel, PowerPivot, SQL Server Analysis Services y SQL Server Reporting Services, recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver.

Estas herramientas de BI pueden ayudarle en el análisis de Big Data:

* [Conexión de Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster de HDInsight mediante Microsoft Power Query para Excel. Se requiere una estación de trabajo de Windows. Funciona con clústeres de Linux o Windows.
* [Conexión de Excel a Hadoop con el controlador ODBC de Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): aprenda a importar datos desde HDInsight con el controlador ODBC de Microsoft Hive. Se requiere una estación de trabajo de Windows. Funciona con clústeres de Linux o Windows.
* [Plataforma en la nube de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Dec16_HO2-->


