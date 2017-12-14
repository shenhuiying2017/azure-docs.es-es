---
title: "Kit de herramientas de Azure para IntelliJ: creación de aplicaciones Spark para un clúster de HDInsight | Microsoft Docs"
description: "Uso del kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2017
ms.author: maxluk,jejiang
ms.openlocfilehash: 77c7163b896c2b364039ea6c669ee70cf8be4d9e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Uso del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark para un clúster de HDInsight

Uso del complemento de kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a continuación a un clúster de HDInsight Spark directamente desde el entorno de desarrollo integrado (IDE) de IntelliJ. Puede usar el complemento de varias maneras:

* Desarrollar y enviar una aplicación Spark en Scala en un clúster de Spark en HDInsight.
* Tener acceso a los recursos del clúster de Azure HDInsight Spark.
* Desarrollar y ejecutar localmente una aplicación Spark en Scala.

Para crear el proyecto, vea el vídeo [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creación de aplicaciones Spark con el kit de herramientas de Azure para IntelliJ).

> [!IMPORTANT]
> Puede usar este complemento para crear y enviar aplicaciones solo para un clúster de HDInsight Spark en Linux.
> 

## <a name="prerequisites"></a>Requisitos previos

- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
- Kit de desarrollo de Oracle Java. Puede instalarlo desde el [sitio web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- IntelliJ IDEA. En este artículo se usa la versión 2017.1. Puede instalarlo desde el [sitio web de JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalación del kit de herramientas de Azure para IntelliJ
Para obtener instrucciones de instalación, consulte [Instalación del kit de herramientas de Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure

1. Inicie el IDE de IntelliJ y abra Azure Explorer. En el menú **View** (Ver), seleccione **Tools Windows** (Ventanas de herramientas) y luego seleccione **Azure Explorer** (Explorador de Azure).
       
   ![Vínculo de Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Haga clic con el botón derecho en el nodo **Azure** y después seleccione **Iniciar sesión**.

3. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, seleccione **Iniciar sesión** y, a continuación, escriba las credenciales de Azure.

    ![Cuadro de diálogo de inicio de sesión en Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Seleccione el botón **Seleccionar**.

    ![Cuadro de diálogo Seleccionar suscripciones](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. En la pestaña **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight Spark de su suscripción.
   
    ![Clústeres de HDInsight Spark en Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados al clúster, puede expandir un nodo de nombre de clúster.
   
    ![Nodo de nombre de clúster expandido](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark

1. Inicie IntelliJ IDEA y, después, cree un proyecto. En el cuadro de diálogo **Nuevo proyecto** , haga lo siguiente: 

   a. Seleccione **HDInsight** > **Spark en HDInsight (Scala)**.

   b. En la lista de **herramientas de compilación**, seleccione cualquiera de las siguientes, según sus necesidades:

      * **Maven**, para la compatibilidad con el asistente para crear un proyecto de Scala
      * **SBT**, para administrar las dependencias y compilar el proyecto de Scala

    ![Cuadro de diálogo Nuevo proyecto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. Seleccione **Siguiente**.

3. El asistente para crear un proyecto de Scala detecta automáticamente si se ha instalado el complemento de Scala. Seleccione **Instalar**.

   ![Comprobación de complemento de Scala](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Para descargar el complemento de Scala, seleccione **Aceptar**. Siga las instrucciones para reiniciar IntelliJ. 

   ![Cuadro de diálogo de instalación del complemento de Scala](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. En la ventana **Nuevo proyecto**, haga lo siguiente:  

    ![Selección del SDK de Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. Escriba un nombre y una ubicación de proyecto.

   b. En la lista desplegable **SDK de proyecto**, seleccione **Java 1.8** para el clúster Spark 2.x, o bien **Java 1.7** para el clúster Spark 1.x.

   c. En la lista desplegable **Versión de Spark**, el asistente para crear un proyecto de Scala integra la versión correcta del SDK de Spark y el SDK de Scala. Si la versión del clúster de Spark es inferior a la 2.0, seleccione **Spark 1.x**. De lo contrario, seleccione **Spark 2.x**. Este ejemplo utiliza **Spark 2.0.2 (Scala 2.11.8)**.

6. Seleccione **Finalizar**.

7. El proyecto Spark creará automáticamente un artefacto. Para ver el artefacto, haga lo siguiente:

   a. En el menú **File** (Archivo), seleccione **Project Structure** (Estructura del proyecto).

   b. En el cuadro de diálogo **Project Structure** (Estructura del proyecto), seleccione **Artifacts** (Artefactos) para ver el artefacto predeterminado que se crea. También puede crear su propio artefacto seleccionando el signo más (**+**).

      ![Información de artefacto en el cuadro de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. Agregue el código fuente de aplicación de la siguiente forma:

   a. En el Explorador de proyectos, haga clic con el botón derecho en **src**, elija **New** (Nuevo) y, a continuación, seleccione **Scala Class** (Clase de Scala).
      
      ![Comandos para crear una clase Scala desde el explorador de proyectos](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, seleccione **Object** (Objeto) en **Kind** (Variante) y seleccione **OK** (Aceptar).
      
      ![Creación de un cuadro de diálogo de nueva clase de Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. En el archivo **MyClusterApp.scala** , pegue el siguiente código. El código lee los datos de HVAC.csv (disponible en todos los clústeres de HDInsight Spark), recupera las filas que solo tienen un dígito en la séptima columna del archivo CSV y escribe la salida en **/HVACOut** en el contenedor de almacenamiento predeterminado para el clúster.

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. Ejecute la aplicación en un clúster de HDInsight Spark de la siguiente forma:

   a. En el explorador de proyectos, haga clic con el botón derecho en el nombre del proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).
      
      ![Envío de aplicación Spark a comando HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Se le pedirá que escriba las credenciales de la suscripción de Azure. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores y luego seleccione **Submit** (Enviar).
      
      * Para **Spark clusters (Linux only)**(Clústeres de Spark [solo Linux]), seleccione el clúster de Spark de HDInsight en el que desea ejecutar la aplicación.

      * Seleccione un artefacto del proyecto IntelliJ o uno del disco duro.

      * En el cuadro de texto **Main class name** (Nombre de clase principal), seleccione los puntos suspensivos (**ellipsis**), seleccione la clase principal del código fuente de aplicación y seleccione **OK** (Aceptar).

        ![Cuadro de diálogo de selección de clase principal](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * Dado que el código de aplicación de este ejemplo no requiere argumentos de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto. Después de proporcionar toda la información, el cuadro de diálogo debería parecerse a la imagen siguiente.
        
        ![Cuadro de diálogo de envío de Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. La pestaña **Spark Submission** (Envío de Spark) de la parte inferior de la ventana debe empezar a mostrar el progreso. También puede detener la aplicación seleccionando el botón rojo en la ventana **Spark Submission** (Envío de Spark).
      
      ![Ventana de envío de Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Para obtener información sobre cómo tener acceso a la salida de trabajo, consulte la sección "Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ" que encontrará más adelante en este artículo.

## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicaciones Spark de forma local o remota en un clúster de HDInsight 
También se recomienda otra manera de enviar la aplicación Spark al clúster. También puede establecer los parámetros del IDE de **configuraciones de ejecución o depuración**. Para obtener más información, consulte [Depuración de aplicaciones Spark en un clúster de HDInsight con el Kit de herramientas de Azure para IntelliJ mediante SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ
Puede realizar varias operaciones mediante el Kit de herramientas de Azure para IntelliJ.

### <a name="access-the-job-view"></a>Acceso a la vista de trabajo
1. En Azure Explorer, expanda **HDInsight** y el nombre del clúster de Spark. Después, seleccione **Trabajos**.  

    ![Nodo Vista de trabajos](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Seleccione el nombre de la aplicación para la que desea ver más detalles.

    ![Detalles de aplicación](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Para mostrar información básica del trabajo de ejecución, mantenga el mouse sobre el gráfico del trabajo. Para ver el gráfico de fases y la información que todo trabajo genera, seleccione un nodo del gráfico del trabajo.

    ![Detalles de fase de trabajo](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para ver registros usados con frecuencia, como *Driver Stderr*, *Driver Stdout* y *Directory Info*, seleccione la pestaña **Registro**.

    ![Detalles del registro](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. También puede ver Spark History UI (IU de historial de Spark) y YARN UI (IU de YARN), en el nivel de aplicación, al seleccionar los vínculos correspondientes de la parte superior de la ventana.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark
1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). 

2. Cuando se le solicite, escriba las credenciales de administrador del clúster, que especificó al configurar el clúster.

3. En el panel del servidor de historial de Spark, puede usar el nombre de aplicación para buscar la aplicación que acaba de terminar de ejecutar. En el código anterior, se establece el nombre de la aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark es **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Inicio del portal de Ambari
1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir el portal de administración de clústeres [Ambari]). 

2. Cuando se le pida, escriba las credenciales de administrador para el clúster. Estas son las credenciales que especificó durante el proceso de configuración de clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure
De forma predeterminada, el kit de herramientas de Azure para IntelliJ enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones a las que desea tener acceso. 

1. En Azure Explorer, haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Manage Subscriptions** (Administrar suscripciones). 

2. En el cuadro de diálogo, desactive las casillas situadas junto a las suscripciones a las que no desea tener acceso y seleccione **Close** (Cerrar). También puede seleccionar **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Conversión de las aplicaciones IntelliJ IDEA existentes para usar el kit de herramientas de Azure para IntelliJ
Puede convertir las aplicaciones Spark en Scala existentes creadas en IntelliJ IDEA para que sean compatibles con el kit de herramientas de Azure para IntelliJ. Esto le permitirá utilizar el complemento para enviar las aplicaciones a un clúster de HDInsight Spark.

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo .iml asociado.

2. En el nivel raíz, hay un elemento de **module** similar al siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite el elemento para agregar `UniqueKey="HDInsightTool"` de forma que el elemento de **module** sea similar a lo siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Guarde los cambios. La aplicación ahora debe ser compatible con el kit de herramientas de Azure para IntelliJ. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en el explorador de proyectos. El menú emergente ahora debería tener la opción **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

## <a name="troubleshooting"></a>Solución de problemas

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>Error en la ejecución local: *Please use a larger heap size* (Utilice un tamaño de montón mayor)
En Spark 1.6, si utiliza un SDK de Java de 32 bits durante la ejecución local, puede encontrar los siguientes errores:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Estos errores se producen porque el tamaño del montón no es lo suficientemente grande como para que se ejecute Spark. Spark requiere al menos 471 MB. (para obtener más información, consulte [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)). Una solución sencilla es utilizar un SDK de Java de 64 bits. También puede cambiar la configuración de JVM en IntelliJ agregando las siguientes opciones:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Incorporación de opciones a la casilla de opciones de máquina virtual en IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>P+F
Para enviar una aplicación a Azure Data Lake Store, elija el modo **Interactivo** durante el proceso de inicio de sesión en Azure. Si selecciona el modo **Automatizado**, puede aparecer un error.

![inicio de sesión interactivo](./media/apache-spark-intellij-tool-plugin/interative-signin.png)

Ahora se ha resuelto. Puede elegir un clúster de Azure Data Lake para enviar la aplicación con cualquier método de inicio de sesión.

## <a name="feedback-and-known-issues"></a>Comentarios y problemas conocidos
Actualmente, la visualización de salidas de Spark directamente no se admite.

Si tiene sugerencias o comentarios, o si se le presenta algún problema al usar este complemento, no dude en enviarnos un correo electrónico a la dirección hdivstool@microsoft.com.

## <a name="seealso"></a>Pasos siguientes
* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="demo"></a>Demostración
* Creación del proyecto Scala (vídeo): [Creación de aplicaciones Scala de Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (en inglés)
* Depuración remota (vídeo): [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark en clústeres de HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (en inglés)

### <a name="scenarios"></a>Escenarios
* [Spark con BI: realización del análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para analizar la temperatura de edificios con los datos del sistema de acondicionamiento de aire](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

