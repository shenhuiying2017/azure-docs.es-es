 <properties
	pageTitle="Creación de aplicaciones Spark en Scala con el complemento de HDInsight para IntelliJ IDEA | Microsoft Azure"
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
	ms.date="07/06/2016"
	ms.author="nitinme"/>


# Uso del complemento Herramientas de HDInsight para IntelliJ IDEA para crear aplicaciones Spark para clúster HDInsight Spark Linux

En este artículo se proporcionan instrucciones paso a paso sobre cómo desarrollar aplicaciones Spark escritas en Scala y enviarlas a un clúster de HDInsight Spark mediante el complemento de HDInsight para IntelliJ IDEA. Puede usar el complemento de varias maneras diferentes:

* Para desarrollar y enviar una aplicación Spark en Scala en un clúster de HDInsight Spark
* Para acceder a los recursos del clúster de Azure HDInsight Spark
* Para desarrollar y ejecutar localmente una aplicación Spark en Scala

También puede seguir un vídeo [aquí](https://mix.office.com/watch/1nqkqjt5xonza) para comenzar.

>[AZURE.IMPORTANT] Esta herramienta se puede utilizar para crear y enviar solicitudes solo para un clúster de HDInsight Spark en Linux.


##Requisitos previos

* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. En este artículo se usa la versión 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).

## Instalación del complemento Herramientas de HDInsight para IntelliJ IDEA

El complemento de herramientas de HDInsight para IntelliJ IDEA está disponible como parte del kit de herramientas de Azure para IntelliJ. Para obtener instrucciones sobre cómo instalar el kit de herramientas de Azure, consulte [Instalación del kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).

## Ejecución de una aplicación Spark en Scala en un clúster de HDInsight Spark

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* En el panel izquierdo, seleccione **HDInsight**.
	* En el panel derecho, seleccione **Spark on HDInsight (Scala)** (Spark en HDInsight [Scala]).
	* Haga clic en **Siguiente**.

2. En la siguiente ventana, proporcione los detalles del proyecto.

	* Proporcione un nombre de proyecto y la ubicación del proyecto.
	* En **Project SDK** (SDK de proyecto), asegúrese de proporcionar una versión de Java superior a la 7.
	* En **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear), haga clic en **Download** (Descargar) y luego seleccione la versión de Scala que desea usar. **Asegúrese de no usar la versión 2.11.x**. En este ejemplo se usa la versión **2.10.6**.

		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para **Spark SDK** (SDK de Spark), descargue y use el SDK desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). También puede omitir esto y utilizar el [repositorio de Spark Maven](http://mvnrepository.com/search?q=spark) en su lugar. Sin embargo, asegúrese de que tiene el repositorio de Maven correcto instalado para desarrollar sus aplicaciones de Spark. Por ejemplo, debe asegurarse de que tiene la parte Spark Streaming instalada si está utilizando Spark Streaming. Asimismo, asegúrese de que utiliza el repositorio marcado como Scala 2.10 y no el repositorio marcado como Scala 2.11.

		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* Haga clic en **Finalizar**

3. El proyecto Spark creará automáticamente un artefacto. Para ver el artefacto, siga estos pasos.

	1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
	2. En el cuadro de diálogo **Project Structure**, haga clic en **Artifacts** (Artefactos) para ver el artefacto predeterminado que se crea.

		![Crear archivo JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)

	También puede crear su propio artefacto haciendo clic en el icono **+** (resaltado en la imagen anterior).

4. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Project** (Proyecto). Si **Project SDK** (SDK de proyecto) está establecido en 1.8, asegúrese de que **Project language level** (Nivel de lenguaje del proyecto) esté establecido en **7 - Diamonds, ARM, multi-catch, etc**.

	![Establecer el nivel de lenguaje del proyecto](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)

5. Agregue el código fuente de aplicación.

	1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en **src**, seleccione **New** (Nueva) y haga clic en **Scala class** (Clase de Scala).

		![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, seleccione **Object** (Objeto) en **Kind** (Variante) y haga clic en **OK** (Aceptar).

		![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. En el archivo **MyClusterApp.scala**, pegue el siguiente código. Este código lee los datos de HVAC.csv (disponible en todos los clústeres de Spark de HDInsight), recupera las filas que solo tienen un dígito en la séptima columna del CSV y escribe el resultado en **/HVACOut** bajo el contenedor de almacenamiento predeterminado para el clúster.


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

	1. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en el nombre de proyecto y luego seleccione **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).

		![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. Se le pedirá que escriba las credenciales de la suscripción de Azure. En el cuadro de diálogo **Spark Submission** (Envío de Spark), proporcione los siguientes valores.

		* Para **Spark clusters (Linux only)** (Clústeres de Spark [solo Linux]), seleccione el clúster de Spark de HDInsight en el que desea ejecutar la aplicación.

		* Necesita seleccionar un artefacto del proyecto IntelliJ o uno del disco duro.

		* En el cuadro de texto **Main class name** (Nombre de clase principal), haga clic en los puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), seleccione la clase principal del código fuente de aplicación y luego haga clic en **OK** (Aceptar).

			![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* Dado que el código de aplicación de este ejemplo no requiere ningún argumento de línea de comandos ni archivos o JAR de referencia, puede dejar vacíos los demás cuadros de texto.

		* Después de proporcionar todas las entradas, el cuadro de diálogo debería parecerse al siguiente.

			![Enviar aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* Haga clic en **Enviar**.

	3. La pestaña **Spark Submission** (Envío de Spark) de la parte inferior de la ventana debe empezar a mostrar el progreso. También puede detener la aplicación haciendo clic en el botón rojo en la ventana "Spark Submission" (Envío de Spark).

        ![Resultado de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    En la siguiente sección, obtendrá información sobre cómo acceder a la salida de trabajo con el complemento de HDInsight para IntelliJ IDEA.


## Acceso y administración de clústeres de HDInsight Spark mediante el complemento de HDInsight para IntelliJ

Puede realizar una serie de operaciones con el complemento de HDInsight.

### Acceso al contenedor de almacenamiento para el clúster

1. En el menú **View** (Ver), seleccione **Tool Windows** (Ventanas de herramientas) y luego haga clic en **HDInsight Explorer** (Explorador de HDInsight). Si se le solicita, introduzca las credenciales para acceder a su suscripción de Azure.

2. Expanda el nodo raíz **HDInsight** para ver una lista de los clústeres de Spark de HDInsight disponibles.

3. Expanda el nombre del clúster para ver la cuenta de almacenamiento y el contenedor de almacenamiento predeterminado para el clúster.

	![Acceder a almacenamiento de clúster](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. Haga clic en el nombre del contenedor de almacenamiento asociado con el clúster. En el panel derecho, debería ver una carpeta llamada "**HVACOut**". Haga doble clic para abrir la carpeta y verá archivos **part-***. Abra uno de esos archivos para ver el resultado de la aplicación.

### Acceso a la vista de trabajo directamente en el complemento

1. Desde **HDInsight Explorer** (Explorador de HDInsight), expanda el nombre del clúster de Spark y haga clic en **Jobs** (Trabajos).

2. En el panel derecho, la pestaña **Spark Job View** (Vista de trabajos de Spark) muestra todas las aplicaciones que se ejecutaron en el clúster. Haga clic en el nombre de la aplicación para la que desea ver más detalles.

	![Acceso a la vista de trabajos](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Los cuadros **Error Message** (Mensaje de error), **Job Output** (Resultado del trabajo), **Livy Job Logs** (Registros de trabajos de Livy) y **Spark Driver Logs** (Registros de controlador de Spark) se rellenan según la aplicación seleccionada.

4. También puede abrir **Spark History UI** (IU de historial de Spark) y **YARN UI** (IU de YARN) (en el nivel de aplicación) si hace clic en los botones correspondientes de la parte superior de la pantalla.

### Acceso al servidor de historial de Spark

1. En **HDInsight Explorer** (Explorador de HDInsight), haga clic con el botón derecho en el nombre de clúster de Spark y seleccione **Open Spark History UI** (Abrir IU de historial de Spark). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

2. En el panel del Servidor de historial de Spark, puede buscar la aplicación que acaba de terminar de ejecutar con la utilización del nombre de aplicación. En el código anterior, estableció el nombre de aplicación con `val conf = new SparkConf().setAppName("MyClusterApp")`. Por lo tanto, el nombre de aplicación Spark era **MyClusterApp**.

### Inicio del portal de Ambari

En **HDInsight Explorer** (Explorador de HDInsight), haga clic con el botón derecho en el nombre de clúster de Spark y seleccione **Open Cluster Management Portal (Ambari)** (Abrir portal de administración de clústeres [Ambari]). Cuando se le pida, escriba las credenciales de administrador para el clúster. Debe haberlas especificado al aprovisionar el clúster.

### Administración de suscripciones de Azure

De forma predeterminada, el complemento HDInsight enumera los clústeres de Spark de todas las suscripciones de Azure. Si es necesario, puede especificar las suscripciones para las que desea tener acceso al clúster. En **HDInsight Explorer** (Explorador de HDInsight), haga clic con el botón derecho en el nodo raíz de **HDInsight** y luego haga clic en **Manage Subscriptions** (Administrar suscripciones). En el cuadro de diálogo, desactive las casillas de la suscripción a las que no quiera obtener acceso y haga clic en **Close** (Cerrar). También puede hacer clic en **Sign Out** (Cerrar sesión) si desea cerrar sesión en su suscripción de Azure.


## Ejecución local de una aplicación Spark en Scala

Puede utilizar el complemento Herramientas de HDInsight para IntelliJ IDEA para ejecutar aplicaciones Spark en Scala localmente en su estación de trabajo. Normalmente, dichas aplicaciones no necesitan tener acceso a los recursos de clúster, como el contenedor de almacenamiento, y se pueden ejecutar y probar de forma local.

### Requisito previo

Mientras se ejecuta la aplicación Spark en Scala local en un equipo Windows, puede obtener una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), que se produce por la ausencia del archivo WinUtils.exe en Windows. Para solucionar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) en una ubicación como **C:\\WinUtils\\bin**. Después, debe agregar una variable de entorno **HADOOP\_HOME** y establecer el valor de la variable en **C\\WinUtils**.

### Ejecución de una aplicación Spark en Scala local	 

1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next** (Siguiente).

	![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* En el panel izquierdo, seleccione **HDInsight**.
	* En el panel derecho, seleccione **Spark on HDInsight Local Run Sample (Scala)** (Ejemplo de ejecución local de Spark en HDInsight [Scala]).
	* Haga clic en **Siguiente**.

2. En la siguiente ventana, proporcione los detalles del proyecto.

	* Proporcione un nombre de proyecto y la ubicación del proyecto.
	* En **Project SDK** (SDK de proyecto), asegúrese de proporcionar una versión de Java superior a la 7.
	* En **Scala SDK** (SDK de Scala), haga clic en **Create** (Crear), haga clic en **Download** (Descargar) y luego seleccione la versión de Scala que desea usar. **Asegúrese de no usar la versión 2.11.x**. En este ejemplo se usa la versión **2.10.6**.

		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* Para **Spark SDK** (SDK de Spark), descargue y use el SDK desde [aquí](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). También puede omitir esto y utilizar el [repositorio de Spark Maven](http://mvnrepository.com/search?q=spark) en su lugar. Sin embargo, asegúrese de que tiene el repositorio de Maven correcto instalado para desarrollar sus aplicaciones de Spark. Por ejemplo, debe asegurarse de que tiene la parte Spark Streaming instalada si está utilizando Spark Streaming. Asimismo, asegúrese de que utiliza el repositorio marcado como Scala 2.10 y no el repositorio marcado como Scala 2.11.

		![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)

	* Haga clic en **Finalizar**

3. La plantilla agrega un código de ejemplo (**LogQuery**) en la carpeta **src** que puede ejecutar localmente en el equipo.

	![Aplicación de Scala local](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

4.  Haga clic con el botón derecho en la aplicación **LogQuery** y en **Run LogQuery** (Ejecutar LogQuery). Verá un resultado similar al siguiente en la pestaña **Run** (Ejecutar) en la parte inferior.

	![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## Conversión de las aplicaciones IntelliJ IDEA existentes para usar el complemento de la herramienta de HDInsight

También puede convertir las aplicaciones existentes Spark en Scala creadas en IntelliJ IDEA para que sean compatibles con el complemento de la herramienta de HDInsight. Esto le permitirá utilizar la herramienta para enviar las aplicaciones a un clúster de HDInsight Spark. Para ello, puede seguir estos pasos:

1. Para una aplicación Spark en Scala existente creada con IntelliJ IDEA, abra el archivo .iml asociado.
2. En el nivel raíz, verá un elemento de **module** similar al siguiente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. Edite el elemento para agregar `UniqueKey="HDInsightTool"` de forma que el elemento de **module** sea similar a lo siguiente:

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. Guarde los cambios. La aplicación ahora debe ser compatible con el complemento de la herramienta de HDInsight. Puede comprobarlo si hace clic con el botón derecho en el nombre de proyecto en el Explorador de proyectos. El menú emergente ahora debería tener la opción **Submit Spark Application to HDInsight** (Enviar aplicación Spark a HDInsight).


## Solución de problemas

### Error "Please use a larger heap size" (Utilice un tamaño de montón mayor) en la ejecución local

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

Esto se debe a que el tamaño del montón no es lo suficientemente grande como para que se ejecute el Spark, ya que requiere 471 MB como mínimo (encontrará más información en [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)). Una solución sencilla es utilizar un SDK de Java de 64 bits. También puede cambiar la configuración de JVM en IntelliJ agregando las siguientes opciones:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Resultado de la ejecución local de la aplicación Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## Comentarios y problemas conocidos

Actualmente, la visualización de salidas de Spark directamente no se admite y estamos trabajando en ello.

Si tiene sugerencias o comentarios, o si encuentra algún problema al usar esta herramienta, no dude en enviarnos un correo electrónico a hdivstool a microsoft punto com.

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

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Uso del complemento de herramientas de HDInsight para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels available for Jupyter notebook in Spark cluster for HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0706_2016-->