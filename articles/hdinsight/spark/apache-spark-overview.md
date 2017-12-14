---
title: "Introducción a Spark en Azure HDInsight | Microsoft Docs"
description: "En este artículo se proporciona una introducción a Spark en HDInsight y los diferentes escenarios en los que puede usar un clúster de Spark en HDInsight."
keywords: "qué es apache spark, clúster de spark, introducción a spark, spark en hdinsight"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/13/2017
ms.author: nitinme
ms.openlocfilehash: b52f896c0d2a023a0a371668c4f6ce55060c2cfd
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="introduction-to-spark-on-hdinsight"></a>Introducción a Spark en HDInsight

En este artículo se proporciona una introducción a Spark en HDInsight. <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. El clúster de Spark en HDInsight es compatible con Azure Storage (WASB), así como con Azure Data Lake Store. Por lo tanto, los datos existentes almacenados en Azure se pueden procesar fácilmente a través de un clúster de Spark.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Cuando crea un clúster de Spark en HDInsight, aprovisiona recursos de proceso de Azure con Spark instalado y configurado. Apenas se tardan unos diez minutos en crear un clúster Spark en HDInsight. Los datos que se van a procesar se almacenan en Azure Storage o Azure Data Lake Store. Consulte [Uso de Azure Storage con HDInsight](../hdinsight-hadoop-use-blob-storage.md).

![Spark: un marco unificado](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="spark-vs-traditional-mapreduce"></a>Comparación de Spark y el tradicional MapReduce

¿Por qué Spark es tan rápido? ¿En qué se diferencia la arquitectura de Apache Spark del tradicional MapReduce, que permite ofrecer un mejor rendimiento de uso compartido de datos?

![Comparación del tradicional MapReduce y Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Spark proporciona primitivas de computación de clúster en memoria. Un trabajo de Spark puede cargar y almacenar en la memoria caché datos, y repetir consultas sobre ellos, mucho más rápidamente que los sistemas basados en disco. Spark también se integra en el lenguaje de programación de Scala para que pueda manipular conjuntos de datos distribuidos como colecciones locales. No se necesita estructurar todo como operaciones de asignación y reducción.

En Spark, el uso compartido de datos entre las operaciones es más rápido, ya que los datos están en memoria. En cambio, Hadoop comparte datos a través de HDFS, cuyo procesamiento tarda más.

## <a name="what-is-apache-spark-on-azure-hdinsight"></a>¿Qué es Apache Spark en Azure HDInsight?
Los clústeres de Spark en HDInsight ofrecen un servicio de Spark completamente administrado. Aquí se enumeran las ventajas de crear un clúster de Spark en HDInsight.

| Característica | Descripción |
| --- | --- |
| Facilidad de creación de clústeres de Spark |Puede crear un clúster de Spark en HDInsight en cuestión de minutos mediante Azure Portal, Azure PowerShell o el SDK de .NET de HDInsight. Consulte [Introducción al clúster de Spark en HDInsight](apache-spark-jupyter-spark-sql.md) |
| Facilidad de uso |El clúster de Spark en HDInsight incluye notebooks de Jupyter y Zeppelin. Puede usar estos notebooks para el procesamiento y la visualización de datos interactivos.|
| API de REST |Los clústeres de Spark en HDInsight incluyen [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un servidor de trabajos de Spark basado en una API de REST para enviar y supervisar trabajos de forma remota. |
| Compatibilidad con Almacén de Azure Data Lake | Se puede configurar un clúster de Spark en HDInsight para que utilice Azure Data Lake Store como almacenamiento adicional, además de como almacenamiento principal (solo con clústeres de HDInsight 3.5). Para más información acerca del Almacén de Data Lake, consulte la página con [información general del Almacén de Azure Data Lake](../../data-lake-store/data-lake-store-overview.md). |
| Integración con servicios de Azure |Un clúster de Spark en HDInsight incluye un conector a Azure Event Hubs. Los clientes pueden compilar aplicaciones de streaming con Event Hubs, además de con [Kafka](http://kafka.apache.org/), que ya está disponible como parte de Spark. |
| Compatibilidad con R Server | Puede configurar R Server en el clúster Spark de HDInsight para ejecutar cálculos R distribuidos con las velocidades prometidas mediante un clúster Spark. Para obtener más información, consulte [Get started using R Server on HDInsight](../r-server/r-server-get-started.md)(Introducción a R Server en HDInsight). |
| Integración con IDE de terceros | HDInsight proporciona complementos para IDE tales como IntelliJ IDEA y Eclipse que puede usar para crear y enviar solicitudes a un clúster de Spark en HDInsight. Para más información, consulte artículo sobre el [uso del kit de herramientas de Azure para IntelliJ IDEA](apache-spark-intellij-tool-plugin.md) y el [uso del kit de herramientas de Azure para Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Consultas simultáneas |Los clústeres de Spark en HDInsight admiten consultas simultáneas. Esto permite que varias consultas de un usuario o varias consultas de diversos usuarios y aplicaciones compartan los mismos recursos de clúster. |
| Almacenamiento en caché en SSD |Puede almacenar datos en caché, ya sea en memoria o en SSD conectadas a los nodos del clúster. El almacenamiento en memoria proporciona el mejor rendimiento para las consultas pero podría ser costoso; el almacenamiento en SSD ofrece una opción excelente para mejorar el rendimiento de las consultas sin necesidad de crear un clúster de un tamaño que admita todo el conjunto de datos en memoria. |
| Integración con herramientas de BI |Los clústeres de Spark en HDInsight ofrecen conectores para herramientas de BI como [Power BI](http://www.powerbi.com/) y [Tableau](http://www.tableau.com/products/desktop) para el análisis de datos. |
| Bibliotecas de Anaconda precargadas |Los clústeres Spark en HDInsight incluyen bibliotecas de Anaconda preinstaladas. [Anaconda](http://docs.continuum.io/anaconda/) ofrece prácticamente 200 bibliotecas para el aprendizaje automático, el análisis de datos, la visualización, etc. |
| Escalabilidad |Aunque puede especificar el número de nodos del clúster durante la creación, puede que desee aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres de HDInsight le permiten cambiar el número de nodos del clúster. Además, se pueden quitar clústeres de Spark sin que se pierdan datos, ya que todos están almacenados en Azure Storage o Data Lake Store. |
| Soporte técnico ininterrumpido |Los clústeres de Spark en HDInsight incluyen soporte técnico ininterrumpido de nivel empresarial y un Acuerdo de Nivel de Servicio del 99,9 % de tiempo de actividad. |

## <a name="spark-cluster-architecture"></a>Arquitectura de clúster de Spark

Aquí se explica la arquitectura de clúster de Spark y cómo funciona:

![Arquitectura de clúster de Spark](./media/apache-spark-overview/spark-architecture.png)

El nodo principal tiene el principal de Spark que administra el número de aplicaciones, las aplicaciones se asignan a los controladores de Spark. Todas las aplicaciones se administran mediante el principal de Spark de varias maneras. Spark puede implementarse sobre Mesos, YARN o el Administrador de clústeres de Spark, que asigna recursos de nodo de trabajo a una aplicación. En HDInsight, Spark se ejecuta mediante el Administrador de clústeres YARN. Los recursos del clúster se administran mediante el principal de Spark en HDInsight. Esto significa que el principal de Spark conoce qué recursos, como la memoria, están ocupados o disponibles en el nodo de trabajo.

El controlador ejecuta la función principal del usuario y las distintas operaciones paralelas en los nodos de trabajo. A continuación, recopila los resultados de las operaciones. Los nodos de trabajo leen y escriben datos del sistema de archivos distribuido de Hadoop (HDFS) y también en él. Además, los nodos de trabajo almacenan en caché datos transformados en memoria como conjuntos de datos distribuidos resistentes (RDD).

Una vez creada la aplicación en el nodo principal de Spark, este asigna los recursos a las aplicaciones al crear una ejecución denominada controlador de Spark. El controlador de Spark también crea SparkContext y comienza a crear los RDD. Los metadatos de los RDD se almacenan en el controlador de Spark.

El controlador de Spark se conecta al nodo principal de Spark y es responsable de convertir una aplicación en un grafo dirigido (DAG) de las tareas individuales que se ejecutan dentro de un proceso ejecutor en los nodos de trabajo. Cada aplicación obtiene sus propios procesos ejecutores, que permanecen mientras dure la aplicación y ejecutan tareas en varios subprocesos.

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>¿Cuáles son los casos de uso para Spark en HDInsight?
Los clústeres de Spark en HDInsight hacen posibles los siguientes escenarios clave:

### <a name="interactive-data-analysis-and-bi"></a>Análisis interactivo de datos y BI
[Vea un tutorial](apache-spark-use-bi-tools.md)

Apache Spark en HDInsight almacena datos en Azure Storage o Azure Data Lake Store. Los expertos de la empresa y los responsables de la toma de decisiones clave pueden analizar esos datos y generar informes con ellos, así como usar Microsoft Power BI para crear informes interactivos a partir de los datos analizados. Los analistas pueden comenzar a partir de datos no estructurados y semiestructurados presentes en el almacenamiento de clúster, definir un esquema de los datos mediante notebooks y luego generar modelos de datos mediante Microsoft Power BI. Los clústeres de Spark en HDInsight también admiten varias herramientas de BI de terceros, como Tableau, por lo que es una plataforma ideal para analistas de datos, expertos de empresa y responsables de la toma de decisiones clave.

### <a name="spark-machine-learning"></a>Machine Learning con Spark
[Mire un tutorial: predecir las temperaturas de edificios con datos HVAC](apache-spark-ipython-notebook-machine-learning.md)

[Mire un tutorial: predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)

Apache Spark incluye [MLlib](http://spark.apache.org/mllib/), una biblioteca de aprendizaje automático basada en Spark que puede usar desde un clúster de Spark en HDInsight. El clúster de Spark en HDInsight también incluye Anaconda, una distribución de Python con diversos paquetes para aprendizaje automático. Si agregamos a esto la compatibilidad integrada con notebooks de Jupyter y Zeppelin, dispone de un entorno de primera línea para la creación de aplicaciones de aprendizaje automático.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Streaming y análisis de datos en tiempo real con Spark
[Vea un tutorial](apache-spark-eventhub-streaming.md)

Los clústeres de Spark en HDInsight ofrecen amplia compatibilidad para crear soluciones de análisis en tiempo real. Mientras que Spark ya posee conectores para insertar datos de varios orígenes como los sockets TCP, Flume, Twitter, ZeroMQ o Kafka, Spark en HDInsight agrega compatibilidad de primera clase para insertar datos desde Azure Event Hubs. Event Hubs son el servicio de cola más usado de Azure. La disponibilidad inmediata de la compatibilidad con Event Hubs convierte a los clústeres de Spark en HDInsight en una plataforma ideal para crear la canalización de análisis en tiempo real.

## <a name="next-steps"></a>¿Qué componentes se incluyen como parte de un clúster Spark?
Los clústeres de Spark en HDInsight incluyen los siguientes componentes que están disponibles en los clústeres de manera predeterminada.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Incluye Spark Core, Spark SQL, API Spark de streaming, GraphX y MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

Los clústeres de Spark en HDInsight también ofrecen un [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para la conectividad con clústeres de Spark en HDInsight desde herramientas de inteligencia empresarial como Microsoft Power BI y Tableau.

## <a name="where-do-i-start"></a>¿Por dónde empiezo?
Empiece por crear un clúster de Spark en HDInsight. Consulte el [inicio rápido sobre cómo crear un clúster de Spark en HDInsight Linux y ejecutar consultas interactivas mediante Jupyter](apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Pasos siguientes
### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)
* [Problemas conocidos de Apache Spark en Azure HDInsight](apache-spark-known-issues.md).
