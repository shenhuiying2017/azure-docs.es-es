 <properties
	pageTitle="Creación de aplicaciones Spark en Scala con el complemento de HDInsight para Eclipse | Microsoft Azure"
	description="Obtenga información sobre cómo crear una aplicación independiente Spark para ejecutarla en clústeres de HDInsight Spark."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="nitinme"/>


# Uso del complemento de herramientas de HDInsight para Eclipse con el fin de crear aplicaciones Spark destinadas al clúster Spark en HDInsight (Linux)

En este artículo se proporcionan instrucciones paso a paso sobre cómo desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster Spark en HDInsight mediante el complemento de HDInsight para Eclipse. Puede usar el complemento de varias maneras diferentes:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

>[AZURE.IMPORTANT] Esta herramienta se puede utilizar para crear y enviar solicitudes solo para un clúster de HDInsight Spark en Linux.


##Requisitos previos

* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres Apache Spark en HDInsight mediante opciones personalizadas](hdinsight-apache-spark-jupyter-spark-sql.md).

* Oracle Java Development Kit (versiones 7 y 8).
	* **Java SDK 7** se utiliza para compilar proyectos Spark, ya que los clústeres de HDInsight admiten la versión 7 de Java. Puede descargar Java SDK 7 [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).
	* **Java SDK 8** se utiliza en el entorno de tiempo de ejecución del IDE de Eclipse. Puede descargarla [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* IDE de Eclipse. En este artículo se utiliza Eclipse Neon. Puede instalarlo desde [aquí](https://www.eclipse.org/downloads/).

* IDE de Scala para Eclipse.
	* **Si tiene instalado el IDE de Eclipse**, puede agregar el complemento del IDE de Scala accediendo a **Help** (Ayuda) -> **Install new software** (Instalar nuevo software). Luego, agregue [http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site) como origen para descargar el complemento de Scala para Eclipse.
	* **Si no tiene instalado el IDE de Eclipse**, puede instalar el IDE de Scala directamente desde [aquí](http://scala-ide.org/download/sdk.html). Para ello, descargue el archivo .zip del vínculo anterior, extráigalo, acceda a la carpeta **/eclipse** y ejecute el archivo **eclipse.exe** de dicha carpeta.
	
	>[AZURE.NOTE] Los pasos de este documento están basados en un escenario en el que se utiliza el IDE de Eclipse con el complemento de Scala instalado.

* SDK de Spark. Puede descargarla [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).

* Instale e(fx)clipse de [https://www.eclipse.org/efxclipse/install.html](https://www.eclipse.org/efxclipse/install.html).


## Instalación del complemento de herramientas de HDInsight para Eclipse

1. Inicie el IDE de Eclipse. Acceda a la ubicación donde descargó y extrajo el archivo .zip, vaya a la carpeta **/eclipse** de dicha ubicación y haga clic en **eclipse.exe**. En la pantalla de inicio, haga clic en **Help** (Ayuda) y, después, en **Install New Software** (Instalar nuevo software).

	![Instalar complemento de HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/install-hdinsight-plugin-1.png)

2. En la siguiente pantalla, escriba **http://dl.microsoft.com/eclipse** en el cuadro de texto **Work with** (Trabajar con) y presione **INTRO**. Seleccione **Azure Toolkit for Java** (Kit de herramientas de Azure para Java), desactive la casilla de **Contact all update sites during install to find required software** (Ponerse en contacto con todos los sitios de actualización durante la instalación para encontrar el software necesario) y, luego, haga clic en **Next** (Siguiente).

	![Instalar complemento de HDInsight](./media/hdinsight-apache-spark-eclipse-tool-plugin/install-hdinsight-plugin-2.png)

3. En el cuadro de diálogo **Install Details** (Detalles de la instalación), revise los componentes que se instalarán y, luego, haga clic en **Next** (Siguiente).

4. En el cuadro de diálogo **Review Licenses** (Revisar licencias), acepte los términos de la licencia y, luego, haga clic en **Finish** (Finalizar).

5. Cuando finalice la instalación, se le pedirá que reinicie Eclipse. Haga clic en el botón **Yes** (Sí) del cuadro de diálogo para reiniciar Eclipse.

## Inicio de sesión en la suscripción de Azure

1. Abra el Explorador de Azure. En el menú **Window** (Ventana) del IDE, haga clic en **Show View** (Mostrar vista) y, después, en **Other** (Otros). En el cuadro de diálogo que se abre, expanda **Azure**, haga clic en **Explorer Azure** (Explorador de Azure) y, luego, en **OK** (Aceptar).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)

2. Haga clic con el botón derecho en el nodo **Azure** del **Explorador de Azure**. Después, haga clic en **Manage Subscriptions** (Administrar suscripciones).

3. En el cuadro de diálogo **Manage Subscriptions** (Administrar suscripciones), haga clic en **Sign in** (Iniciar sesión) y escriba sus credenciales de Azure.

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)

4. Cuando haya iniciado sesión, en el cuadro de diálogo **Manage Subscriptions** (Administrar suscripciones) se enumeran todas las suscripciones de Azure asociadas a las credenciales. Haga clic en el botón **Close** (Cerrar) del cuadro de diálogo.

5. En la pestaña Azure Explorer (Explorador de Azure), expanda **HDInsight** para ver los clústeres Spark en HDInsight de su suscripción.

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)

6. Puede expandir un nodo de nombre de clúster para ver los recursos (por ejemplo, las cuentas de almacenamiento) asociados a este.

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## Configuración de un proyecto Spark en Scala de un clúster Spark en HDInsight

1. En el área de trabajo del IDE de Eclipse, haga clic en **File** (Archivo), en **New** (Nuevo) y, luego, en **Project** (Proyecto).

2. En el asistente **New Project** (Nuevo proyecto), expanda **HDInsight**, seleccione **Spark on HDInsight (Scala)** (Spark en HDInsight [Scala]) y, luego, haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

3. En el cuadro de diálogo **New HDInsight Scala Project** (Nuevo proyecto de Scala en HDInsight), escriba o seleccione valores tal y como se muestra en la imagen siguiente. Después, haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

	* Escriba un nombre para el proyecto.
	* En el cuadro **JRE**, asegúrese de que el valor de **Use an execution environment JRE** (Utilizar un entorno de ejecución JRE) esté establecido en **JavaSE-1.7**.
	* Asegúrese de que el SDK de Spark esté establecido en la ubicación donde descargó el SDK. El vínculo a la ubicación de descarga figura en la sección anterior [Requisitos previos](#prerequisites) de este tema. También puede descargar el SDK desde el vínculo de este cuadro de diálogo, tal y como se muestra en la imagen anterior.

4. En el siguiente cuadro de diálogo, haga clic en la pestaña **Libraries** (Bibliotecas) y, luego, haga doble clic en **JRE System Library [JavaSE-1.7]**.

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)

5. En el cuadro de diálogo **Edit Library** (Editar biblioteca), asegúrese de que el valor de **Execution Environment** (Entorno de ejecución) esté establecido en **JavaSE-1.7(jdk1.7.0\_79)**. Si no aparece como opción, siga estos pasos:

	1. Seleccione la opción **Alternate JRE** (JRE alternativo) y compruebe si **JavaSE-1.7(jdk1.7.0\_79)** está disponible.
	2. En caso contrario, haga clic en el botón **Installed JREs** (JRE instalados).

		  ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-5.png)

	3. En el cuadro de diálogo **Installed JREs** (JRE instalados), haga clic en **Add** (Agregar).

		  ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-6.png)

	4. En el cuadro de diálogo **JRE Type** (Tipo de JRE), seleccione **Standard VM** (Máquina virtual estándar) y haga clic en **Next** (Siguiente).

		  ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-7.png)

	5. En el cuadro de diálogo **JRE Definition** (Definición de JRE), haga clic en el directorio y, después, acceda hasta la ubicación de instalación de JDK 7. Seleccione la carpeta raíz de **jdk1.7.0\_79**.

		  ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-8.png)

	6. Haga clic en **Finalizar** En el cuadro de diálogo **Installed JREs** (JRE instalados), seleccione el JRE que se acaba de agregar y, luego, haga clic en **OK** (Aceptar).

		   ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-9.png)

	7. El JRE recién agregado debería aparecer en **Execution Environment** (Entorno de ejecución). Haga clic en **Finalizar**

	  	   ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-10.png)

6. En la pestaña **Libraries** (Bibliotecas), haga doble clic en **Scala Library Container[2.11.8]**. En el cuadro de diálogo **Edit Library** (Editar biblioteca), seleccione **Fixed Scala Library container:2.10.6**.

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-11.png)

	Haga clic en **Finish** (Finalizar) hasta salir del cuadro de diálogo de configuración del proyecto.

## Creación de una aplicación Spark en Scala para un clúster Spark en HDInsight

1. En el IDE de Eclipse que ya está abierto, desde el **Explorador de paquetes**, expanda el proyecto que creó anteriormente, haga clic con el botón derecho en **src**, seleccione **New** (Nuevo) y, después, haga clic en **Other** (Otros).

2. En el cuadro de diálogo **Select a wizard** (Seleccionar un asistente), expanda **Scala Wizards** (Asistentes de Scala), haga clic en **Scala Object** (Objeto de Scala) y, luego, en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)

3. En el cuadro de diálogo **Create New File** (Crear nuevo archivo), escriba un nombre para el objeto y, luego, haga clic en **Finish** (Finalizar).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)

4. Pegue el código siguiente en el editor de texto.

		import org.apache.spark.SparkConf
		import org.apache.spark.SparkContext
	
		object MyClusterApp{
		  def main (arg: Array[String]): Unit = {
		    val conf = new SparkConf().setAppName("MyClusterApp")
		    val sc = new SparkContext(conf)
		
		    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		    //find the rows which have only one digit in the 7th column in the CSV
		    val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
		
		    rdd1.saveAsTextFile("wasb:///HVACOut")
		  }		
		}


5. Ejecute la aplicación en un clúster de HDInsight Spark.

	1. En el **Explorador de paquetes**, haga clic con el botón derecho en el nombre del proyecto y, luego, seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

	2. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores.

		* Para **Cluster Name** (Nombre del clúster), seleccione el clúster Spark en HDInsight en el que quiere ejecutar la aplicación.

		* Tiene que seleccionar un artefacto del proyecto Eclipse o uno del disco duro.

		* En el cuadro de texto **Main class name** (Nombre de la clase principal), escriba el nombre del objeto que especificó en el código (consulte la imagen siguiente).

			![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)

		* Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.

		* Haga clic en **Enviar**.

	3. La pestaña **Spark Submission** (Envío de Spark) debería empezar a mostrar el progreso. También puede detener la aplicación haciendo clic en el botón rojo de la ventana Spark Submission (Envío de Spark). También puede ver los registros de la ejecución de esta aplicación concreta haciendo clic en el icono de globo (que se indica con el cuadro azul en la imagen).

        ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

    En la siguiente sección, obtendrá información sobre cómo acceder a la salida de trabajo con el complemento de HDInsight para Eclipse.


## Acceso y administración de clústeres Spark en HDInsight mediante el complemento de HDInsight para Eclipse

Puede realizar una serie de operaciones con el complemento de HDInsight.

### Acceso al contenedor de almacenamiento para el clúster

1. En el Explorador de Azure, expanda el nodo raíz **HDInsight** para ver una lista de los clústeres Spark en HDInsight disponibles.

3. Expanda el nombre del clúster para ver la cuenta de almacenamiento y el contenedor de almacenamiento predeterminado para el clúster.

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)

4. Haga clic en el nombre del contenedor de almacenamiento asociado con el clúster. En el panel derecho, debería ver una carpeta llamada "**HVACOut**". Haga doble clic para abrir la carpeta y verá archivos **part-***. Abra uno de esos archivos para ver el resultado de la aplicación.

### Acceso al servidor de historial de Spark

1. En el **Explorador de Azure**, haga clic con el botón derecho en el nombre del clúster Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

2. En el panel del Servidor de historial de Spark, puede buscar la aplicación que acaba de terminar de ejecutar con la utilización del nombre de aplicación. En el código anterior, estableció el nombre de aplicación con `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark era **MyClusterApp**.

### Inicio del portal de Ambari

En el **Explorador de Azure**, haga clic con el botón derecho en el nombre del clúster Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir portal de administración de clústeres [Ambari]). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

### Administración de suscripciones de Azure

De forma predeterminada, el complemento HDInsight enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones para las que desea tener acceso al clúster. En el **Explorador de Azure**, haga clic con el botón derecho en el nodo raíz **Azure** y, luego, haga clic en **Manage Subscriptions** (Administrar suscripciones). En el cuadro de diálogo, desactive las casillas de la suscripción a las que no quiera obtener acceso y haga clic en **Close** (Cerrar). También puede hacer clic en **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.


## Ejecución local de una aplicación Spark en Scala

Puede utilizar el complemento de herramientas de HDInsight para Eclipse si quiere ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente, dichas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### Requisito previo

Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, podría producirse una excepción, tal y como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que se debe a la ausencia del archivo **WinUtils.exe** en el SO Windows. Para solucionar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) en una ubicación como **C:\\WinUtils\\bin**. Después, debe agregar una variable de entorno **HADOOP\_HOME** y establecer el valor de la variable en **C\\WinUtils**.

### Ejecución de una aplicación Spark en Scala local	 

1. Inicie Eclipse y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

	* En el panel izquierdo, seleccione **HDInsight**.
	* En el panel derecho, seleccione **Spark on HDInsight Local Run Sample (Scala)** (Ejemplo de ejecución local de Spark en HDInsight [Scala]).
	* Haga clic en **Siguiente**.

2. Para proporcionar los detalles del proyecto, siga los pasos del 3 a 6 tal y como se muestra en la sección anterior [Configuración de un proyecto Spark en Scala de un clúster Spark en HDInsight](#set-up-a-spark-scala-application-project-for-an-hdinsight-spark cluster).

3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta **src** que puede ejecutar localmente en el equipo.

	![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)

4.  Haga clic con el botón derecho en la aplicación **LogQuery**, seleccione **Ejecutar como** y, después, haga clic en **1 Scala Application** (1 aplicación de Scala). Verá un resultado similar al siguiente en la pestaña **Console** (Consola) de la parte inferior.

	![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## Comentarios y problemas conocidos

Actualmente, la visualización de salidas de Spark directamente no se admite y estamos trabajando en ello.

Si tiene sugerencias o comentarios, o si encuentra algún problema al usar esta herramienta, no dude en enviarnos un correo electrónico a la dirección hdivstool@microsoft.com.

## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels available for Jupyter notebook in Spark cluster for HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0706_2016-->