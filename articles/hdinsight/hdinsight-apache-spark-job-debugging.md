---
title: "Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark de HDInsight | Microsoft Docs"
description: "Usar la interfaz de usuario de YARN, la de Spark y el servidor de historial de Spark para realizar un seguimiento y depurar trabajos que se ejecutan en un clúster de Spark en HDInsight de Azure"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 849e883a707cd84564a5cfae76f31494057ab181


---
# <a name="track-and-debug-jobs-running-on-apache-spark-cluster-in-hdinsight-linux"></a>Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight Linux
En este artículo aprenderá a realizar un seguimiento y a depurar los trabajos de Spark mediante la interfaz de usuario de YARN, la de Spark y el servidor de historial de Spark. En este artículo, se iniciará un trabajo de Spark mediante un cuaderno disponible con el clúster de Spark, **Aprendizaje automático: análisis predictivo en datos de inspección de alimentos con MLLib**. Puede utilizar los pasos siguientes para realizar el seguimiento de una aplicación que haya enviado mediante cualquier otro enfoque, por ejemplo, **spark-submit**.

## <a name="prerequisites"></a>Requisitos previos
Debe tener lo siguiente:

* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Debería haber iniciado la ejecución del cuaderno, **[Aprendizaje automático: análisis predictivo en datos de inspección de alimentos con MLLib](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**. Para obtener instrucciones sobre cómo ejecutar este cuaderno, siga el vínculo.  

## <a name="track-an-application-in-the-yarn-ui"></a>Seguimiento de una aplicación en la interfaz de usuario de YARN
1. Inicie la interfaz de usuario de YARN. En la hoja del clúster, haga clic en **Panel de clúster** y en **YARN**.
   
    ![Iniciar interfaz de usuario de YARN](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > También puede iniciar la interfaz de usuario de YARN desde la de Ambari. Para iniciar la interfaz de usuario de Ambari, en la hoja del clúster, haga clic en **Panel de clúster** y luego en **Panel de clúster de HDInsight**. En la interfaz de usuario de Ambari, haga clic en **YARN**, en **Vínculos rápidos**, en la instancia activa de Resource Manager y, finalmente, en la **interfaz de usuario de Resource Manager**.    
   > 
   > 
2. Dado que se inició el trabajo de Spark mediante cuadernos de Jupyter Notebook, la aplicación se llama **remotesparkmagics** (este es el nombre de todas las aplicaciones que se inician desde los cuadernos). Haga clic en el identificador de la aplicación en lugar de en el nombre de la aplicación para más información sobre el trabajo. Esto inicia la vista de la aplicación.
   
    ![Buscar identificador de aplicación Spark](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)
   
    Para estas aplicaciones que se inician desde los cuadernos de Jupyter Notebook, el estado es siempre **RUNNING** (EN EJECUCIÓN) hasta que sale del cuaderno.
3. En la vista de la aplicación, puede explorar más en profundidad para averiguar los contenedores asociados con la aplicación y los registros (stdout y stderr). También puede iniciar la interfaz de usuario de Spark haciendo clic en el vínculo correspondiente a la **Dirección URL de seguimiento**, tal como se muestra a continuación. 
   
    ![Descargar registros de contenedor](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Seguimiento de una aplicación en la interfaz de usuario de Spark
En la interfaz de usuario de Spark, puede explorar en profundidad los trabajos de Spark generados por la aplicación que ha iniciado anteriormente.

1. Para iniciar la interfaz de usuario de Spark, desde la vista de la aplicación, haga clic en el vínculo en la **Dirección URL de seguimiento**, como se muestra en la captura de pantalla anterior. Puede ver todos los trabajos de Spark iniciados por la aplicación que se ejecuta en el cuaderno de Jupyter Notebook.
   
    ![Ver trabajos de Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)
2. Haga clic en la pestaña **Executors** (Ejecutores) para ver la información de procesamiento y almacenamiento de cada elemento de ejecución. También puede recuperar la pila de llamadas haciendo clic en el vínculo **Thread Dump** (Volcado de subprocesos).
   
    ![Ver ejecutores de Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
3. Haga clic en la pestaña **Stages** (Fases) para ver las fases asociadas a la aplicación.
   
    ![Ver fases de Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)
   
    Cada fase puede tener varias tareas de las que puede ver las estadísticas de ejecución, como se muestra a continuación.
   
    ![Ver fases de Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png) 
4. En la página de detalles de la fase, puede iniciar la visualización de DAG. Expanda el vínculo **DAG Visualization** (Visualización de DAG) en la parte superior de la página, como se muestra a continuación.
   
    ![Ver visualización DAG de las fases de Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    El DAG o grafo acíclico dirigido representa las distintas fases de la aplicación. Cada cuadro azul en el grafo representa una operación de Spark que se invoca desde la aplicación.
5. En la página de detalles de la fase, también puede iniciar la vista de escala de tiempo de la aplicación. Expanda el vínculo **Event Timeline** (Escala de tiempo del evento) en la parte superior de la página, como se muestra a continuación.
   
    ![Ver escala de tiempo de eventos de fases de Spark](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Esto permite mostrar los eventos de Spark en forma de una escala de tiempo. La vista de la escala de tiempo está disponible en tres niveles: entre trabajos, dentro de un trabajo y dentro de una fase. La imagen anterior captura la vista de la escala de tiempo para una fase determinada.
   
   > [!TIP]
   > Si selecciona la casilla **Enable zooming** (Habilitar zoom), puede desplazarse a izquierda y derecha en la vista de escala de tiempo.
   > 
   > 
6. Otras pestañas de la interfaz de usuario de Spark proporcionan también información útil acerca de la instancia de Spark.
   
   * Pestaña Storage (Almacenamiento): si la aplicación crea varios RDD, podrá encontrar información sobre estos en esta pestaña.
   * Pestaña Environment (Entorno): esta pestaña proporciona mucha información útil sobre la instancia de Spark como, por ejemplo: 
     * La versión de la escala
     * El directorio de registro de eventos asociado con el clúster
     * El número de núcleos del ejecutor de la aplicación
     * Etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Búsqueda de información acerca de los trabajos completados con el servidor de historial de Spark
Cuando se completa un trabajo, se conserva la información sobre este en el servidor de historial de Spark.

1. Para iniciar el servidor de historial de Spark, en la hoja del clúster, haga clic en **Panel de clúster** y en **Servidor de historial de Spark**.
   
    ![Iniciar servidor de historial de Spark](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > También puede iniciar la interfaz de usuario del servidor de historial de Spark desde la de Ambari. Para iniciar la interfaz de usuario de Ambari, en la hoja del clúster, haga clic en **Panel de clúster** y luego en **Panel de clúster de HDInsight**. En la interfaz de usuario de Ambari, haga clic en **Spark**, en **Vínculos rápidos** y en **Spark History Server UI** (IU del servidor de historial de Spark).
   > 
   > 
2. Aparecerá una lista de todas las aplicaciones completadas. Haga clic en un identificador de aplicación para explorar en profundidad una aplicación para obtener más información.
   
    ![Iniciar servidor de historial de Spark](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)




<!--HONumber=Nov16_HO3-->


