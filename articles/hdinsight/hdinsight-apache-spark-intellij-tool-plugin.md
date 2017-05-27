---
title: "Kit de herramientas de Azure para IntelliJ: creación de aplicaciones Scala para HDInsight Spark | Microsoft Docs"
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
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e6e005e07b35bbaffebf1efa216feebafc99df8b
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>Uso del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark para el clúster de HDInsight

Uso del complemento de kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark directamente desde el IDE de IntelliJ. Puede usar el complemento de varias maneras diferentes:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

También puede seguir un [vídeo](https://mix.office.com/watch/1nqkqjt5xonza) para poder empezar a usarlo.

> [!IMPORTANT]
> Este complemento se puede utilizar para crear y enviar aplicaciones solo para un clúster de HDInsight Spark en Linux.
> 
> 

## <a name="prerequisites"></a>Requisitos previos

* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

* Kit de desarrollo de Oracle Java. Puede instalarlo desde el [sitio web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* IntelliJ IDEA. En este artículo se usa la versión 15.0.1. Puede instalarlo desde el [sitio web de JetBrains](https://www.jetbrains.com/idea/download/).

## <a name="install-azure-toolkit-for-intellij"></a>Instalación del kit de herramientas de Azure para IntelliJ
Para más información, consulte [Instalación del kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure
1. Inicie el IDE de IntelliJ y abra Azure Explorer. En el menú **View** (Ver), haga clic en **Tools Windows** (Ventanas de herramientas) y luego haga clic en **Azure Explorer** (Explorador de Azure).
   
    ![Comandos seleccionados en el menú Ver](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. Haga clic con el botón derecho en el nodo **Azure** y después haga clic en **Iniciar sesión**.
3. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, haga clic en **Iniciar sesión** y escriba las credenciales de Azure.
   
    ![Cuadro de diálogo de inicio de sesión en Azure](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Haga clic en **Seleccionar** para cerrar el cuadro.

    ![Cuadro de diálogo de selección de suscripciones](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)
5. En la pestaña **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight Spark de su suscripción.
   
    ![Clústeres de HDInsight Spark en Azure Explorer](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. Puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados al clúster.
   
    ![Expansión de un nombre de clúster para ver recursos](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark
1. Inicie IntelliJ IDEA y cree un proyecto. En el cuadro de diálogo **New project** (Nuevo proyecto), realice las siguientes selecciones y después haga clic en **Next**(Siguiente).
   
   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight (Scala)**(Spark en HDInsight [Scala]).
   
    ![Cuadro de diálogo Nuevo proyecto](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
2. En la ventana siguiente, proporcione los detalles de proyecto siguientes y, a continuación, haga clic en **Finish** (Finalizar).
   
   * Proporcione un nombre de proyecto y la ubicación del proyecto.
   * Para **Project SDK** (SDK de proyecto), use Java 1.8 para un clúster de Spark 1.6 o 2.0.
   * En **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear) y en **Download** (Descargar). A continuación, seleccione la versión de Scala que desea usar.
     * Elija **JDK 1.8 y Scala 2.11.x** si está dispuesto a enviar el trabajo a un clúster de Spark 2.0.
     * Elija **JDK 1.8 (nivel de idioma 7) y Scala 2.10.x** si está dispuesto a enviar el trabajo a un clúster de Spark 1.6.

        ![Selección de la versión de Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * Para **Spark SDK**, descargue y utilice el SDK de [GitHub] (http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Tenga en cuenta que spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar es para un clúster de Spark 2.0 y spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar es para un clúster de Spark 1.6. 

     Si lo desea, también puede usar el [repositorio de Spark Maven](http://mvnrepository.com/search?q=spark), pero asegúrese de que tiene instalado el repositorio de Maven correcto para poder desarrollar aplicaciones Spark. Por ejemplo, si usa Spark Streaming, compruebe que tiene la parte correspondiente a Spark Streaming instalada. También asegúrese de que está usando el repositorio marcado como Scala 2.10 para un clúster de Spark 1.6 y el marcado como Scala 2.11 para un clúster de Spark 2.0.
     
       ![Selección del SDK de Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
3. El proyecto Spark creará automáticamente un artefacto. Para ver el artefacto, siga estos pasos:
   
   1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
   2. En el cuadro de diálogo **Project Structure** (Estructura del proyecto), haga clic en **Artifacts** (Artefactos) para ver el artefacto predeterminado que se crea. También puede crear su propio artefacto haciendo clic en el icono **+**.
      
       ![Información de artefacto en el cuadro de diálogo](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
4. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Project** (Proyecto). Si **Project SDK** (SDK de proyecto) está establecido en 1.8, asegúrese de que **Project language level** (Nivel de lenguaje del proyecto) esté establecido en **7 - Diamonds, ARM, multi-catch, etc**. (Es opcional para un clúster de Spark 2.0).
   
    ![Establecimiento del nivel de lenguaje del proyecto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. Agregue el código fuente de aplicación.
   
   1. En el explorador de proyectos, haga clic con el botón derecho en **src**, elija **New** (Nuevo) y haga clic en **Scala Class** (Clase de Scala).
      
       ![Comandos para crear una clase Scala desde el explorador de proyectos](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, seleccione **Object** (Objeto) en **Kind** (Variante) y haga clic en **OK** (Aceptar).
      
       ![Creación de un cuadro de diálogo de nueva clase de Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. En el archivo **MyClusterApp.scala** , pegue el siguiente código. Este código lee los datos de HVAC.csv (disponible en todos los clústeres HDInsight Spark), recupera las filas que solo tienen un dígito en la séptima columna del archivo CSV y escribe la salida en **/HVACOut** en el contenedor de almacenamiento predeterminado para el clúster.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows that have only one digit in the seventh column in the CSV file
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

6. Ejecute la aplicación en un clúster de HDInsight Spark.
   
   1. En el explorador de proyectos, haga clic con el botón derecho en el nombre del proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).
      
       ![Selección del envío de aplicación Spark a HDInsight](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Se le pedirá que escriba las credenciales de la suscripción de Azure. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores y luego haga clic en **Submit** (Enviar).
      
      * Para **Spark clusters (Linux only)**(Clústeres de Spark [solo Linux]), seleccione el clúster de Spark de HDInsight en el que desea ejecutar la aplicación.
      * Seleccione un artefacto del proyecto IntelliJ o uno del disco duro.
      * En el cuadro de texto **Main class name** (Nombre de clase principal), haga clic en los puntos suspensivos (![ellipsis](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), seleccione la clase principal del código fuente de la aplicación y haga clic en **OK** (Aceptar).
        
          ![Cuadro de diálogo de selección de clase principal](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.
        Después de proporcionar todas las entradas, el cuadro de diálogo debería parecerse a la imagen siguiente.
        
          ![Cuadro de diálogo de envío de Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
   3. La pestaña **Spark Submission** (Envío de Spark) de la parte inferior de la ventana debe empezar a mostrar el progreso. También puede detener la aplicación haciendo clic en el botón rojo en la ventana **Spark Submission** (Envío de Spark).
      
       ![Ventana de envío de Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      En la sección "Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ" que encontrará más adelante obtendrá podrá ver cómo acceder a la salida de trabajo.

## <a name="choose-azure-data-lake-store-as-spark-scala-application-storage"></a>Selección de Azure Data Lake Store como almacenamiento para la aplicación Spark en Scala
Si desea enviar una aplicación a Azure Data Lake Store, tiene que elegir el modo **Interactivo** durante el proceso de inicio de sesión en Azure. 

   ![Opción de modo interactivo en el inicio de sesión](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-interactive.png)

Si selecciona el modo **Automatizado**, obtendrá el siguiente error:

   ![Error de inicio de sesión](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-error.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acceso y administración de clústeres de HDInsight Spark mediante el uso del kit de herramientas de Azure para IntelliJ
Puede realizar varias operaciones mediante el Kit de herramientas de Azure para IntelliJ.

### <a name="access-the-job-view"></a>Acceso a la vista de trabajo
1. En Azure Explorer, expanda **HDInsight** y el nombre del clúster de Spark. Después, haga clic en **Trabajos**.
2. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Haga clic en el nombre de la aplicación para la que desea ver más detalles.
   
    ![Detalles de aplicación](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

Las casillas correspondientes a **Error**, **Driver Log** (Registro del controlador), **OutPut** (Salida), **Livy Log** (Registro de Livy) se rellenan según la aplicación que seleccionó.

También puede abrir Spark History UI (IU de historial de Spark) y YARN UI (IU de YARN), en el nivel de aplicación, haciendo clic en los botones correspondientes de la parte superior de la ventana.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark
1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.
2. En el panel del servidor de historial de Spark, puede usar el nombre de aplicación para buscar la aplicación que acaba de terminar de ejecutar. En el código anterior, se establece el nombre de la aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark era **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Inicio del portal de Ambari
1. En Azure Explorer, expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir el portal de administración de clústeres [Ambari]). 
2. Cuando se le pida, escriba las credenciales de administrador para el clúster. Estas son las credenciales que especificó durante el proceso de aprovisionamiento de clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure
De forma predeterminada, el kit de herramientas de Azure para IntelliJ enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones que desea que tengan acceso al clúster. 

1. En Azure Explorer, haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Manage Subscriptions** (Administrar suscripciones). 
2. En el cuadro de diálogo, desactive las casillas de la suscripción a las que no quiera dar acceso y haga clic en **Close**(Cerrar). También puede hacer clic en **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.

## <a name="run-a-spark-scala-application-locally"></a>Ejecución local de una aplicación Spark en Scala
Puede utilizar el kit de herramientas de Azure para IntelliJ si quiere ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente estas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### <a name="prerequisite"></a>Requisito previo
Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede producirse una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta excepción se produce porque falta WinUtils.exe en Windows. 

Para solucionar este error, tiene que [descargar el archivo ejecutable](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) y guardarlo en una ubicación como **C:\WinUtils\bin**. Después, agregue una variable de entorno **HADOOP_HOME** y establezca el valor de la variable en **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Ejecución de una aplicación Spark en Scala local
1. Inicie IntelliJ IDEA y cree un proyecto. En el cuadro de diálogo **New project** (Nuevo proyecto), realice las siguientes selecciones y después haga clic en **Next**(Siguiente).
   
   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight Local Run Sample (Scala)** (Ejemplo de ejecución local de Spark en HDInsight [Scala]).
   
    ![Selecciones en el cuadro de diálogo de nuevo proyecto](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
2. En la ventana siguiente, proporcione los detalles de proyecto siguientes y, a continuación, haga clic en **Finish** (Finalizar).
   
   * Proporcione un nombre de proyecto y la ubicación del proyecto.
   * En **Project SDK**(SDK de proyecto), asegúrese de proporcionar una versión de Java posterior a la 7.
   * Para **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear), **Download** (Descargar) y, luego, seleccione la versión de Scala para usar: Scala 2.11.x para Spark 2.0 y Scala 2.10.x para Spark 1.6.
     
       ![Selección de la versión de Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * En **Spark SDK** (SDK de Spark), descargue y utilice el SDK de [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Si lo desea, también puede usar el [repositorio de Spark Maven](http://mvnrepository.com/search?q=spark), pero asegúrese de que tiene instalado el repositorio de Maven correcto para poder desarrollar aplicaciones Spark. Por ejemplo, si usa Spark Streaming, compruebe que tiene la parte correspondiente a Spark Streaming instalada. También asegúrese de que está usando el repositorio marcado como Scala 2.10 para un clúster de Spark 1.6 y el marcado como Scala 2.11 para un clúster de Spark 2.0.
     
       ![Selección del SDK de Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta **src** que puede ejecutar localmente en el equipo.
   
    ![Ubicación de LogQuery](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Haga clic con el botón derecho en la aplicación **LogQuery** y seleccione **Run "LogQuery"** (Ejecutar LogQuery). En la pestaña **Run** (Ejecutar) en la parte inferior verá una salida similar a la que se ve a continuación.
   
   ![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Conversión de las aplicaciones IntelliJ IDEA existentes para usar el kit de herramientas de Azure para IntelliJ
Puede convertir las aplicaciones Spark en Scala existentes creadas en IntelliJ IDEA para que sean compatibles con el kit de herramientas de Azure para IntelliJ. Esto le permitirá utilizar el complemento para enviar las aplicaciones a un clúster de HDInsight Spark.

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo .iml asociado.
2. En el nivel raíz, verá un elemento de **module** similar al siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
   Edite el elemento para agregar `UniqueKey="HDInsightTool"` de forma que el elemento de **module** sea similar a lo siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
3. Guarde los cambios. La aplicación ahora debe ser compatible con el kit de herramientas de Azure para IntelliJ. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en el explorador de proyectos. El menú emergente ahora debería tener la opción **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

## <a name="troubleshooting"></a>Solución de problemas
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>Error "Please use a larger heap size" (Utilice un tamaño de montón mayor) en la ejecución local
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

Estos errores se producen porque el tamaño del montón no es lo suficientemente grande como para que se ejecute Spark. (Spark requiere al menos 471 MB. Puede obtener más detalles en [12081 SPARK](https://issues.apache.org/jira/browse/SPARK-12081)). Una solución sencilla es utilizar un SDK de Java de 64 bits. También puede cambiar la configuración de JVM en IntelliJ agregando las siguientes opciones:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Incorporación de opciones a la casilla de opciones de máquina virtual en IntelliJ](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback-and-known-issues"></a>Comentarios y problemas conocidos
Actualmente, la visualización de salidas de Spark directamente no se admite.

Si tiene sugerencias o comentarios, o si se le presenta algún problema al usar este complemento, no dude en enviarnos un correo electrónico a la dirección hdivstool@microsoft.com.

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)


