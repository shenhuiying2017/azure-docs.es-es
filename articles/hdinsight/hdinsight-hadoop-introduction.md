---
title: "¿Qué son Azure HDInsight y la pila de tecnología y los clústeres de Hadoop? | Microsoft Docs"
description: "Introducción a HDInsight y a la pila de tecnología y los componentes de Hadoop, incluidos Spark, Kafka, Hive y HBase para el análisis de macrodatos."
keywords: "azure hadoop, hadoop azure, hadoop intro, hadoop introducción, pila de tecnología de hadoop, intro de hadoop, introducción a hadoop, qué es un clúster de hadoop, qué es clúster hadoop, para qué se utiliza hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/11/2017
ms.author: cgronlun
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 7d1f52550ca2b50e9536606d0f0099f4bf0f1e5e
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Introducción a Azure HDInsight, la pila de tecnología de Hadoop y los clústeres de Hadoop
 Este artículo proporciona una introducción a Azure HDInsight, una distribución en la nube de la pila de tecnología de Hadoop. También explica qué es un clúster de Hadoop y cuándo se utilizaría. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>¿Qué son HDInsight y la pila de tecnología de Hadoop? 
Azure HDInsight es una distribución en la nube de los componentes de Hadoop de **Hortonworks Data Platform (HDP)**. [Apache Hadoop](http://hadoop.apache.org/) era el entorno de trabajo de código abierto original para el procesamiento distribuido y análisis de macrodatos en clústeres de equipos. 

HDInsight hace más fácil de usar las tecnologías de Hadoop, con:

*    Menos configuración. Consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
*    Alta disponibilidad y confiabilidad. Consulte [Disponibilidad y confiabilidad de HDInsight](hdinsight-high-availability-linux.md).
*    Seguridad y gobierno mediante la integración con Active Directory. Consulte [Clústeres unidos al dominio](hdinsight-domain-joined-introduction.md).
*    Escalado dinámico sin interrupción de trabajos
*   Actualizaciones de componentes y versiones actuales. Consulte [Componentes y versiones de Haddoop en HDInsight][component-versioning].
*   Integración con otros servicios de Azure, como [Web Apps](https://docs.microsoft.com/azure/app-service-web/) y [SQL Database](https://docs.microsoft.com/azure/sql-database/)

La pila de tecnología de Hadoop incluye software relacionado y utilidades, incluidas Apache Hive, HBase, Spark, Kafka y muchas otras. Para más información sobre Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>¿Qué es un clúster de Hadoop y cuándo se utiliza?
El término *Hadoop* también hace referencia a un tipo de clúster que tiene:

* El sistema de archivos distribuido de Hadoop (HDFS)
* YARN para programación de trabajos y administración de recursos
* MapReduce para procesamiento en paralelo
  
Los clústeres de Hadoop se suelen usar para el procesamiento por lotes de datos almacenados. Otros tipos de clústeres de HDInsight tienen funcionalidades adicionales, como procesamiento más rápido y en memoria o el procesamiento de la transmisión de colas de mensajes. Consulte [Tipos de clúster de HDInsight](#overview) para más información.

## <a name="what-is-big-data"></a>¿Qué son grandes volúmenes de datos?
Por macrodatos se entiende cualquier volumen grande de información digital, tales como:

* Una fuente de noticias de Twitter
* Datos de sensores de equipos industriales
* Actividad de los clientes recopilada en un sitio web

Los macrodatos pueden ser históricos (es decir, datos almacenados) o en tiempo real (es decir, transferidos directamente desde el origen). Los macrodatos se recopilan en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos.

## <a name="overview"></a>Tipos de clúster de HDInsight
HDInsight es una distribución en la nube en Microsoft Azure del componente tecnológico de Apache Hadoop en rápida expansión para el análisis de macrodatos. Incluye tipos de clúster concretos y funcionalidades de personalización del clúster, tales como agregar componentes, utilidades y lenguajes.

### <a name="spark-kafka-interactive-hive-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Interactive Hive, HBase, personalizado y otros tipos de clúster
HDInsight ofrece los siguientes tipos de clúster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: utiliza [HDFS](#hdfs), administración de recursos [YARN](#yarn) y un modelo de programación de [MapReduce](#mapreduce) simple para procesar y analizar datos en paralelo.
* **[Apache Spark](http://spark.apache.org/)**: marco de procesamiento paralelo que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos, trabajos de Spark para SQL, datos de streaming y aprendizaje automático. Consulte [¿qué es Apache Spark en HDInsight?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: base de datos NoSQL en Hadoop que proporciona acceso aleatorio y gran coherencia para grandes cantidades de datos no estructurados y semiestructurados; potencialmente miles de millones de filas multiplicadas por millones de columnas. Consulte [¿qué es HBase en HDInsight?](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: un servidor para hospedar y administrar procesos de R distribuidos en paralelo. Proporciona a los científicos de datos, estadísticos y programadores de R acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight. [Información general de R Server en HDInsight](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: sistema distribuido de cálculo en tiempo real para el procesamiento rápido de grandes transmisiones de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop](hdinsight-storm-sensor-data-analysis.md).
* **[Versión preliminar de Apache Interactive Hive (también conocido como Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: almacenamiento en caché en memoria para consultas de Hive interactivas y más rápidas. Consulte [Uso de Interactive Hive en HDInsight](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)**: una plataforma de código abierto usada para crear canalizaciones y aplicaciones de datos de streaming. Kafka también proporciona funcionalidad de cola de mensajes que le permite publicar flujos de datos y suscribirse a ellos. Consulte [Introducción a Apache Kafka en HDInsight](hdinsight-apache-kafka-introduction.md).
* **[Versión preliminar de clústeres unidos a dominio](hdinsight-domain-joined-introduction.md)**: un clúster unido a un dominio de Active Directory para que pueda controlar el acceso a los datos y manejarlos.
* **[Clústeres personalizados con acciones de script](hdinsight-hadoop-customize-cluster-linux.md)**: clústeres con scripts que se ejecutan durante el aprovisionamiento y que instalan componentes.

### <a name="example-cluster-customization-scripts"></a>Scripts de personalización de clúster de ejemplo
Las acciones de script son scripts de Bash en Linux que se ejecutan durante el aprovisionamiento del clúster y que se pueden usar para instalar componentes adicionales en el clúster. 

Los siguientes scripts de ejemplo los ofrece el equipo de HDInsight:

* **[Hue](hdinsight-hadoop-hue-linux.md)**: conjunto de aplicaciones web que se usan para interactuar con un clúster. Solo clústeres Linux.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: procesamiento de gráficos para modelar las relaciones entre objetos o usuarios.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: plataforma de búsqueda de escala empresarial que permite la búsqueda de texto completo en los datos.

Para obtener información sobre el desarrollo de sus propias acciones de script, consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>Componentes y utilidades en clústeres de HDInsight
Se incluyen los siguientes componentes y utilidades en clústeres de HDInsight:

* **[Ambari](#ambari)**: aprovisionamiento, administración, supervisión y utilidades de clústeres.
* **[Avro](#avro)** (biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno de Microsoft .NET. 
* **[Hive y HCatalog](#hive)**: consultas tipo SQL y una capa de administración de almacenamiento y de tablas.
* **[Mahout](#mahout)**: para aplicaciones de aprendizaje automático escalables.
* **[MapReduce](#mapreduce)**: marco heredado para el procesamiento distribuido y la administración de recursos de Hadoop. Consulte [YARN](#yarn).
* **[Oozie](#oozie)**: administración de flujo de trabajo.
* **[Phoenix](#phoenix)**: capa de base de datos relacional sobre HBase.
* **[Pig](#pig)**: scripts más sencillos para transformaciones de MapReduce.
* **[Sqoop](#sqoop)**: importación y exportación de datos.
* **[Tez](#tez)**: permite a los procesos con muchos datos ejecutarse de forma eficaz a escala.
* **[YARN](#yarn)**: administración de recursos que forma parte de la biblioteca básica de Hadoop.
* **[ZooKeeper](#zookeeper)**: coordinación de procesos en sistemas distribuidos.

> [!NOTE]
> Para más información sobre los componentes específicos y sus versiones, consulte [Componentes y versiones de Hadoop HDInsight][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Apache Ambari se utiliza para aprovisionar, administrar y supervisar clústeres de Apache Hadoop. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Los clústeres de HDInsight en Linux proporcionan la interfaz de usuario web de Ambari y la API REST de Ambari. Las Vistas de Ambari en clústeres de HDInsight admiten funcionalidades de IU de complementos.
Consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md) y <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referencia de la API de Apache Ambari</a>.

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)
La biblioteca de Microsoft .NET para Avro implementa el formato compacto de intercambio de datos binarios de Apache Avro para la serialización del entorno de Microsoft .NET. Define un esquema independiente del idioma para que los datos serializados en un idioma puedan leerse en otro. Encontrará información detallada sobre el formato en <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html"> Apache Avro Specification</a> (Especificaciones de Apache Avro). El formato de archivos de Avro es compatible con el modelo de programación distribuido de MapReduce: los archivos son "divisibles", lo que significa que puede buscar cualquier punto en un archivo y empezar a leer desde un bloque específico. Para descubrir cómo, consulte [Serialización de datos con la biblioteca de Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md). Compatibilidad con clústeres basados en Linux.

### <a name="hdfs"></a>HDFS
El sistema de archivos distribuido de Hadoop (HDFS) es un sistema que, junto con MapReduce y YARN, conforma el núcleo de la tecnología de Hadoop. Es el sistema de archivos estándar para los clústeres de Hadoop en HDInsight. Consulte [Consultar datos de un almacenamiento compatible con HDFS](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive y HCatalog
<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> es el software de almacenamiento de datos basado en Hadoop que le permite consultar y administrar grandes conjuntos de datos en almacenamiento distribuido con un lenguaje de tipo SQL denominado HiveQL. Hive, como Pig, es una abstracción por encima de MapReduce y convierte las consultas en una serie de trabajos de MapReduce. Hive se encuentra conceptualmente más cerca de un sistema de administración de bases de datos relacionales que Pig y se utiliza con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Consulte el artículo sobre el [uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> es una capa de administración de almacenamiento y tablas para Hadoop que presenta una vista relacional de los datos. En HCatalog, se pueden leer y escribir archivos en cualquier formato para el que se pueda escribir un SerDe de Hive (serializador-deserializador).

### <a name="mahout"></a>Mahout
<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> es una biblioteca de algoritmos de aprendizaje automático que se ejecuta en Hadoop. Mediante principios de estadísticas, las aplicaciones de aprendizaje automático enseñan a los sistemas a aprender de los datos y a usar los resultados obtenidos en el pasado para determinar el comportamiento en el futuro. Consulte [Generación de recomendaciones de películas mediante Mahout en Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce es el marco de software heredado para permitir a Hadoop la escritura de aplicaciones con el fin de procesar en lote conjuntos de macrodatos en paralelo. Un trabajo de MapReduce divide conjuntos de datos de gran tamaño y organiza los datos en pares clave-valor para su procesamiento. Los trabajos de MapReduce se ejecutan en [YARN](#yarn). Consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> en el sitio Wiki de Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java. Consulte [Uso de Oozie con Hadoop para definir y ejecutar un flujo de trabajo en HDInsight](hdinsight-use-oozie-linux-mac.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> es una capa de base de datos relacional sobre HBase. Phoenix incluye un controlador JDBC que permite consultar y administrar tablas SQL directamente. Phoenix traduce consultas y otras instrucciones en llamadas nativas de la API NoSQL nativas en lugar de usar MapReduce, lo que permite aplicaciones más rápidas además de los almacenes NoSQL. Consulte [Usar Apache Phoenix y SQuirreL con clústeres de HBase](hdinsight-hbase-phoenix-squirrel.md).

### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> es una plataforma de alto nivel que permite realizar transformaciones complejas de MapReduce en conjuntos de datos de gran tamaño con un sencillo lenguaje de scripting denominado Pig Latin. Pig traduce los scripts de Pig Latin para que se ejecuten en Hadoop. Se pueden crear funciones definidas por el usuario (UDF) para ampliar Pig Latin. Consulte [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL, u otros almacenes de datos estructurados, de la manera más eficiente posible. Consulte [Uso de Sqoop con Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> es un marco de aplicación basado en Hadoop YARN que ejecuta gráficos complejos y acíclicos general de procesamiento de datos generales. Es un sucesor más flexible y eficaz al marco de MapReduce que permite procesos intensivos de datos, como Hive, para ejecutar de manera más eficaz a escala. Consulte ["Usar Apache Tez para un rendimiento mejorado" en Usar Hive y HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN es la siguiente generación de MapReduce (MapReduce 2.0, o MRv2) y es compatible con escenarios de procesamiento de datos más allá del procesamiento por lotes de MapReduce con mayor escalabilidad y procesamiento en tiempo real. YARN proporciona un marco de aplicación distribuida y administración de recursos. Los trabajos de MapReduce se ejecutan en YARN. Consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina procesos en grandes sistemas distribuidos por medio de un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contiene pequeñas cantidades de metadatos necesarios para la coordinación de procesos: estado, ubicación, configuración, entre otros. Ver un ejemplo de [ZooKeeper con un clúster de HBase y Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md). 

## <a name="programming-languages-on-hdinsight"></a>Lenguajes de programación en HDInsight
Los clústeres de HDInsight, como Spark, HBase, Kafka, Hadoop y otros clústeres, admiten varios lenguajes de programación, aunque algunos no están instalados de manera predeterminada. En el caso de las bibliotecas, los módulos o los paquetes no instalados de manera predeterminada, [instale el componente con una acción de script](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Compatibilidad con lenguajes de programación predeterminados
De forma predeterminada, los clústeres de HDInsight admiten lo siguiente:

* Java
* Python

Se pueden instalar lenguajes adicionales mediante [acciones de script](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Lenguajes de la máquina virtual de Java (JVM)
En una máquina virtual de Java se pueden utilizar muchos lenguajes distintos de Java; sin embargo, la ejecución de algunos de estos lenguajes puede requerir la instalación de componentes adicionales en el clúster.

Estos lenguajes basados en JVM son compatibles con clústeres de HDInsight:

* Clojure
* Jython (Python para Java)
* Scala

### <a name="hadoop-specific-languages"></a>Lenguajes específicos de Hadoop
Los clústeres de HDInsight son compatibles con los siguientes lenguajes específicos de la pila de tecnología de Hadoop:

* Pig Latin para trabajos de Pig
* HiveQL para trabajos de Hive y SparkSQL

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard y HDInsight Premium
HDInsight proporciona ofertas en la nube de macrodatos en dos categorías, Estándar y Premium. HDInsight Estándar proporciona un clúster de escala empresarial que las organizaciones pueden usar para ejecutar sus cargas de trabajo de macrodatos. HDInsight Premium va más allá de las funcionalidades estándar y proporciona funcionalidades avanzadas de análisis y seguridad para un clúster de HDInsight. Para más información, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>Microsoft Business Intelligence y HDInsight
Las herramientas de Business Intelligence (BI) habituales recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver:

* [Conexión de Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster de HDInsight mediante Microsoft Power Query para Excel. Se requiere una estación de trabajo de Windows. 
* [Conexión de Excel a Hadoop con el controlador ODBC de Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md): aprenda a importar datos desde HDInsight con el controlador ODBC de Microsoft Hive. Se requiere una estación de trabajo de Windows. 
* [Plataforma en la nube de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Hadoop en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): un tutorial de inicio rápido sobre el aprovisionamiento de clústeres de Hadoop para HDInsight y la ejecución de consultas de ejemplo de Hive.
* [Introducción a Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): un tutorial de inicio rápido para crear un clúster Spark y ejecutar consultas Spark SQL interactivas.
* [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md): empiece a utilizar R Server en HDInsight Premium.
* [Aprovisionamiento de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md): aprenda a aprovisionar un clúster Hadoop de HDInsight a través de Azure Portal, la CLI de Azure o Azure PowerShell.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
