---
title: Uso de paquetes personalizados de Maven con cuadernos de Jupyter en Spark en Azure | Microsoft Docs
description: "Dispone de instrucciones detalladas sobre cómo configurar los cuadernos de Jupyter Notebook con los clústeres de HDInsight Spark para utilizar paquetes de Spark externos."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 2dd0d456d0c6b1c83a409fead63dacff26c03198
ms.lasthandoff: 01/24/2017


---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Uso de paquetes externos con cuadernos de Jupyter en clústeres de Apache Spark en HDInsight
> [!div class="op_single_selector"]
> * [Uso de magic cell](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso de acciones de script](hdinsight-apache-spark-python-package-installation.md)
>
>

Aprenda a configurar un cuaderno de Jupyter en clústeres de Apache Spark en HDInsight para usar paquetes **maven** externos aportados por la comunidad que no se incluyan listos para utilizarse en el clúster. 

Puede buscar el [repositorio de Maven](http://search.maven.org/) para obtener una lista completa de los paquetes que están disponibles. También puede obtener una lista de paquetes disponibles de otras fuentes. Por ejemplo, dispone de la lista completa de los paquetes externos aportados por la comunidad en [Spark Packages](http://spark-packages.org/)(Paquetes Spark).

En este artículo, aprenderá a utilizar el paquete [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con el cuaderno de Jupyter Notebook.



## <a name="prerequisites"></a>Requisitos previos
Debe tener lo siguiente:

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Uso de paquetes externos con cuadernos de Jupyter Notebook
1. Desde el [Portal de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   
2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

    > [!NOTE]
    > También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

   

3. Cree un nuevo notebook. Haga clic en **Nuevo** y luego en **Spark**.
   
    ![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Crear un nuevo cuaderno de Jupyter")

4. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.
   
    ![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Proporcionar un nombre para el cuaderno")

5. Utilizará la instrucción mágica `%%configure` para configurar el cuaderno para usar un paquete externo. En los cuadernos que utilizan paquetes externos, asegúrese de invocar la instrucción mágica `%%configure` en la primera celda de código. Esto garantiza que el kernel se configure para utilizar el paquete antes de iniciar la sesión.

    >[!IMPORTANT] 
    >Si se olvida de configurar el kernel en la primera celda, puede utilizar el parámetro `%%configure` con el parámetro `-f`, pero ello reiniciará la sesión y se perderá todo el trabajo.

    | Versión de HDInsight | Comando |
    |-------------------|---------|
    |Para HDInsight 3.3 y HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Para HDInsight 3.5 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. En el fragmento de código anterior, se esperan las coordenadas de Maven correspondientes al paquete externo de Maven Central Repository. En este fragmento de código, `com.databricks:spark-csv_2.10:1.4.0` es la coordenada de Maven para el paquete **spark-csv** . Le mostramos cómo crear las coordenadas de un paquete.
   
    a. Busque el paquete en el repositorio de Maven. En este tutorial, utilizamos [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. En el repositorio, recopile los valores de **GroupId**, **ArtifactId** y **Version**.
   
    ![Uso de paquetes externos con cuadernos de Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Uso de paquetes externos con cuadernos de Jupyter")
   
    c. Concatene los tres valores separados por dos puntos (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Ejecute la celda de código con la instrucción mágica `%%configure` . De esta forma, se configurará la sesión de Livy subyacente para utilizar el paquete que facilitó. En las siguientes celdas del cuaderno, ya podrá usar el paquete como se muestra a continuación.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. A continuación, podrá ejecutar los fragmentos de código como se muestra seguidamente para ver los datos de la trama de datos que creó en el paso anterior.
   
        df.show()
   
        df.select("Time").count()

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

* [Uso de paquetes externos de Python con cuadernos de Jupyter Notebook en clústeres de Apache Spark en HDInsight Linux](hdinsight-apache-spark-python-package-installation.md)
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)


