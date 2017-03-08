---
title: "Información general de Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Introducción a Apache Spark en HDInsight y escenarios en los que usar Spark en HDInsight en sus aplicaciones."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 2cb69f253e1a6d01b412775bbfc37de3423bde0b
ms.lasthandoff: 01/24/2017


---
# <a name="overview-apache-spark-on-hdinsight"></a>Introducción a Apache Spark en HDInsight

<a href="http://spark.apache.org/" target="_blank">Apache Spark</a> es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. Spark también es compatible con el almacenamiento de blobs de Azure (WASB), por lo que se pueden procesar los datos existentes almacenados en Azure fácilmente mediante Spark.

Cuando crea un clúster Spark en HDInsight, aprovisiona recursos de proceso de Azure con Spark instalado y configurado. Solamente se tardan unos diez minutos en crear un clúster Spark en HDInsight. Los datos que se van a renovar se almacenan en el almacenamiento de blobs de Azure. Consulte [Uso de Azure Blob Storage con HDInsight][hdinsight-storage].

![Apache Spark en Azure HDInsight](./media/hdinsight-apache-spark-overview/hdispark.architecture.png "Apache Spark en Azure HDInsight")

**¿Desea empezar a trabajar con Apache Spark en HDInsight de Azure?** Consulte [Inicio rápido: creación de un clúster Spark en HDInsight y ejecución de ejemplos de aplicaciones mediante Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

> [!NOTE]
> Para una lista de los problemas conocidos y las limitaciones con la versión actual, consulte [Problemas conocidos de Apache Spark en Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
> 
> 

## <a name="why-use-spark-on-azure-hdinsight"></a>¿Por qué usar Spark en HDInsight de Azure?
HDInsight de Azure ofrece un servicio Spark completamente administrado. Estas son las ventajas de usar Spark en HDInsight:

| Característica | Description |
| --- | --- |
| Facilidad de creación de clústeres |Puede crear un nuevo clúster Spark en HDInsight en cuestión de minutos mediante el Portal de administración de Azure, Azure PowerShell o el SDK de .NET de HDInsight. Consulte [Introducción al clúster de Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilidad de uso |Spark en clústeres de HDInsight incluye cuadernos de Jupyter preconfigurados. Puede usarlos para el procesamiento y la visualización de datos interactivos. La dirección URL para Jupyter Notebook es https://CLUSTERNAME.azurehdinsight.net/jupyter. Reemplace **CLUSTERNAME** por el nombre del clúster de Spark en HDInsight. |
| API de REST |Spark en HDInsight incluye [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un servidor de trabajo Spark de API de REST, para enviar y supervisar trabajos en ejecución de forma remota. |
| Compatibilidad con Almacén de Azure Data Lake |Se puede configurar Spark en HDInsight para que utilice Azure Data Lake Store como un almacenamiento adicional, así como almacenamiento principal (solo con clústeres de HDInsight 3.5). Para más información acerca del Almacén de Data Lake, consulte la página con [información general del Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md). |
| Integración con servicios de Azure |Spark en HDInsight incluye un conector a los Centros de eventos de Azure. Los clientes pueden compilar aplicaciones de streaming con los Centros de eventos, además de con [Kafka](http://kafka.apache.org/), que ya está disponible como parte de Spark. |
| Compatibilidad con R Server |Puede configurar R Server en el clúster Spark de HDInsight para ejecutar cálculos R distribuidos con las velocidades prometidas mediante un clúster Spark. Para más información, consulte [Introducción al uso de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-get-started.md). |
| Integración con IntelliJ IDEA |Puede usar el complemento de HDInsight para IntelliJ para crear y enviar aplicaciones a un clúster Spark de HDInsight. Para más información, consulte [Uso del complemento Herramientas de HDInsight para IntelliJ IDEA para crear aplicaciones Spark en Scala para clúster HDInsight Spark Linux (versión preliminar)](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Consultas simultáneas |Spark en HDInsight admite las consultas simultáneas. Esto permite que varias consultas de un usuario o varias consultas de diversos usuarios y aplicaciones compartan los mismos recursos de clúster. |
| Almacenamiento en caché en SSD |Puede almacenar datos en caché, ya sea en memoria o en SSD conectadas a los nodos del clúster. El almacenamiento en memoria proporciona el mejor rendimiento para las consultas pero podría ser costoso; el almacenamiento en SSD ofrece una opción excelente para mejorar el rendimiento de las consultas sin necesidad de crear un clúster de un tamaño que admita todo el conjunto de datos en memoria. |
| Integración con herramientas de BI |Spark para HDInsight ofrece conectores para herramientas de BI como [Power BI](http://www.powerbi.com/) y [Tableau](http://www.tableau.com/products/desktop) para el análisis de datos. |
| Bibliotecas de Anaconda precargadas |Los clústeres Spark en HDInsight incluyen bibliotecas de Anaconda preinstaladas. [Anaconda](http://docs.continuum.io/anaconda/) ofrece prácticamente 200 bibliotecas para el aprendizaje automático, el análisis de datos, la visualización, etc. |
| Escalabilidad |Aunque puede especificar el número de nodos del clúster durante la creación, puede que desee aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres de HDInsight le permiten cambiar el número de nodos del clúster. Además, se pueden quitar clústeres Spark sin que se pierdan datos, ya que todos están almacenados en el almacenamiento de blobs de Azure. |
| Soporte técnico ininterrumpido |Spark en HDInsight incluye soporte técnico ininterrumpido de nivel empresarial y un Acuerdo de Nivel de Servicio del 99,9 % de tiempo de actividad. |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>¿Cuáles son los casos de uso para Spark en HDInsight?
Apache Spark en HDInsight permite los siguientes escenarios clave.

### <a name="interactive-data-analysis-and-bi"></a>Análisis interactivo de datos y BI
[Vea un tutorial](hdinsight-apache-spark-use-bi-tools.md)

Apache Spark en HDInsight almacena datos en blobs de Azure. Los expertos de la empresa y los responsables de la toma de decisiones clave pueden analizar esos datos y generar informes con ellos, así como usar Microsoft Power BI para crear informes interactivos a partir de los datos analizados. Los analistas pueden comenzar a partir de datos no estructurados y semiestructurados presentes en el almacenamiento de Azure, definir un esquema de los datos mediante cuadernos y luego generar modelos de datos mediante Microsoft Power BI. Spark en HDInsight también admite varias herramientas de BI de terceros como Tableau, Qlikview y Lumira de SAP, por lo que es una plataforma ideal para los analistas de datos, expertos de empresa y responsables de la toma de decisiones clave.

### <a name="iterative-machine-learning"></a>Aprendizaje automático iterativo
[Mire un tutorial: predecir las temperaturas de edificios con datos HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Mire un tutorial: predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache Spark incluye [MLlib](http://spark.apache.org/mllib/), una biblioteca de aprendizaje automático que se basa en Spark. Además, Spark en HDInsight incluye Anaconda, una distribución de Python con diversos paquetes para Aprendizaje automático. Si agregamos a esto la compatibilidad integrada con cuadernos de Jupyter, dispone de un entorno de primera línea para la creación de aplicaciones de Aprendizaje automático.  

### <a name="streaming-and-real-time-data-analysis"></a>Análisis de datos de streaming y en tiempo real
[Vea un tutorial](hdinsight-apache-spark-eventhub-streaming.md)

El análisis de datos en tiempo real se usa para escenarios que incluyen tanto la reducción del tiempo dedicado a la comprensión de los datos al procesarlos en cuanto llegan como la creación de una verdadera solución de streaming. Spark en HDInsight ofrece amplia compatibilidad para crear soluciones de análisis en tiempo real. Mientras que Spark ya posee conectores para insertar datos de varios orígenes como los sockets TCP, Flume, Twitter, ZeroMQ o Kafka, Spark en HDInsight agrega compatibilidad de primera clase para insertar datos desde Centros de eventos de Azure. Los Centros de eventos son el servicio de cola más usado en Azure. La disponibilidad inmediata de la compatibilidad con Centros de eventos convierte a Spark en HDInsight en una plataforma ideal para crear la canalización de análisis en tiempo real.

## <a name="next-steps"></a>¿Qué componentes se incluyen como parte de un clúster Spark?
Spark en HDInsight incluye los siguientes componentes que están disponibles en los clústeres de manera predeterminada.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Incluye Spark Core, Spark SQL, API Spark de streaming, GraphX y MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)

Spark en HDInsight también ofrece un [controlador ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para la conectividad con clústeres Spark en HDInsight desde herramientas de inteligencia empresarial como Microsoft Power BI y Tableau.

## <a name="where-do-i-start"></a>¿Por dónde empiezo?
Empiece por crear un clúster de Spark en HDInsight Linux. Vea [Inicio rápido: creación de un clúster Spark en HDInsight basado en Linux y ejecución de consultas interactivas mediante Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

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
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

