---
title: "¿Qué son HDInsight y la pila de tecnología de Hadoop y Spark? -Azure | Microsoft Docs"
description: "Introducción a HDInsight y a la pila de tecnología y los componentes de Hadoop y Spark, incluidos Kafka, Hive, Storm y HBase para el análisis de macrodatos."
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
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: c53c4eba6d46c03bbfc6bb316ae4e505abb7b781
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="introduction-to-azure-hdinsight-and-the-hadoop-and-spark-technology-stack"></a>Introducción a Azure HDInsight y la pila de tecnología de Hadoop y Spark
En este artículo se proporciona una introducción a Azure HDInsight. Azure HDInsight es un servicio de análisis, de código abierto, espectro completo y totalmente administrado para empresas. Puede usar plataformas de código abierto como Hadoop, Spark, Hive, LLAP, Kafka, Storm, R, etc. 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

[Apache Hadoop](http://hadoop.apache.org/) era el entorno de trabajo de código abierto original para el procesamiento distribuido y análisis de macrodatos en clústeres. La pila de tecnología de Hadoop incluye software relacionado y utilidades, incluidas Apache Hive, HBase, Spark, Kafka y muchas otras. 

Para ver los componentes disponibles de la pila de tecnología de Hadoop en HDInsight, consulte [¿Cuáles son los componentes y versiones de Hadoop disponibles con HDInsight?][component-versioning] Para más información sobre Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight](https://azure.microsoft.com/services/hdinsight/).

[Apache Spark](http://spark.apache.org) es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. Para más información acerca de Spark en HDInsight, consulte [Introducción a Spark en Azure HDInsight](../spark/apache-spark-overview.md). 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>¿Qué son HDInsight y la pila de tecnología de Hadoop? 
Azure HDInsight es una distribución en la nube de los componentes de Hadoop de [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). Azure HDInsight hace que sea fácil, rápido y rentable procesar grandes cantidades de datos. Puede usar las plataformas de código abierto más conocidas como Hadoop, Spark, Hive, LLAP, Kafka, Storm, R, etc. Con estas plataformas puede habilitar una amplia gama de escenarios, como la extracción, transformación y carga (ETL), el almacenamiento de datos, el aprendizaje automático e IoT.

## <a name="what-is-big-data"></a>¿Qué son grandes volúmenes de datos?

Los macrodatos se recopilan en volúmenes de escala a una mayor velocidad y con una variedad de formatos nunca vista. Pueden ser históricos (es decir, almacenados) o en tiempo real (es decir, transferidos directamente desde el origen). Consulte [Escenarios de uso de HDInsight](#scenarios-for-using-hdinsight) para más información sobre los casos de uso más habituales para macrodatos.

## <a name="why-should-i-use-hdinsight"></a>¿Por qué debo usar HDInsight?

En esta sección se enumeran las funcionalidades de Azure HDInsight.


|Capacidad  |DESCRIPCIÓN  |
|---------|---------|
|Nativo en la nube     |     Azure HDInsight le permite crear clústeres optimizados para [Hadoop](apache-hadoop-linux-tutorial-get-started.md), [Spark](../spark/apache-spark-jupyter-spark-sql.md), [Interactive Query (LLAP)](../interactive-query/apache-interactive-query-get-started.md), [Kafka](../kafka/apache-kafka-get-started.md), [Storm](../storm/apache-storm-tutorial-get-started-linux.md), [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) y  [R Server](../r-server/r-server-get-started.md) en Azure. HDInsight también proporciona un Acuerdo de Nivel de Servicio de un extremo a otro en las cargas de trabajo de producción.  |
|Rentable y escalable     | HDInsight le permite [escalar](../hdinsight-administer-use-portal-linux.md) o reducir verticalmente cargas de trabajo. Puede reducir el costo mediante la  [creación de clústeres a petición](../hdinsight-hadoop-create-linux-clusters-adf.md) y pagar solo por lo que se utiliza. También puede compilar canalizaciones de datos para poner en marcha los trabajos. El procesamiento y el almacenamiento desacoplados ofrecen un mejor rendimiento y flexibilidad. |
|Seguro y conforme    | HDInsight le permite proteger los recursos de datos de la empresa mediante [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), el [cifrado](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) y la integración con [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight también cumple con los [estándares de cumplimiento normativo](https://azure.microsoft.com/overview/trusted-cloud) más conocidos del sector y de la administración.        |
|Supervisión    | Azure HDInsight se integra con [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) para proporcionar una única interfaz con la que puede supervisar todos los clústeres.        |
|Disponibilidad global | HDInsight está disponible en más [regiones](https://azure.microsoft.com/regions/services/) que ninguna otra oferta de análisis de macrodatos. También está disponible en Azure Government, China y Alemania, lo que le permite satisfacer las necesidades de su empresa en áreas soberanas clave. |  
|Productividad     |  Azure HDInsight le permite usar herramientas de productividad muy completas de Hadoop y Spark con los entornos de desarrollo que prefiera. Estos entornos de desarrollo incluyen [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) e [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) para la compatibilidad con Scala, Python, R, Java y .NET. Los científicos de datos también pueden colaborar mediante cuadernos conocidos como los de [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) y [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Extensibilidad     |  Puede ampliar los clústeres de HDInsight con componentes instalados (Hue, Presto, etc.) mediante [acciones de script](../hdinsight-hadoop-customize-cluster-linux.md), la [incorporación de nodos perimetrales](../hdinsight-apps-use-edge-node.md) o la [integración con otras aplicaciones certificadas de macrodatos](../hdinsight-apps-install-applications.md). HDInsight le permite una integración sin problemas con las soluciones de macrodatos más conocidas con una implementación [con un solo clic](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Escenarios de uso de HDInsight

Azure HDInsight se puede usar para una amplia variedad de escenarios de procesamiento de macrodatos. Pueden ser datos históricos (datos ya recopilados y almacenados) o datos en tiempo real (datos que se transmiten directamente desde el origen). Los escenarios de procesamiento de tales datos se pueden resumir en las siguientes categorías: 

### <a name="batch-processing-etl"></a>Procesamiento por lotes (ETL)

El de extracción, transformación y carga (ETL) es un proceso en el que se extraen datos estructurados o no estructurados de orígenes de datos heterogéneos. Estos datos se transforman a un formato estructurado y se cargan en un almacén de datos. Los datos transformados se pueden usar para ciencia de datos o almacenamiento de datos.

### <a name="internet-of-things-iot"></a>Internet de las cosas (IoT)

Puede usar HDInsight para procesar los datos de streaming recibidos en tiempo real desde varios tipos de dispositivos. Para más información, [lea esta entrada de blog de Azure que anuncia la versión preliminar pública de Apache Kafka en HDInsight con Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Arquitectura de HDInsight: Internet de las cosas](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>Ciencia de datos

Puede usar HDInsight para compilar aplicaciones que extraigan información crítica de los datos. También puede usar Azure Machine Learning para predecir tendencias futuras de la empresa. Para más información, [lea este caso de cliente](https://customers.microsoft.com/story/pros).

![Arquitectura de HDInsight: ciencia de datos](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>Almacenamiento de datos

Puede usar HDInsight para realizar consultas interactivas a escalas de petabytes sobre datos estructurados o no estructurados en cualquier formato. También puede generar modelos conectándolos a herramientas de BI. Para más información, [lea este caso de cliente](https://customers.microsoft.com/story/milliman). 

![Arquitectura de HDInsight: almacenamiento de datos](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>Híbrido

Puede usar HDInsight para ampliar la infraestructura local de macrodatos existente en Azure para aprovechar las avanzadas funcionalidades de análisis en la nube.

![Arquitectura de HDInsight: híbrido](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="overview"></a>Tipos de clúster de HDInsight
HDInsight incluye tipos de clúster concretos y funcionalidades de personalización del clúster, tales como la de agregar componentes, utilidades y lenguajes.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Interactive Query, HBase, personalizado y otros tipos de clúster
HDInsight ofrece los siguientes tipos de clúster:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: una plataforma que utiliza [HDFS](#hdfs), administración de recursos [YARN](#yarn) y un modelo de programación de [MapReduce](#mapreduce) simple para procesar y analizar datos por lotes en paralelo.

* **[Apache Spark](http://spark.apache.org/)**: una plataforma de procesamiento paralelo que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. Spark funciona con SQL, datos de streaming y aprendizaje automático. Consulte [¿qué es Apache Spark en HDInsight?](../spark/apache-spark-overview.md)

* **[Apache HBase](http://hbase.apache.org/)**: base de datos NoSQL en Hadoop que proporciona acceso aleatorio y gran coherencia para grandes cantidades de datos no estructurados y semiestructurados; potencialmente miles de millones de filas multiplicadas por millones de columnas. Consulte [¿qué es HBase en HDInsight?](../hbase/apache-hbase-overview.md)

* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: un servidor para hospedar y administrar procesos de R distribuidos en paralelo. Proporciona a los científicos de datos, estadísticos y programadores de R acceso a petición a métodos escalables y distribuidos para realizar análisis en HDInsight. [Información general de R Server en HDInsight](../r-server/r-server-overview.md).

* **[Apache Storm](https://storm.incubator.apache.org/)**: sistema distribuido de cálculo en tiempo real para el procesamiento rápido de grandes transmisiones de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop](../storm/apache-storm-sensor-data-analysis.md).

* **[Versión preliminar de Apache Interactive Query (también conocido como Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: almacenamiento en caché en memoria para consultas de Hive interactivas y más rápidas. Consulte [Uso de Interactive Query en HDInsight](../interactive-query/apache-interactive-query-get-started.md).

* **[Apache Kafka](https://kafka.apache.org/)**: una plataforma de código abierto que se usa para crear canalizaciones y aplicaciones de datos de streaming. Kafka también proporciona funcionalidad de cola de mensajes que le permite publicar flujos de datos y suscribirse a ellos. Consulte [Introducción a Apache Kafka en HDInsight](../kafka/apache-kafka-introduction.md).

## <a name="open-source-components-in-hdinsight"></a>Componentes de código abierto en HDInsight

Azure HDInsight le permite crear clústeres con plataformas de código abierto como Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase y R. De forma predeterminada, estos clústeres incluyen otros componentes de código abierto como [Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](http://avro.apache.org/docs/current/spec.html), [Hive](http://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Mahout](https://mahout.apache.org/), [MapReduce](http://wiki.apache.org/hadoop/MapReduce), [YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Phoenix](http://phoenix.apache.org/), [Pig](http://pig.apache.org/), [Sqoop](http://sqoop.apache.org/), [Tez](http://tez.apache.org/), [Oozie](http://oozie.apache.org/), [ZooKeeper](http://zookeeper.apache.org/).  


## <a name="programming-languages-in-hdinsight"></a>Lenguajes de programación en HDInsight
Los clústeres de HDInsight, como Spark, HBase, Kafka, Hadoop, etc, admiten muchos lenguajes de programación. Aunque algunos no están instalados de manera predeterminada. En el caso de las bibliotecas, los módulos o los paquetes que no se instalan de manera predeterminada, [instale el componente con una acción de script](../hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Compatibilidad con lenguajes de programación predeterminados
De forma predeterminada, los clústeres de HDInsight admiten lo siguiente:

* Java
* Python

Puede instalar lenguajes adicionales mediante [acciones de script](../hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Lenguajes de la máquina virtual de Java (JVM)
Muchos lenguajes distintos de Java se pueden ejecutar en una máquina virtual de Java (JVM). Sin embargo, si ejecuta algunos de estos lenguajes, puede que tenga que instalar componentes adicionales en el clúster.

Los siguientes lenguajes basados en JVM son compatibles con clústeres de HDInsight:

* Clojure
* Jython (Python para Java)
* Scala

### <a name="hadoop-specific-languages"></a>Lenguajes específicos de Hadoop
Los clústeres de HDInsight son compatibles con los siguientes lenguajes específicos de la pila de tecnología de Hadoop:

* Pig Latin para trabajos de Pig
* HiveQL para trabajos de Hive y SparkSQL

## <a name="business-intelligence-on-hdinsight"></a>Inteligencia empresarial en HDInsight
Las herramientas de Business Intelligence (BI) habituales recuperan, analizan y generan informes de datos que se integran en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver:

* [Apache Spark BI mediante herramientas de visualización de datos con Azure HDInsight](../spark/apache-spark-use-bi-tools.md)

* [Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) 

* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Conexión de Excel a Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md): aprenda a conectar Excel a una cuenta de Azure Storage que almacene los datos de su clúster de HDInsight mediante Microsoft Power Query para Excel. Se requiere una estación de trabajo de Windows. 

* [Conexión de Excel a Hadoop con el controlador ODBC de Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md): aprenda a importar datos desde HDInsight con el controlador ODBC de Microsoft Hive. Se requiere una estación de trabajo de Windows. 

* [Plataforma en la nube de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>pasos siguientes

* [Introducción a Hadoop en HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Introducción a Spark en HDInsight](../spark/apache-spark-jupyter-spark-sql.md)
* [Introducción a Kafka en HDInsight](../kafka/apache-kafka-get-started.md)
* [Introducción a Storm en HDInsight](../storm/apache-storm-tutorial-get-started-linux.md)
* [Introducción a HBase en HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [Introducción a Interactive Query (LLAP) en HDInsight](../interactive-query/apache-interactive-query-get-started.md)
* [Introducción a R Server en HDInsight](../r-server/r-server-get-started.md)
* [Administración de clústeres de HDInsight](../hdinsight-administer-use-portal-linux.md)
* [Protección de clústeres de HDInsight](../domain-joined/apache-domain-joined-introduction.md)
* [Supervisión de clústeres de HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
