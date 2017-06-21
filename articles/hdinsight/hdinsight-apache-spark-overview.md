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
ms.date: 05/12/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: acb80aa98cc978a906ccd6e4b4132a439e505bc8
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="introduction-to-spark-on-hdinsight"></a>Introducción a Spark en HDInsight

En este artículo se proporciona una introducción a Spark en HDInsight. <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. Un clúster de Spark en HDInsight es compatible con Azure Storage (WASB), así como con Azure Data Lake Store, por lo que los datos existentes almacenados en Azure pueden procesarse fácilmente por medio de un clúster de Spark.

Cuando crea un clúster de Spark en HDInsight, aprovisiona recursos de proceso de Azure con Spark instalado y configurado. Solamente se tardan unos diez minutos en crear un clúster Spark en HDInsight. Los datos que se van a procesar se almacenan en Azure Storage o Azure Data Lake Store. Consulte [Uso de Azure Storage con HDInsight](hdinsight-hadoop-use-blob-storage.md).

**Para crear un clúster de Spark en HDInsight**, consulte el [inicio rápido sobre cómo crear un clúster de Spark en HDInsight y ejecutar consultas interactivas mediante Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="what-is-apache-spark-on-azure-hdinsight"></a>¿Qué es Apache Spark en Azure HDInsight?
Los clústeres de Spark en HDInsight ofrecen un servicio de Spark completamente administrado. Aquí se enumeran las ventajas de crear un clúster de Spark en HDInsight.

| Característica | Descripción |
| --- | --- |
| Facilidad de creación de clústeres de Spark |Puede crear un clúster de Spark en HDInsight en cuestión de minutos mediante Azure Portal, Azure PowerShell o el SDK de .NET de HDInsight. Consulte [Introducción al clúster de Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidad de uso |El clúster de Spark en HDInsight incluye notebooks de Jupyter y Zeppelin. Puede usarlos para el procesamiento y la visualización de datos interactivos.|
| API de REST |Los clústeres de Spark en HDInsight incluyen [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un servidor de trabajos de Spark basado en una API de REST para enviar y supervisar trabajos de forma remota. |
| Compatibilidad con Almacén de Azure Data Lake | Se puede configurar un clúster de Spark en HDInsight para que utilice Azure Data Lake Store como almacenamiento adicional, además de como almacenamiento principal (solo con clústeres de HDInsight 3.5). Para más información acerca del Almacén de Data Lake, consulte la página con [información general del Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md). |
| Integración con servicios de Azure |Un clúster de Spark en HDInsight incluye un conector a Azure Event Hubs. Los clientes pueden compilar aplicaciones de streaming con los Centros de eventos, además de con [Kafka](http://kafka.apache.org/), que ya está disponible como parte de Spark. |
| Compatibilidad con R Server | Puede configurar R Server en el clúster Spark de HDInsight para ejecutar cálculos R distribuidos con las velocidades prometidas mediante un clúster Spark. Para obtener más información, consulte [Get started using R Server on HDInsight](hdinsight-hadoop-r-server-get-started.md)(Introducción a R Server en HDInsight). |
| Integración con IDE de terceros | HDInsight proporciona complementos para IDE tales como IntelliJ IDEA y Eclipse que puede usar para crear y enviar solicitudes a un clúster de Spark en HDInsight. Para más información, consulte [Uso del kit de herramientas de Azure para IDEA IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) y [Uso del kit de herramientas de Azure para Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md).|
| Consultas simultáneas |Los clústeres de Spark en HDInsight admiten consultas simultáneas. Esto permite que varias consultas de un usuario o varias consultas de diversos usuarios y aplicaciones compartan los mismos recursos de clúster. |
| Almacenamiento en caché en SSD |Puede almacenar datos en caché, ya sea en memoria o en SSD conectadas a los nodos del clúster. El almacenamiento en memoria proporciona el mejor rendimiento para las consultas pero podría ser costoso; el almacenamiento en SSD ofrece una opción excelente para mejorar el rendimiento de las consultas sin necesidad de crear un clúster de un tamaño que admita todo el conjunto de datos en memoria. |
| Integración con herramientas de BI |Los clústeres de Spark en HDInsight ofrecen conectores para herramientas de BI como [Power BI](http://www.powerbi.com/) y [Tableau](http://www.tableau.com/products/desktop) para el análisis de datos. |
| Bibliotecas de Anaconda precargadas |Los clústeres Spark en HDInsight incluyen bibliotecas de Anaconda preinstaladas. [Anaconda](http://docs.continuum.io/anaconda/) ofrece prácticamente 200 bibliotecas para el aprendizaje automático, el análisis de datos, la visualización, etc. |
| Escalabilidad |Aunque puede especificar el número de nodos del clúster durante la creación, puede que desee aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres de HDInsight le permiten cambiar el número de nodos del clúster. Además, se pueden quitar clústeres de Spark sin que se pierdan datos, ya que todos están almacenados en Azure Storage o Data Lake Store. |
| Soporte técnico ininterrumpido |Los clústeres de Spark en HDInsight incluyen soporte técnico ininterrumpido de nivel empresarial y un Acuerdo de Nivel de Servicio del 99,9 % de tiempo de actividad. |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>¿Cuáles son los casos de uso para Spark en HDInsight?
Los clústeres de Spark en HDInsight hacen posibles los siguientes escenarios clave.

### <a name="interactive-data-analysis-and-bi"></a>Análisis interactivo de datos y BI
[Vea un tutorial](hdinsight-apache-spark-use-bi-tools.md)

Apache Spark en HDInsight almacena datos en Azure Storage o Azure Data Lake Store. Los expertos de la empresa y los responsables de la toma de decisiones clave pueden analizar esos datos y generar informes con ellos, así como usar Microsoft Power BI para crear informes interactivos a partir de los datos analizados. Los analistas pueden comenzar a partir de datos no estructurados y semiestructurados presentes en el almacenamiento de clúster, definir un esquema de los datos mediante notebooks y luego generar modelos de datos mediante Microsoft Power BI. Los clústeres de Spark en HDInsight también admiten varias herramientas de BI de terceros, como Tableau, por lo que es una plataforma ideal para los analistas de datos, expertos de empresa y responsables de la toma de decisiones clave.

### <a name="spark-machine-learning"></a>Aprendizaje automático con Spark
[Mire un tutorial: predecir las temperaturas de edificios con datos HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Mire un tutorial: predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark incluye [MLlib](http://spark.apache.org/mllib/), una biblioteca de aprendizaje automático basada en Spark que puede usar desde un clúster de Spark en HDInsight. El clúster de Spark en HDInsight también incluye Anaconda, una distribución de Python con diversos paquetes para aprendizaje automático. Si agregamos a esto la compatibilidad integrada con notebooks de Jupyter y Zeppelin, dispone de un entorno de primera línea para la creación de aplicaciones de aprendizaje automático.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Streaming y análisis de datos en tiempo real con Spark
[Vea un tutorial](hdinsight-apache-spark-eventhub-streaming.md)

Los clústeres de Spark en HDInsight ofrecen amplia compatibilidad para crear soluciones de análisis en tiempo real. Mientras que Spark ya posee conectores para insertar datos de varios orígenes como los sockets TCP, Flume, Twitter, ZeroMQ o Kafka, Spark en HDInsight agrega compatibilidad de primera clase para insertar datos desde Centros de eventos de Azure. Los Centros de eventos son el servicio de cola más usado en Azure. La disponibilidad inmediata de la compatibilidad con Event Hubs convierte a los clústeres de Spark en HDInsight en una plataforma ideal para crear la canalización de análisis en tiempo real.

## <a name="next-steps"></a>¿Qué componentes se incluyen como parte de un clúster Spark?
Los clústeres de Spark en HDInsight incluyen los siguientes componentes que están disponibles en los clústeres de manera predeterminada.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Incluye Spark Core, Spark SQL, API Spark de streaming, GraphX y MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)
* [Zeppelin Notebook](http://zeppelin-project.org/)

Los clústeres de Spark en HDInsight también ofrecen un [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para la conectividad con clústeres de Spark en HDInsight desde herramientas de inteligencia empresarial como Microsoft Power BI y Tableau.

## <a name="where-do-i-start"></a>¿Por dónde empiezo?
Empiece por crear un clúster de Spark en HDInsight. Consulte el [inicio rápido sobre cómo crear un clúster de Spark en HDInsight Linux y ejecutar consultas interactivas mediante Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Pasos siguientes
### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)
* [Problemas conocidos de Apache Spark en Azure HDInsight](hdinsight-apache-spark-known-issues.md).

