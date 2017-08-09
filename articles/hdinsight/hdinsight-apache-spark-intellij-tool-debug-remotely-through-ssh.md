---
title: "Kit de herramientas de Azure para IntelliJ: depuración de aplicaciones de Spark de forma remota mediante SSH | Microsoft Docs"
description: "Guía paso a paso sobre cómo usar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ para depurar aplicaciones de forma remota que se ejecutan en clústeres de HDInsight mediante VPN"
keywords: "depurar remotamente intellij, depuración remota intellij, ssh, intellij, hdinsight, depurar intellij, depuración"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depuración de aplicaciones de Spark de forma remota en un clúster de HDInsight con el kit de herramientas de Azure para IntelliJ mediante SSH

En este artículo se ofrece una guía paso a paso sobre cómo usar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ para depurar aplicaciones de forma remota que se ejecutan en clústeres de HDInsight. Puede seguir un [vídeo](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) para depurar el proyecto.

**Requisitos previos:**

* **Herramientas de HDInsight del kit de herramientas de Azure para IntelliJ**. Es parte del kit de herramientas de Azure para IntelliJ. Para obtener más información, consulte [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)(Instalación del kit de herramientas de Azure para IntelliJ).
* **Kit de herramientas de Azure para IntelliJ**. Utilícelo para crear aplicaciones Spark para el clúster de HDInsight. Para más información, siga las instrucciones en [Uso del kit de herramientas de Azure para IntelliJ con el fin de crear aplicaciones Spark para el clúster de HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Servicio SSH de HDInsight con administración del nombre de usuario y la contraseña.** Para más información vea [Conexión a través de SSH con HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) y [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari, JobHistory, NameNode, Oozie y otras interfaces de usuario web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Creación de una aplicación Scala de Spark y configuración para la depuración remota
1. Inicie IntelliJ IDEA y, después, cree un proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next**(Siguiente). En este artículo se utiliza **Spark on HDInsight Cluster Run Sample (Scala)** [Spark en el ejemplo de ejecución de clúster de HDInsight (Scala)] como ejemplo.

     ![Creación de un proyecto de depuración](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - En el panel izquierdo, seleccione **HDInsight**.
   - En el panel derecho, seleccione una plantilla de Java o Scala según sus preferencias. Elija entre las siguientes opciones: 
      - **Spark en HDInsight (Scala)**
      - **Spark en HDInsight (Java)**
      - **Spark en el ejemplo de ejecución de clúster de HDInsight (Scala)**
   - Herramienta de compilación: el asistente para crear un proyecto de Scala admite Maven o SBT para administrar las dependencias y compilar el proyecto de Scala. Seleccione una según las necesidades.
2. En la siguiente ventana, proporcione los detalles del proyecto.

   ![Selección del SDK de Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - Proporcione un nombre de proyecto y la ubicación del proyecto.
   - En **Project SDK** (SDK de proyecto), use **Java 1.8** para el clúster **Spark 2.x** o **Java 1.7** para el clúster **Spark 1.x**.
   - En **Versión de Spark**, el asistente para crear un proyecto de Scala integra la versión correcta del SDK de Spark y el SDK de Scala. Si la versión del clúster de Spark es inferior a la 2.0, elija **Spark 1.x**. De lo contrario, seleccione **Spark 2.x**. En este artículo se usa **Spark 2.0.2 (Scala 2.11.8)** como ejemplo.
3. Seleccione **src** > **main** > **scala** para abrir el código en el proyecto. En este artículo se utiliza el script **SparkCore_wasbloTest** como ejemplo.
4. Para acceder al menú **Edit Configurations** (Editar configuraciones), seleccione el icono en la esquina superior derecha. Desde este menú, puede crear o modificar las configuraciones para la depuración remota.

   ![Editar configuraciones](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. En la ventana **Run/Debug Configurations** (Ejecutar/Depuración configuraciones), haga clic en el signo más (**+**). Después, seleccione la opción **Submit Spark Job** (Enviar trabajo de Spark).

   ![Editar configuraciones](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. Escriba la información en los campos **Name** (Nombre), **Spark cluster** (Clúster de Spark) y **Main class name** (Nombre de clase principal). Después seleccione **Advanced configuration** (Configuración avanzada). 

   ![Ejecutar configuraciones de depuración](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. En el cuadro de diálogo **Spark Submission Advanced Configuration** (Configuración avanzada de envío de Spark), seleccione **Enable Spark remote debug** (Habilitar depuración remota de Spark). Escriba el nombre de usuario o la contraseña de SSH, o use un archivo de clave privada. Para guardarlo, seleccione **Aceptar**.

   ![Habilitar la depuración remota de Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. La configuración se guarda ahora con el nombre especificado. Para ver los detalles de configuración, seleccione el nombre de configuración. Para realizar cambios, seleccione **Edit Configurations** (Editar configuraciones). 
9. Después de completar la configuración, puede ejecutar el proyecto en el clúster remoto o realizar la depuración remota.

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>Obtenga información acerca de cómo realizar la depuración remota y ejecución remota
### <a name="scenario-1-perform-remote-run"></a>Escenario 1: Realizar la ejecución remota
1. Para ejecutar el proyecto de forma remota, seleccione el icono de **ejecución**.

   ![Hacer clic en el icono de ejecución](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. La ventana **HDInsight Spark Submission** (Envío de Spark de HDInsight Spark) muestra el estado de ejecución de la aplicación. Puede supervisar el progreso del trabajo de Scala en función de la información que se muestra aquí.

   ![Hacer clic en el icono de ejecución](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Escenario 2: Realizar la depuración remota
1. Configure un punto de interrupción y, después, seleccione el icono de **depuración**.

    ![Hacer clic en el icono de depuración](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. Cuando la ejecución del programa alcance el punto de interrupción, verá una pestaña **Debugger** (Depurador) en el panel inferior. También puede ver la información de parámetros y variables en la ventana **Variable**. Haga clic en el icono de **paso a paso por procedimientos** para pasar a la siguiente línea de código. A continuación, siga recorriendo el código. Seleccione el icono de **reanudar programa** para seguir ejecutando el código. Puede revisar el estado de ejecución en la ventana **HDInsight Spark Submission** (Envío de HDInsight Spark). 

   ![Pestaña Depuración](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Escenario 3: Realizar la depuración remota y corrección de errores
En esta sección, se muestra cómo actualizar dinámicamente el valor de la variable mediante el uso de la funcionalidad de depuración de IntelliJ para una revisión sencilla. En el ejemplo de código siguiente, se produce una excepción porque el archivo de destino ya existe.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Para realizar la depuración remota y la corrección de errores
1. Configure dos puntos de interrupción y, después, haga clic en el icono de **depuración** para iniciar el proceso de depuración remota.

2. El código se detiene en el primer punto de interrupción y el parámetro y la información de las variables se muestran en la ventana **Variable**. 

3. Haga clic en el icono de **reanudar programa** para continuar. El código se detiene en el segundo punto. La excepción se detecta según lo previsto.

  ![Iniciar error](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Vuelva a hacer clic en el icono de **reanudar programa**. La ventana **HDInsight Spark Subsmission**  (Envío de HDInsight Spark) muestra un error de ejecución del trabajo.

  ![Envío de error](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Para actualizar dinámicamente el valor de variable mediante la funcionalidad de depuración de IntelliJ, vuelva a seleccionar **Depurar**. Las ventanas de variables vuelven a aparecer. 

6. Haga clic con el botón derecho en el destino en el pestaña **Depurar** y, a continuación, seleccione **Establecer valor**. Ahora, escriba un nuevo valor para la variable. A continuación, seleccione **Entrar** para guardar el valor. 

  ![Establecer valor](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Haga clic en el icono de **reanudar programa** para seguir ejecutando el programa. Esta vez no se detecta la excepción. Puede ver que el proyecto se ejecuta correctamente sin ninguna excepción.

  ![Depurar sin excepciones](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Demostración
* Creación del proyecto Scala (vídeo): [Creación de aplicaciones Scala de Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (en inglés)
* Depuración remota (vídeo): [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark en clústeres de HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (en inglés)

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
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con el fin de crear y enviar aplicaciones Spark en Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar aplicaciones de forma remota en HDInsight Spark mediante VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)
 
