---
title: "Administración de recursos de un clúster Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Aprenda a usar recursos de administración para clústeres Spark en Azure HDInsight para mejorar el rendimiento."
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: e37abf9bbcf8f98c969a32d8eb37b9a7a6ddeb14
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Administración de recursos de un clúster Apache Spark en Azure HDInsight 

Aprenda a acceder a interfaces como la interfaz de usuario de Ambari, la de YARN y el servidor de historial de Spark asociadas al clúster de Spark, y a ajustar la configuración del clúster para optimizar el rendimiento.

**Requisitos previos:**

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Apertura de la interfaz de usuario web de Ambari

Apache Ambari se usa para supervisar el clúster y realizar cambios de configuración. Para más información, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui).

## <a name="open-the-spark-history-server"></a>Apertura del servidor de historial de Spark

Servidor de historial de Spark es la interfaz de usuario web para aplicaciones de Spark completadas y en ejecución. Es una extensión de la interfaz de usuario web de Sparkl.

**Para abrir la interfaz de usuario web del servidor de historial de Spark**

1. En [Azure Portal](https://portal.azure.com/), abra el clúster de Spark. Para obtener más información, consulte [Enumeración y visualización de clústeres](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. En **Vínculos rápidos**, haga clic en **Panel de clúster** y después en **Servidor de historial de Spark**.

    ![Servidor de historial de Spark](./media/apache-spark-resource-manager/launch-history-server.png "Servidor de historial de Spark")

    Cuando se le pida, escriba las credenciales de administrador del clúster Spark. También puede abrir el servidor de historial de Spark a través de la dirección URL siguiente:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Reemplace <ClusterName> por el nombre del clúster de Spark.

La interfaz de usuario web del servidor de historial de Spark tiene el aspecto siguiente:

![Servidor de historial de HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Apertura de la interfaz de usuario de YARN
Puede utilizar la interfaz de usuario de YARN para supervisar las aplicaciones que se encuentran en ejecución en el clúster Spark.

1. En [Azure Portal](https://portal.azure.com/), abra el clúster de Spark. Para obtener más información, consulte [Enumeración y visualización de clústeres](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. En **Vínculos rápidos**, haga clic en **Panel de clúster** y después en **YARN**.

    ![Iniciar interfaz de usuario de YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > También puede iniciar la interfaz de usuario de YARN desde la de Ambari. Para iniciar la interfaz de usuario de Ambari, haga clic en **Panel de clúster** y luego en **Panel de clúster de HDInsight**. En la interfaz de usuario de Ambari, haga clic en **YARN**, en **Vínculos rápidos**, en la instancia activa de Resource Manager y luego en la **interfaz de usuario de Resource Manager**.
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Optimice los clústeres para las aplicaciones Spark
Los tres parámetros clave que se pueden utilizar para la configuración de Spark según los requisitos de la aplicación son `spark.executor.instances`, `spark.executor.cores` y `spark.executor.memory`. Un ejecutor es un proceso que se inicia para una aplicación Spark. Se ejecuta en el nodo de trabajo y es responsable de realizar las tareas de la aplicación. El número predeterminado de ejecutores y el tamaño de estos para cada clúster se calcula en función del número de nodos de trabajo y el tamaño de estos. Esta información se almacena en `spark-defaults.conf` en los nodos principales del clúster.

Los tres parámetros de configuración se pueden configurar en el nivel de clúster (para todas las aplicaciones que se ejecutan en el clúster) o se pueden especificar también para cada aplicación individual.

### <a name="change-the-parameters-using-ambari-ui"></a>Cambio de los parámetros mediante la interfaz de usuario de Ambari
1. En la interfaz de usuario de Ambari, haga clic en **Spark** y en **Configs** (Configuraciones). A continuación, expanda **Custom spark-defaults** (spark-defaults personalizado).

    ![Establecer los parámetros mediante Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Los valores predeterminados son necesarios para hacer que se ejecuten cuatro aplicaciones Spark simultáneamente en el clúster. Puede cambiar estos valores desde la interfaz de usuario, como se muestra en la captura de pantalla siguiente:

    ![Establecer los parámetros mediante Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Para guardar los cambios de configuración, haga clic en **Save** (Guardar). En la parte superior de la página se le pide que reinicie todos los servicios afectados. Haga clic en **Restart**(Reiniciar).

    ![Reiniciar los servicios](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Cambio de los parámetros de una aplicación que se ejecuta en Jupyter Notebook
Para aplicaciones que se ejecutan en Jupyter Notebook, puede utilizar la instrucción mágica `%%configure` para realizar los cambios de configuración. Idealmente, debe realizar estos cambios al comienzo de la aplicación, antes de ejecutar la primera celda de código. Esto garantiza que la configuración se aplique a la sesión de Livy cuando se cree. Si desea cambiar la configuración en una fase posterior de la aplicación, debe utilizar el parámetro `-f` . Sin embargo, al hacerlo, se perderán todos los progresos en la aplicación.

El siguiente fragmento de código muestra cómo cambiar la configuración de una aplicación que se ejecuta en Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Los parámetros de configuración deben pasarse como una cadena JSON y deben estar en la siguiente línea después de la instrucción mágica, como se muestra en la columna de ejemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Cambio de los parámetros de una aplicación enviada mediante spark-submit
El siguiente comando es un ejemplo de cómo cambiar los parámetros de configuración de una aplicación de lote que se envía mediante `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Cambio de los parámetros de una aplicación enviada mediante cURL
El siguiente comando es un ejemplo de cómo cambiar los parámetros de configuración de una aplicación de lote que se envía mediante cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Cambiar estos parámetros en un servidor Thrift de Spark
El servidor Thrift de Spark proporciona acceso JDBC/ODBC a un clúster de Spark y se utiliza para enviar consultas de Spark SQL. Herramientas como Power BI, Tableau, etc. usan el protocolo ODBC para comunicarse con el servidor Thrift de Spark para ejecutar consultas de Spark SQL como una aplicación Spark. Cuando se crea un clúster de Spark, se inician dos instancias del servidor Thrift de Spark, una en cada nodo principal. Cada servidor Thrift de Spark está visible como una aplicación Spark en la interfaz de usuario de YARN.

El servidor Thrift de Spark utiliza la asignación dinámica de ejecutores de Spark y, por tanto, las `spark.executor.instances` no se utilizan. En su lugar, el servidor Thrift de Spark usa `spark.dynamicAllocation.minExecutors` y `spark.dynamicAllocation.maxExecutors` para especificar el recuento de ejecutores. Los parámetros de configuración `spark.executor.cores` y `spark.executor.memory` se usan para modificar el tamaño del ejecutor. Puede cambiar estos parámetros como se muestra en los siguientes pasos:

* Expanda la categoría **Advanced spark-thrift-sparkconf** (spark-thrift-sparkconf avanzado) para actualizar los parámetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` y `spark.executor.memory`.

    ![Configurar el servidor Thrift de Spark](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expanda la categoría **Custom spark-thrift-sparkconf** (spark-thrift-sparkconf personalizado) para actualizar el parámetro `spark.executor.cores`.

    ![Configurar el servidor Thrift de Spark](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Cambiar la memoria del controlador del servidor Thrift de Spark
La memoria del controlador del servidor Thrift de Spark se ha configurado al 25 % del tamaño de la RAM del nodo principal, suponiendo que el tamaño total de la RAM del nodo principal sea superior a 14 GB. Puede utilizar la interfaz de usuario de Ambari para cambiar la configuración de memoria del controlador, como se muestra en la captura de pantalla siguiente:

* En la interfaz de usuario de Ambari, haga clic en **Spark** y en **Configs** (Configuraciones). A continuación, expanda **Advanced spark-env** (spark-env avanzado) y especifique un valor para **spark_thrift_cmd_opts**.

    ![Configurar la RAM del servidor Thrift de Spark](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Reclamar recursos de clúster de Spark
Debido a la asignación dinámica de Spark, los únicos recursos consumidos por el servidor Thrift son los recursos de los dos maestros de aplicación. Para recuperar estos recursos debe detener los servicios del servidor Thrift que se estén ejecutando en el clúster.

1. En la interfaz de usuario de Ambari, en el panel izquierdo, haga clic en **Spark**.
2. En la siguiente página, haga clic en **Spark Thrift Servers**(Servidores Thrift de Spark).

    ![Reiniciar el servidor Thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Aparecerán los dos nodos principales en el que se ejecuta el servidor Thrift de Spark. Haga clic en uno de los nodos principales.

    ![Reiniciar el servidor Thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. La siguiente página muestra todos los servicios que se ejecutan en ese nodo principal. En la lista, haga clic en el botón desplegable situado junto al servidor Thrift de Spark y, a continuación, haga clic en **Stop**(Detener).

    ![Reiniciar el servidor Thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Repita también estos pasos en el otro nodo principal.

## <a name="restart-the-jupyter-service"></a>Reiniciar el servicio de Jupyter
Inicie la interfaz de usuario web de Ambari tal como se muestra al principio del artículo. En el panel de navegación izquierdo, haga clic en **Jupyter**, en **Service Actions** (Acciones de servicio) y en **Restart All** (Reiniciar todo). Esta acción iniciará el servicio de Jupyter en todos los nodos principales.

![Reinicio de Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "Reinicio de Jupyter")

## <a name="monitor-resources"></a>Supervisión de recursos
Inicie la interfaz de usuario web de Yarn tal como se muestra al principio del artículo. En la tabla de métricas del clúster, compruebe los valores de **Memory Used** (Memoria usada) y **Memory Total** (Memoria total). Si los dos valores son similares, puede que no haya recursos suficientes para iniciar la siguiente aplicación. Lo mismo se aplica a las columnas **VCores Used** (Núcleos virtuales usados) y **VCores Total** (Núcleos virtuales totales). Además, si en la vista principal hay una aplicación con el estado **ACCEPTED** (ACEPTADO) y no pasa al estado **RUNNING** (EN EJECUCIÓN) o **FAILED** (ERROR), podría ser una señal de que no hay suficientes recursos para comenzar.

![Límite de recursos](./media/apache-spark-resource-manager/resource-limit.png "Límite de recursos")

## <a name="kill-running-applications"></a>Terminar las aplicaciones en ejecución
1. En la interfaz de usuario de Yarn, en el panel izquierdo, haga clic en **Running** (En ejecución). En la lista de aplicaciones en ejecución, determine la aplicación que se eliminará y haga clic en el **ID** (Identificador).

    ![Eliminación de App1](./media/apache-spark-resource-manager/kill-app1.png "Eliminación de App1")

2. Haga clic en **Kill Application** (Eliminar aplicación) en la esquina superior derecha y luego en **OK** (Aceptar).

    ![Eliminación de App2](./media/apache-spark-resource-manager/kill-app2.png "Eliminación de App2")

## <a name="see-also"></a>Otras referencias
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de datos

* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análisis del registro del sitio web con Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análisis de datos de telemetría de Application Insights con Spark en HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Uso de Caffe en Azure HDInsight Spark para el aprendizaje profundo distribuido](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Para desarrolladores de Spark

* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Spark Streaming: Use Spark in HDInsight for building real-time streaming applications](apache-spark-eventhub-streaming.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)
