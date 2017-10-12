---
title: Microsoft Cognitive Toolkit con Azure HDInsight Spark para aprendizaje profundo | Microsoft Docs
description: "Aprenda cómo un modelo de aprendizaje profundo de Microsoft Cognitive Toolkit formado se puede aplicar a un conjunto de datos mediante la API de Spark Python en un clúster de Azure HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 5364a8fabe3ca5d0fdf9aabe296fef085587604e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Uso del modelo de aprendizaje profundo de Microsoft Cognitive Toolkit con un clúster de Azure HDInsight Spark

En este artículo, realice los pasos siguientes.

1. Ejecute un script personalizado para instalar Microsoft Cognitive Toolkit en un clúster de Azure HDInsight Spark.

2. Cargue un cuaderno de Jupyter en el clúster de Spark para ver cómo aplicar un modelo de aprendizaje profundo de Microsoft Cognitive Toolkit formado a los archivos de una cuenta de Azure Blob Storage mediante la [API de Spark Python (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte la página [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

* **Clúster de Azure HDInsight Spark**. Para este artículo, cree un clúster de Spark 2.0. Para obtener instrucciones, consulte [Creación de un clúster de Apache Spark en Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>¿Cómo funciona esta solución?

Esta solución se divide entre este artículo y un cuaderno de Jupyter que se carga como parte de este tutorial. En este artículo, realice los pasos siguientes:

* Ejecute una acción de script en un clúster de HDInsight Spark para instalar los paquetes de Microsoft Cognitive Toolkit y Python.
* Cargue el cuaderno de Jupyter que ejecuta la solución en el clúster de HDInsight Spark.

Los pasos restantes siguientes se tratan en el cuaderno de Jupyter.

- Carga de imágenes de ejemplo en un conjunto de datos distribuido resistente o RDD de Spark
   - Carga de los módulos y definición de los valores preestablecidos
   - Descarga del conjunto de datos de forma local en el clúster de Spark
   - Conversión del conjunto de datos en un RDD
- Puntuación de las imágenes mediante un modelo de Cognitive Toolkit formado
   - Descarga del modelo de Cognitive Toolkit formado en el clúster de Spark
   - Definición de las funciones que van a usar los nodos de trabajo
   - Puntuación de las imágenes en los nodos de trabajo
   - Evaluación de la precisión del modelo


## <a name="install-microsoft-cognitive-toolkit"></a>Instalación de Microsoft Cognitive Toolkit

Puede instalar Microsoft Cognitive Toolkit en un clúster de Spark mediante la acción de scripts. La acción de scripts usa scripts personalizados para instalar los componentes en el clúster que no están disponibles de forma predeterminada. Puede utilizar el script personalizado desde Azure Portal, mediante el uso del SDK de .NET para HDInsight o Azure PowerShell. También puede utilizar el script para instalar el kit de herramientas como parte de la creación del clúster, o una vez que el clúster está en funcionamiento. 

En este artículo se utiliza el portal para instalar el kit de herramientas una vez creado el clúster. Para conocer otras formas de ejecutar el script personalizado, consulte [Personalizar los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Uso del portal de Azure

Para obtener instrucciones sobre cómo usar Azure Portal con el fin de ejecutar acciones de scripts, consulte [Personalizar los clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Asegúrese de que proporciona las siguientes entradas para instalar Microsoft Cognitive Toolkit.

* Proporcione un valor para el nombre de la acción de script.

* Para el **URI de script de Bash**, escriba `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Asegúrese de que ejecuta el script solo en el nodo principal y en el de trabajo y desactive el resto de las casillas.

* Haga clic en **Crear**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Carga del cuaderno de Jupyter en el clúster de Azure HDInsight Spark

Para usar Microsoft Cognitive Toolkit con el clúster de Azure HDInsight Spark, debe cargar el cuaderno de Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** en el clúster de Azure HDInsight Spark. Este cuaderno está disponible en GitHub en [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Clone el repositorio de GitHub en [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Para obtener las instrucciones de clonación, consulte [Cloning a repository](https://help.github.com/articles/cloning-a-repository/) (Clonación de un repositorio).

2. Desde Azure Portal, abra la hoja del clúster de Spark que ya ha aprovisionado, haga clic en **Panel de clúster**y, a continuación, haga clic en **Jupyter Notebook**.

    También puede iniciar el cuaderno de Jupyter. Para ello, vaya a la dirección URL `https://<clustername>.azurehdinsight.net/jupyter/`. Reemplace \<clustername> por el nombre del clúster de HDInsight.

3. En el cuaderno de Jupyter, haga clic en **Cargar** en la esquina superior derecha y, a continuación, navegue hasta la ubicación donde clonó el repositorio de GitHub.

    ![Cargar el cuaderno de Jupyter en el clúster de Azure HDInsight Spark](./media/hdinsight-apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Cargar el cuaderno de Jupyter en el clúster de Azure HDInsight Spark")

4. Haga clic en **Cargar** de nuevo.

5. Una vez cargado el cuaderno, haga clic en el nombre de este y, a continuación, siga las instrucciones del propio cuaderno sobre cómo cargar el conjunto de datos y realizar el tutorial.

## <a name="see-also"></a>Consulte también
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Análisis de datos de telemetría de Application Insights con Spark en HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
