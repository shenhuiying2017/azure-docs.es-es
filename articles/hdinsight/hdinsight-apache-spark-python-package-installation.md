---
title: "Acción de script: instalación de paquetes de Python con cuadernos de Jupyter en Azure HDInsight | Microsoft Docs"
description: "Instrucciones detalladas sobre cómo configurar cuadernos de Jupyter disponibles con clústeres de HDInsight Spark para usar paquetes externos de Python."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9019a4115e81a7d8f1960098b1138cd437a0460b
ms.openlocfilehash: b0d6e509c5bacd828e9a9938edb860bbf0c0a8f3
ms.lasthandoff: 01/24/2017


---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Uso de acción de script para instalar paquetes externos de Python para cuadernos de Jupyter en clústeres de Apache Spark en HDInsight
> [!div class="op_single_selector"]
> * [Uso de magic cell](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso de acciones de script](hdinsight-apache-spark-python-package-installation.md)
>
>

Aprenda a usar acciones de script para configurar un clúster de Apache Spark en HDInsight (Linux) para el uso de paquetes de **Python** externos aportados por la comunidad que no están incluidos de serie en el clúster.

> [!NOTE]
> También puede configurar un cuaderno de Jupyter mediante `%%configure` para usar paquetes externos. Para obtener instrucciones, consulte [Uso de paquetes externos con cuadernos de Jupyter en clústeres de Apache Spark en HDInsight Linux](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Puede buscar en el [índice de paquetes](https://pypi.python.org/pypi) la lista completa de paquetes que están disponibles. También puede obtener una lista de paquetes disponibles de otras fuentes. Por ejemplo, puede instalar paquetes que están disponibles a través de [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) o [conda-forge](https://conda-forge.github.io/feedstocks.html).

En este artículo, aprenderá a instalar el paquete [TensorFlow](https://www.tensorflow.org/) mediante una acción de script en su clúster y a usarlo mediante el cuaderno de Jupyter.

## <a name="prerequisites"></a>Requisitos previos
Debe tener lo siguiente:

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Si aún no tiene un clúster de Spark en HDInsight Linux, puede ejecutar acciones de script durante la creación del clúster. Consulte la documentación sobre [cómo usar acciones de script personalizadas](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Uso de paquetes externos con cuadernos de Jupyter Notebook

1. Desde el [Portal de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster de Spark, haga clic en **Acciones de script** en **Uso**. Ejecute la acción personalizada que instala TensorFlow en los nodos principales y los nodos de trabajo. Se puede hacer referencia al script de Bash desde: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh Consulte la documentación sobre [cómo usar acciones de script personalizadas](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Hay dos instalaciones de Python en el clúster. Spark usará la instalación de Python de Anaconda ubicada en `/usr/bin/anaconda/bin`. Haga referencia a esa instalación en sus acciones personalizadas mediante `/usr/bin/anaconda/bin/pip` y `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Abrir un cuaderno de Jupyter de PySpark

    ![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-python-package-installation/hdispark.note.jupyter.createpysparknotebook.png "Crear un nuevo cuaderno de Jupyter")

4. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

5. Ahora realizará la acción `import tensorflow` y ejecutará un ejemplo de saludo (Hola mundo). 

    Código para copiar:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    El resultado tendrá este aspecto:
    
    ![Ejecución de código de TensorFlow](./media/hdinsight-apache-spark-python-package-installation/execution.png "Ejecución de código de TensorFlow")



## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

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
* [Uso de paquetes externos con cuadernos de Jupyter en clústeres de Apache Spark en HDInsight](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

