---
title: "Kit de herramientas de Azure para Eclipse: creación de aplicaciones Scala para HDInsight Spark | Microsoft Docs"
description: "Uso de herramientas de HDInsight en el kit de herramientas de Azure para Eclipse con el fin de desarrollar aplicaciones de Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark directamente desde el IDE de Eclipse."
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
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 013a3175d5e19689629d1d0ea3b413184e71c485
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017


---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Uso del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark para un clúster de HDInsight

Use las herramientas de HDInsight en el kit de herramientas de Azure para Eclipse con el fin de desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de Azure HDInsight Spark directamente desde el IDE de Eclipse. Puede usar el complemento de las herramientas de HDInsight de varias maneras distintas:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

> [!IMPORTANT]
> Esta herramienta se puede utilizar para crear y enviar solicitudes solo para un clúster de HDInsight Spark en Linux.
> 
> 

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java versión 8, que se usa para el tiempo de ejecución del IDE de Eclipse. Puede descargarlo desde el [sitio web de Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE de Eclipse. En este artículo se utiliza Eclipse Neon. Puede instalarlo desde el [sitio web de Eclipse](https://www.eclipse.org/downloads/).
* IDE de Scala para Eclipse. 
  
  * **Si tiene instalado el IDE de Eclipse**, puede agregar el complemento del IDE de Scala accediendo a **Ayuda** > **Instalar nuevo software**. Luego, agregue [http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site) como origen para descargar el complemento de Scala para Eclipse. 
  * **Si no tiene instalado el IDE de Eclipse**, puede instalar el IDE de Scala directamente desde [el sitio web de Scala](http://scala-ide.org/download/sdk.html). Descargue el archivo .zip, extráigalo, acceda a la carpeta **/eclipse** y ejecute el archivo **eclipse.exe** de dicha carpeta.
    
    > [!NOTE]
    > Los pasos de este artículo están basados en un escenario en el que se utiliza el IDE de Eclipse con el complemento de Scala instalado.
    > 
    > 
* SDK de Spark. Puede descargarlo en [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
* e(fx)clipse. Puede instalarlo desde la [página de descargas del sitio web de Eclipse](https://www.eclipse.org/efxclipse/install.html).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Instalación de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse
Las herramientas de HDInsight para Eclipse están disponibles como parte del kit de herramientas de Azure para Eclipse. Para obtener instrucciones de instalación, consulte [Instalación del Kit de herramientas de Azure para Eclipse](../azure-toolkit-for-eclipse-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sesión en la suscripción de Azure
1. Inicie el IDE de Eclipse y abra Azure Explorer. En el menú **Window** (Ventana), haga clic en **Show View** (Mostrar vista) y en **Other** (Otros). En el cuadro de diálogo que se abre, expanda **Azure**, haga clic en **Azure Explorer** y en **OK** (Aceptar).

    ![Cuadro de diálogo Show View (Mostrar vista)](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Haga clic con el botón derecho en el nodo **Azure** y después haga clic en **Iniciar sesión**.
3. En el cuadro de diálogo **Inicio de sesión en Microsoft Azure**, elija el modo de autenticación, haga clic en **Iniciar sesión** y escriba las credenciales de Azure.
   
    ![Cuadro de diálogo de inicio de sesión en Azure](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Cuando haya iniciado sesión, en el cuadro de diálogo **Select Subscriptions** (Seleccionar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Haga clic en **Seleccionar** para cerrar el cuadro.

    ![Cuadro de diálogo de selección de suscripciones](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. En la pestaña **Azure Explorer**, expanda **HDInsight** para ver los clústeres de HDInsight Spark de su suscripción.
   
    ![Clústeres de HDInsight Spark en Azure Explorer](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados al clúster.
   
    ![Expansión de un nombre de clúster para ver recursos](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configuración de un proyecto Spark en Scala de un clúster Spark en HDInsight

1. En el área de trabajo del IDE de Eclipse, haga clic en **File** (Archivo), en **New** (Nuevo) y en **Project** (Proyecto). 
2. En el asistente New Project (Nuevo proyecto), expanda **HDInsight**, seleccione **Spark on HDInsight (Scala)** (Spark en HDInsight [Scala]) y haga clic en **Next** (Siguiente).

    ![Selección del proyecto Spark on HDInsight (Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. En el cuadro de diálogo **New HDInsight Scala Project** (Nuevo proyecto de Scala para HDInsight), proporcione los valores siguientes y luego haga clic en **Next** (Siguiente):
   * Escriba un nombre para el proyecto.
   * En el área **JRE**, asegúrese de que el valor de **Use an execution environment JRE** (Utilizar un entorno de ejecución JRE) esté establecido en **JavaSE-1.7**.
   * Asegúrese de que el SDK de Spark esté establecido en la ubicación donde descargó el SDK. El vínculo a la ubicación de descarga figura en la sección [Requisitos previos](#prerequisites) al principio de este artículo. También puede descargar el SDK desde el vínculo del cuadro de diálogo.

    ![Cuadro de diálogo nuevo proyecto de Scala para HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
4.  En el siguiente cuadro de diálogo, haga clic en la pestaña **Libraries** (Bibliotecas) y deje los valores predeterminados, luego clic en **Finish** (Finalizar). 
   
    ![Pestaña de bibliotecas](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)


## <a name="run-a-spark-scala-application-on-an-azure-data-lake-store-cluster"></a>Ejecución de una aplicación Spark en Scala en un clúster de Azure Data Lake Store
Si desea enviar una aplicación a Azure Data Lake Store, tiene que elegir el modo **Interactivo** durante el proceso de inicio de sesión en Azure. 

   ![Opción de modo interactivo en el inicio de sesión](./media/hdinsight-apache-spark-eclipse-tool-plugin/Interactive-Authentication.png)

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Creación de una aplicación de Scala para un clúster de HDInsight Spark

1. En el IDE de Eclipse, en el Explorador de paquetes, expanda el proyecto que creó anteriormente, haga clic con el botón derecho en **src**, seleccione **New** (Nuevo) y haga clic en **Other** (Otros).
2. En el cuadro de diálogo **Select a wizard** (Seleccionar un asistente), expanda **Scala Wizards** (Asistentes de Scala), haga clic en **Scala Object** (Objeto de Scala) y en **Next** (Siguiente).
   
    ![Cuadro de diálogo de selección de asistente](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. En el cuadro de diálogo **Create New File** (Crear nuevo archivo), escriba un nombre para el objeto y haga clic en **Finish** (Finalizar).
   
    ![Cuadro de diálogo de creación de nuevo archivo](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Pegue el código siguiente en el editor de texto:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasbs:///HVACOut")
          }        
        }
5. Ejecute la aplicación en un clúster de HDInsight Spark:
   
   1. En el Explorador de paquetes, haga clic con el botón derecho en el nombre del proyecto y seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).        
   2. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores y luego haga clic en **Submit** (Enviar):
      
      * Para **Cluster Name**(Nombre del clúster), seleccione el clúster Spark en HDInsight en el que quiere ejecutar la aplicación.
      * Seleccione un artefacto del proyecto Eclipse o uno de un disco duro.
      * En el cuadro de texto **Main class name** (Nombre de la clase principal), escriba el nombre del objeto que especificó en el código.
      * Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.
        
       ![Cuadro de diálogo de envío de Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
   3. La pestaña **Spark Submission** (Envío de Spark) debería empezar a mostrar el progreso. Puede detener la aplicación haciendo clic en el botón rojo de la ventana **Spark Submission** (Envío de Spark). También puede ver los registros de la ejecución de esta aplicación concreta haciendo clic en el icono de globo (que se indica con el cuadro azul en la imagen).
      
       ![Ventana de envío de Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)
      
## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acceso y administración de clústeres HDInsight Spark mediante las herramientas de HDInsight del kit de herramientas de Azure para Eclipse
Puede realizar varias operaciones mediante herramientas de HDInsight, incluido el acceso a la salida del trabajo.

### <a name="access-the-storage-container-for-the-cluster"></a>Acceso al contenedor de almacenamiento para el clúster
1. En Azure Explorer, expanda el nodo raíz **HDInsight** para ver una lista de los clústeres HDInsight Spark disponibles.
2. Expanda el nombre del clúster para ver la cuenta de almacenamiento y el contenedor de almacenamiento predeterminado para el clúster.
   
    ![Cuenta de almacenamiento y contenedor de almacenamiento predeterminado](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Haga clic en el nombre del contenedor de almacenamiento asociado con el clúster. En el panel derecho, haga doble clic en la carpeta **HVACOut**. Abra uno de los archivos **part-** para ver la salida de la aplicación.

### <a name="access-the-spark-history-server"></a>Acceso al servidor de historial de Spark
1. En Azure Explorer, haga clic con el botón derecho en el nombre del clúster Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.
2. En el panel del servidor de historial de Spark, use el nombre de aplicación para buscar la aplicación que acaba de terminar de ejecutar. En el código anterior, se establece el nombre de la aplicación mediante `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark era **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Inicio del portal de Ambari
1. En Azure Explorer, haga clic con el botón derecho en el nombre del clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir portal de administración de clústeres [Ambari]). 
2. Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

### <a name="manage-azure-subscriptions"></a>Administración de suscripciones de Azure
De forma predeterminada, las herramientas de HDInsight en el kit de herramientas de Azure para Eclipse enumeran los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones que desea que tengan acceso al clúster. 

1. En Azure Explorer, haga clic con el botón derecho en el nodo raíz de **Azure** y seleccione **Manage Subscriptions** (Administrar suscripciones). 
2. En el cuadro de diálogo, desactive las casillas de la suscripción a las que no quiera dar acceso y haga clic en **Close**(Cerrar). También puede hacer clic en **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.

## <a name="run-a-spark-scala-application-locally"></a>Ejecución local de una aplicación Spark en Scala
Puede utilizar las herramientas de HDInsight del kit de herramientas de Azure para Eclipse si quiere ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente estas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### <a name="prerequisite"></a>Requisito previo
Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede producirse una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta excepción se produce porque falta **WinUtils.exe** en Windows. 

Para solucionar este error, tiene que [descargar el archivo ejecutable](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) y guardarlo en una ubicación como **C:\WinUtils\bin**. Después, tiene que agregar una variable de entorno **HADOOP_HOME** y establecer el valor de la variable en **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Ejecución de una aplicación Spark en Scala local
1. Inicie Eclipse y cree un proyecto. En el cuadro de diálogo **New Project** (Nuevo proyecto), realice las siguientes selecciones y después haga clic en **Next**(Siguiente).
   
   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight Local Run Sample (Scala)** (Ejemplo de ejecución local de Spark en HDInsight [Scala]).

    ![Cuadro de diálogo Nuevo proyecto](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
2. Para proporcionar los detalles del proyecto, siga los pasos del 3 a 6 de la sección anterior [Configuración de un proyecto Spark en Scala de un clúster Spark en HDInsight](#set-up-a-spark-scala-project-for-an-hdinsight-spark cluster).
3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta **src** que puede ejecutar localmente en el equipo.
   
    ![Ubicación de LogQuery](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
4. Haga clic con el botón derecho en la aplicación **LogQuery**, seleccione **Run as** (Ejecutar como) y haga clic en **1 Scala Application** (1 aplicación de Scala). Verá una salida similar a la siguiente en la pestaña **Console** (Consola) de la parte inferior:
   
   ![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="feedback-and-known-issues"></a>Comentarios y problemas conocidos
Actualmente, la visualización de salidas de Spark directamente no se admite.

Si tiene sugerencias o comentarios, o si se le presenta algún problema al usar esta herramienta, no dude en enviarnos un correo electrónico a la dirección hdivstool@microsoft.com.

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
* [Uso del kit de herramientas de Azure para IntelliJ con el fin de crear y enviar aplicaciones Spark en Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)


