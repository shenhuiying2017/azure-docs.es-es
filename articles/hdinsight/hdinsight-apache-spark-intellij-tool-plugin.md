---
title: Uso del kit de herramientas de Azure para IntelliJ para crear aplicaciones de Scala para Spark | Microsoft Docs
description: "Uso de herramientas de HDInsight en el kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones de Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark."
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
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 494545ae20e0b766a3787ae462d5d0f4331853b1
ms.lasthandoff: 04/06/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>Uso del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark para el clúster de HDInsight

Uso de herramientas de HDInsight en el kit de herramientas de Azure para IntelliJ con el fin de desarrollar aplicaciones de Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark directamente desde el IDE de IntelliJ. Puede usar el complemento de las herramientas de HDInsight de varias maneras distintas:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

También puede seguir un vídeo [aquí](https://mix.office.com/watch/1nqkqjt5xonza) para comenzar.

> [!IMPORTANT]
> Esta herramienta se puede utilizar para crear y enviar solicitudes solo para un clúster de HDInsight Spark en Linux.
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. En este artículo se usa la versión 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Instalación de las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ
Las herramientas de HDInsight para IntelliJ están disponibles como parte del kit de herramientas de Azure para IntelliJ. Para obtener instrucciones sobre cómo instalar el kit de herramientas de Azure, consulte [Instalación del kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="log-into-your-azure-subscription"></a>Inicio de sesión en la suscripción de Azure
1. Inicie el IDE de IntelliJ y abra el Explorador de Azure. En el menú **View** (Ver) del IDE, haga clic en **Tool Windows** (Ventanas de herramientas) y en **Azure Explorer** (Explorador de Azure).
   
    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. Haga clic con el botón derecho en el nodo **Azure** de **Azure Explorer** (Explorador de Azure) y después haga clic en **Manage Subscriptions** (Administrar suscripciones).
3. En el cuadro de diálogo **Administrar suscripciones** (Administrar suscripciones), haga clic en **Sign in** (Iniciar sesión) y escriba las credenciales de Azure.
   
    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. Cuando haya iniciado sesión, en el cuadro de diálogo **Manage Subscriptions** (Administrar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Haga clic en el botón **Close** (Cerrar) del cuadro de diálogo.
5. En la pestaña **Azure Explorer** (Explorador de Azure), expanda **HDInsight** para ver los clústeres Spark de HDInsight incluidos en la suscripción.
   
    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. Puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados a este.
   
    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark
1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next**(Siguiente).
   
    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   
   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight (Scala)**(Spark en HDInsight [Scala]).
   * Haga clic en **Siguiente**.
2. En la siguiente ventana, proporcione los detalles del proyecto.
   
   * Proporcione un nombre de proyecto y la ubicación del proyecto.
   * Para **SDK de proyecto**, Java 1.7 o superior para clústeres Spark 1.6 y Java 1.8 para clústeres Spark 2.0.
   * En **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear) y en **Download** (Descargar). A continuación, seleccione la versión de Scala que desea usar.
   * * Elija **JDK 1.8 y Scala 2.11.x** si está dispuesto a enviar el trabajo al clúster Spark 2.0.
   * * Elija **JDK 1.7 o superior y Scala 2.10.x** si está dispuesto a enviar el trabajo al clúster Spark 1.6.

        ![](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * Para **SDK de Spark**, descargue y use el SDK de [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) (spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar es para el clúster Spark 2.0 y spark-assembly-x.jar es para el clúster Spark 1.6). Si lo desea, puede omitir este paso y utilizar en su lugar el [repositorio de Spark Maven](http://mvnrepository.com/search?q=spark). Sin embargo, debe comprobar que tiene instalado el repositorio de Maven correcto para poder desarrollar aplicaciones de Spark. Por ejemplo, debe asegurarse de que tiene la parte Spark Streaming instalada si está utilizando Spark Streaming. Asimismo, asegúrese de que utiliza el repositorio marcado como Scala 2.10 para clústeres Spark 1.6 y como Scala 2.11 para clústeres Spark 2.0.
     
       ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
   * Haga clic en **Finalizar**
3. El proyecto Spark creará automáticamente un artefacto. Para ver el artefacto, siga estos pasos.
   
   1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
   2. En el cuadro de diálogo **Project Structure** (Estructura del proyecto), haga clic en **Artifacts** (Artefactos) para ver el artefacto predeterminado que se crea.
      
       ![Crear archivo JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
      También puede crear su propio artefacto haciendo clic en el icono **+** (resaltado en la imagen anterior).
4. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Project** (Proyecto). Si **Project SDK** (SDK de proyecto) está establecido en 1.8, asegúrese de que **Project language level** (Nivel de lenguaje del proyecto) esté establecido en **7 - Diamonds, ARM, multi-catch, etc** (es opcional para clústeres Spark 2.0).
   
    ![Establecer el nivel de lenguaje del proyecto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. Agregue el código fuente de aplicación.
   
   1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en **src**, elija **New** (Nueva) y haga clic en **Scala class** (Clase de Scala).
      
       ![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, seleccione **Object** (Objeto) en **Kind** (Variante) y haga clic en **OK** (Aceptar).
      
       ![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. En el archivo **MyClusterApp.scala** , pegue el siguiente código. Este código lee los datos de HVAC.csv (disponible en todos los clústeres de Spark de HDInsight), recupera las filas que solo tienen un dígito en la séptima columna del CSV y escribe el resultado en **/HVACOut** bajo el contenedor de almacenamiento predeterminado para el clúster.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows which have only one digit in the 7th column in the CSV
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

1. Ejecute la aplicación en un clúster de HDInsight Spark.
   
   1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en el nombre del proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).
      
       ![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Se le pedirá que escriba las credenciales de la suscripción de Azure. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores.
      
      * Para **Spark clusters (Linux only)**(Clústeres de Spark [solo Linux]), seleccione el clúster de Spark de HDInsight en el que desea ejecutar la aplicación.
      * Necesita seleccionar un artefacto del proyecto IntelliJ o uno del disco duro.
      * En el cuadro de texto **Main class name** (Nombre de clase principal), haga clic en los puntos suspensivos (![...](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), seleccione la clase principal del código fuente de la aplicación y haga clic en **OK** (Aceptar).
        
          ![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.
      * Después de proporcionar todas las entradas, el cuadro de diálogo debería parecerse al siguiente.
        
          ![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      * Haga clic en **Enviar**.
   3. La pestaña **Spark Submission** (Envío de Spark) de la parte inferior de la ventana debe empezar a mostrar el progreso. También puede detener la aplicación haciendo clic en el botón rojo en la ventana "Spark Submission" (Envío de Spark).
      
       ![Resultado de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      En la siguiente sección, obtendrá información sobre cómo acceder a la salida de trabajo con las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ.

## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Acceso y administración de clústeres Spark en HDInsight mediante las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ
Puede realizar una serie de operaciones con las herramientas de HDInsight que forman parte del kit de herramientas de Azure para IntelliJ.

### <a name="access-the-job-view-directly-from-the-hdinsight-tools"></a>Acceso a la vista de trabajo directamente desde las herramientas de HDInsight
1. En **Azure Explorer** (Explorador de Azure), expanda **HDInsight** y el nombre del clúster de Spark. Después, haga clic en **Jobs** (Trabajos).
2. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Haga clic en el nombre de la aplicación para la que desea ver más detalles.
   
    ![Acceso a la vista de trabajos](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. Los cuadros **Error Message** (Mensaje de error), **Job Output** (Salida de trabajo), **Livy Job Logs** (Registros de trabajos de Livy) y **Spark Driver Logs** (Registros de controlador de Spark) se rellenan en función de la aplicación seleccionada.
4. También puede abrir **Spark History UI** (IU de historial de Spark) y **YARN UI** (IU de YARN), en el nivel de aplicación, haciendo clic en los botones correspondientes de la parte superior de la pantalla.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark
1. En **Azure Explorer** (Explorador de Azure), expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.
2. En el panel del Servidor de historial de Spark, puede buscar la aplicación que acaba de terminar de ejecutar con la utilización del nombre de aplicación. En el código anterior, estableció el nombre de aplicación con `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark era **MyClusterApp**.

### <a name="launch-the-ambari-portal"></a>Inicio del portal de Ambari
En **Azure Explorer** (Explorador de Azure), expanda **HDInsight**, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** [Abrir el portal de administración de clústeres (Ambari)]. Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure
De forma predeterminada, las herramientas de HDInsight enumeran los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones para las que desea tener acceso al clúster. En **Azure Explorer** (Explorador de Azure), haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Manage Subscriptions** (Administrar suscripciones). En el cuadro de diálogo, desactive las casillas de la suscripción a las que no quiera obtener acceso y haga clic en **Close**(Cerrar). También puede hacer clic en **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.

## <a name="run-a-spark-scala-application-locally"></a>Ejecución local de una aplicación Spark en Scala
Puede utilizar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ si quiere ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente, dichas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### <a name="prerequisite"></a>Requisito previo
Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede obtener una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) , que se produce por la ausencia del archivo WinUtils.exe en Windows. Para solucionar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) y guardarlo en una ubicación como **C:\WinUtils\bin**. Después, debe agregar una variable de entorno **HADOOP_HOME** y establecer el valor de la variable en **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Ejecución de una aplicación Spark en Scala local
1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next**(Siguiente).
   
    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
   
   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight Local Run Sample (Scala)**(Ejemplo de ejecución local de Spark en HDInsight [Scala]).
   * Haga clic en **Siguiente**.
2. En la siguiente ventana, proporcione los detalles del proyecto.
   
   * Proporcione un nombre de proyecto y la ubicación del proyecto.
   * En **Project SDK**(SDK de proyecto), asegúrese de proporcionar una versión de Java superior a la 7.
   * Para **SDK de Scala**, haga clic en **Crear**, **Descargar**y, luego, seleccione la versión de Scala para usar  **Scala 2.11.x para Spark 2.0 y Scala 2.10.x para Spark 1.6**.
     
       ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * En **Spark SDK** (SDK de Spark), descargue [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) el SDK para utilizarlo. Si lo desea, puede omitir este paso y utilizar en su lugar el [repositorio de Spark Maven](http://mvnrepository.com/search?q=spark). Sin embargo, debe comprobar que tiene instalado el repositorio de Maven correcto para poder desarrollar aplicaciones de Spark. Por ejemplo, debe asegurarse de que tiene la parte Spark Streaming instalada si está utilizando Spark Streaming. Asimismo, asegúrese de que utiliza el repositorio marcado como Scala 2.10 para clústeres Spark 1.6 y como Scala 2.11 para clústeres Spark 2.0.
     
       ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
   * Haga clic en **Finalizar**
3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta **src** que puede ejecutar localmente en el equipo.
   
    ![Aplicación de Scala local](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Haga clic con el botón derecho en la aplicación **LogQuery** y seleccione **Run LogQuery** (Ejecutar LogQuery). Verá un resultado similar al siguiente en la pestaña **Run** (Ejecutar) en la parte inferior.
   
   ![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Conversión de las aplicaciones IntelliJ IDEA existentes para usar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ
También puede convertir las aplicaciones existentes Spark en Scala creadas en IntelliJ IDEA para que sean compatibles con las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ. Esto le permitirá utilizar la herramienta para enviar las aplicaciones a un clúster de HDInsight Spark. Para ello, puede seguir estos pasos:

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo .iml asociado.
2. En el nivel raíz, verá un elemento de **module** similar al siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
3. Edite el elemento para agregar `UniqueKey="HDInsightTool"` de forma que el elemento de **module** sea similar a lo siguiente:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
4. Guarde los cambios. La aplicación ahora debe ser compatible con las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en el Explorador de proyectos. El menú emergente ahora debería tener la opción **Submit Spark Application to HDInsight**(Enviar aplicación Spark a HDInsight).

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

Esto se debe a que el tamaño del montón no es lo suficientemente grande como para que se ejecute el Spark, ya que requiere 471 MB como mínimo (encontrará más información en [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) ). Una solución sencilla es utilizar un SDK de Java de 64 bits. También puede cambiar la configuración de JVM en IntelliJ agregando las siguientes opciones:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback--known-issues"></a>Comentarios y problemas conocidos
Actualmente, la visualización de salidas de Spark directamente no se admite y estamos trabajando en ello.

Si tiene sugerencias o comentarios, o si encuentra algún problema al usar esta herramienta, no dude en enviarnos un correo electrónico a hdivstool a microsoft punto com.

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
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con el fin de depurar aplicaciones Spark de forma remota](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)


