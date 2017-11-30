---
title: "Kit de herramientas de Azure para Eclipse: creación de aplicaciones Scala para HDInsight Spark | Microsoft Docs"
description: "Use las herramientas de HDInsight del Kit de herramientas de Azure para Eclipse para desarrollar aplicaciones de Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark, directamente desde el IDE de Eclipse."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: ede1a974b32227edf44464ed56ae85a1ea7ee97b
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Uso del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark para un clúster de HDInsight

Use las herramientas de HDInsight en el kit de herramientas de Azure para Eclipse con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de Azure HDInsight Spark directamente desde el IDE de Eclipse. Puede usar el complemento de las herramientas de HDInsight de varias maneras distintas:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

> [!IMPORTANT]
> Puede usar esta herramienta para crear y enviar aplicaciones únicamente para un clúster de HDInsight Spark en Linux.
> 
> 

## <a name="prerequisites"></a>Requisitos previos

* Clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java versión 8, que se usa para el tiempo de ejecución del IDE de Eclipse. Puede descargarlo desde el [sitio web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE de Eclipse. En este artículo se utiliza Eclipse Neon. Puede instalarlo desde el [sitio web de Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalación de las herramientas de HDInsight del Kit de herramientas de Azure para Eclipse y el complemento Scala
### <a name="install-hdinsight-toolsazure-toolkit-for"></a>Instalación de HDInsight Toolsazure-toolkit-for
Las herramientas de HDInsight para Eclipse están disponibles como parte del kit de herramientas de Azure para Eclipse. Para obtener instrucciones de instalación, consulte [Instalación del Kit de herramientas de Azure para Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>Instalación del complemento Scala
Al abrir Eclipse, las herramientas de HDInsight detectan automáticamente si se ha instalado el complemento Scala. Seleccione **Aceptar** para continuar y luego siga las instrucciones para instalar el complemento desde Marketplace de Eclipse.

![Instalación automática del complemento Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure
1. Inicie el IDE de Eclipse y abra Azure Explorer. En el menú **Window** (Ventana), seleccione **Show View** (Mostrar vista) y luego **Other** (Otros). En el cuadro de diálogo que se abre, expanda **Azure**, seleccione **Azure Explorer** y luego **Aceptar**.

   ![Cuadro de diálogo Show View (Mostrar vista)](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Haga clic con el botón derecho en el nodo **Azure** y después seleccione **Iniciar sesión**.
3. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, elija el método de autenticación, seleccione **Iniciar sesión** y escriba las credenciales de Azure.
   
   ![Cuadro de diálogo de inicio de sesión en Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Haga clic en **Seleccionar** para cerrar el cuadro.

   ![Cuadro de diálogo de selección de suscripciones](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. En la pestaña **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight Spark de su suscripción.
   
   ![Clústeres de HDInsight Spark en Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados al clúster.
   
   ![Expansión de un nombre de clúster para ver recursos](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)



## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configuración de un proyecto Spark en Scala de un clúster Spark en HDInsight

1. En el área de trabajo del IDE de Eclipse, seleccione **Archivo**, **Nuevo** y luego **Proyecto**. 
2. En el Asistente para nuevos proyectos, expanda **HDInsight**, seleccione **Spark en HDInsight (Scala)** y luego **Siguiente**.

   ![Selección del proyecto Spark on HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. El asistente para la creación de proyectos de Scala detecta automáticamente si se ha instalado el complemento Scala. Seleccione **Aceptar** para continuar con la descarga del complemento Scala y luego siga las instrucciones para reiniciar Eclipse.

   ![Comprobación de Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. En el cuadro de diálogo **New HDInsight Scala Project** (Nuevo proyecto de Scala para HDInsight), proporcione los valores siguientes y luego seleccione **Siguiente**:
   * Escriba un nombre para el proyecto.
   * En el área **JRE**, asegúrese de que el valor de **Use an execution environment JRE** (Uso de un entorno de ejecución JRE) esté establecido en **JavaSE-1.7** o posterior.
   * En el área de **Biblioteca de Spark**, puede elegir la opción **Use Maven to configure Spark SDK** (Usar Maven para configurar el SDK de Spark).  Nuestra herramienta integra la versión correcta del SDK de Spark y de Scala. También puede elegir la opción **Add Spark SDK manually** (Agregar SDK de Spark manualmente) y descargar y agregar dicho SDK de forma manual.

   ![Cuadro de diálogo nuevo proyecto de Scala para HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. En el siguiente cuadro de diálogo, seleccione **Finalizar**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Creación de una aplicación de Scala para un clúster de HDInsight Spark

1. En el IDE de Eclipse, en el Explorador de paquetes, expanda el proyecto que ha creado anteriormente, haga clic con el botón derecho en **src**, seleccione **Nuevo** y luego **Other** (Otros).
2. En el cuadro de diálogo **Select a wizard** (Seleccionar un asistente), expanda **Scala Wizards** (Asistentes de Scala), seleccione **Scala Object** (Objeto de Scala) y luego **Siguiente**.
   
   ![Cuadro de diálogo de selección de asistente](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. En el cuadro de diálogo **Crear nuevo archivo**, escriba un nombre para el objeto y seleccione **Finalizar**.
   
   ![Cuadro de diálogo de creación de nuevo archivo](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Pegue el código siguiente en el editor de texto:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. Ejecute la aplicación en un clúster de HDInsight Spark:
   
   a. En el Explorador de paquetes, haga clic con el botón derecho en el nombre del proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).        
   b. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores y luego seleccione **Enviar**:
      
      * Para **Cluster Name**(Nombre del clúster), seleccione el clúster Spark en HDInsight en el que quiere ejecutar la aplicación.
      * Seleccione un artefacto del proyecto Eclipse o uno de un disco duro. El valor predeterminado depende del elemento en el que se hace clic con el botón derecho desde el explorador de paquetes.
      * En la lista desplegable **Nombre de la clase principal**, el asistente para envío muestra todos los nombres de objetos del proyecto. Seleccione o escriba uno que quiera ejecutar. Si ha seleccionado un artefacto de una unidad de disco duro, debe escribir manualmente el nombre de la clase principal. 
      * Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.
        
      ![Cuadro de diálogo de envío de Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. La pestaña **Spark Submission** (Envío de Spark) debería empezar a mostrar el progreso. Puede detener la aplicación si selecciona el botón rojo de la ventana **Spark Submission** (Envío de Spark). También puede ver los registros de ejecución de esta aplicación concreta si selecciona el icono de globo (que se indica con el cuadro azul en la imagen).
      
   ![Ventana de envío de Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acceso y administración de clústeres HDInsight Spark mediante las herramientas de HDInsight del kit de herramientas de Azure para Eclipse
Puede realizar varias operaciones mediante herramientas de HDInsight, incluido el acceso a la salida del trabajo.

### <a name="access-the-job-view"></a>Acceso a la vista de trabajo
1. En Azure Explorer, expanda **HDInsight** y el nombre del clúster de Spark. Después, seleccione **Trabajos**. 

   ![Nodo Vista de trabajos](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Seleccione el nodo **Trabajos**. Si la versión de Java es anterior a la **1.8**, las Herramientas de HDInsight le recordarán automáticamente que instale el conector **E(fx)clipse**. Seleccione **Aceptar** para continuar y luego siga las instrucciones del asistente para instalarlo desde el Marketplace de Eclipse y reiniciar Eclipse. 

   ![Instalación de E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Abra la vista de trabajo desde el nodo **Trabajos**. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Seleccione el nombre de la aplicación para la que desea ver más detalles.

   ![Detalles de aplicación](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Luego puede realizar cualquiera de estas acciones:

   * Mantener el puntero sobre el gráfico del trabajo. Muestra información básica sobre el trabajo en ejecución. Seleccione el gráfico del trabajo para ver las fases y la información que genera cada trabajo.

     ![Detalles de fase de trabajo](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Para ver registros usados con frecuencia, como **Driver Stderr**, **Driver Stdout** y **Directory Info**, seleccione la pestaña **Registro**.

     ![Detalles del registro](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Abra la interfaz de usuario de historial de Spark y la interfaz de usuario de YARN (en el nivel de aplicación) al seleccionar los hipervínculos de la parte superior de la ventana.

### <a name="access-the-storage-container-for-the-cluster"></a>Acceso al contenedor de almacenamiento para el clúster
1. En Azure Explorer, expanda el nodo raíz **HDInsight** para ver una lista de los clústeres HDInsight Spark disponibles.
2. Expanda el nombre del clúster para ver la cuenta de almacenamiento y el contenedor de almacenamiento predeterminado para el clúster.
   
   ![Cuenta de almacenamiento y contenedor de almacenamiento predeterminado](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Seleccione el nombre del contenedor de almacenamiento asociado al clúster. En el panel derecho, haga doble clic en la carpeta **HVACOut**. Abra uno de los archivos **part-** para ver la salida de la aplicación.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark
1. En Azure Explorer, haga clic con el botón derecho en el nombre del clúster Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Las ha especificado al aprovisionar el clúster.
2. En el panel del servidor de historial de Spark, use el nombre de aplicación para buscar la aplicación que acaba de terminar de ejecutar. En el código anterior, se establece el nombre de la aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de la aplicación Spark era **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Inicio del portal de Ambari
1. En Azure Explorer, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir portal de administración de clústeres [Ambari]). 
2. Cuando se le pida, escriba las credenciales de administrador para el clúster. Las ha especificado al aprovisionar el clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure
De forma predeterminada, las herramientas de HDInsight en el kit de herramientas de Azure para Eclipse enumeran los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones que desea que tengan acceso al clúster. 

1. En Azure Explorer, haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Manage Subscriptions** (Administrar suscripciones). 
2. En el cuadro de diálogo, desactive las casillas de la suscripción a la que no quiere acceder y luego seleccione **Cerrar**. También puede seleccionar **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.

## <a name="run-a-spark-scala-application-locally"></a>Ejecución local de una aplicación Spark en Scala
Puede utilizar las herramientas de HDInsight del kit de herramientas de Azure para Eclipse si quiere ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente estas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### <a name="prerequisite"></a>Requisito previo
Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede producirse una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta excepción se produce porque falta **WinUtils.exe** en Windows. 

Para solucionar este error, debe [descargar el archivo ejecutable](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) en una ubicación como **C:\WinUtils\bin**, después agregar la variable de entorno **HADOOP_HOME** y establecer el valor de la variable en **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Ejecución de una aplicación Spark en Scala local
1. Inicie Eclipse y cree un proyecto. En el cuadro de diálogo **Nuevo proyecto**, realice las siguientes selecciones y después seleccione **Siguiente**.
   
   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight Local Run Sample (Scala)** (Ejemplo de ejecución local de Spark en HDInsight [Scala]).

   ![Cuadro de diálogo Nuevo proyecto](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Para proporcionar los detalles del proyecto, siga los pasos del 3 a 6 de la sección anterior [Configuración de un proyecto Spark en Scala de un clúster Spark en HDInsight](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta **src** que puede ejecutar localmente en el equipo.
   
   ![Ubicación de LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Haga clic con el botón derecho en la aplicación **LogQuery**, seleccione **Ejecutar como** y luego **1 Scala Application** (1 aplicación de Scala). Aparece una salida como esta en la pestaña **Consola**:
   
   ![Resultado de la ejecución local de la aplicación Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Problemas conocidos
Para enviar una aplicación a Azure Data Lake Store, seleccione el modo **Interactivo** durante el proceso de inicio de sesión de Azure. Si selecciona el modo **Automatizado**, puede aparecer un error.

![Inicio de sesión interactivo](./media/apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Puede elegir un clúster de Azure Data Lake para enviar la aplicación con cualquier método de inicio de sesión.

Actualmente, la visualización de salidas de Spark directamente no se admite.

## <a name="feedback"></a>Comentarios
Si tiene algún comentario o si experimenta algún problema al usar esta herramienta, envíe un correo electrónico a hdivstool@microsoft.com.

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Machine Learning con Apache Spark en HDInsight de Azure](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](../hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del kit de herramientas de Azure para IntelliJ con el fin de crear y enviar aplicaciones Spark en Scala](apache-spark-intellij-tool-plugin.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

