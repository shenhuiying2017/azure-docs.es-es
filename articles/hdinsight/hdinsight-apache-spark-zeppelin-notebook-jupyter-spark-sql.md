<properties 
	pageTitle="Aprovisionamiento de un clúster Spark en HDInsight y uso de Spark SQL desde Zeppelin y Jupyter para análisis interactivos | Microsoft Azure" 
	description="Instrucciones paso a paso sobre cómo aprovisionar rápidamente un clúster Spark Apache en HDInsight y luego usar Spark SQL desde cuadernos de Zeppelin y Jupyter para ejecutar consultas interactivas." 
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
	ms.date="08/07/2015" 
	ms.author="nitinme"/>


# Inicio rápido: aprovisionamiento de un clúster Spark en HDInsight y ejecución de consultas interactivas mediante Spark SQL

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Aprovisionamiento de un clúster Spark en HDInsight y ejecución de consultas interactivas mediante Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1.md)

Obtenga información sobre cómo aprovisionar un clúster Apache Spark en HDInsight con la opción Creación rápida y después usar cuadernos de [Zeppelin](https://zeppelin.incubator.apache.org) y [Jupyter](https://jupyter.org) basados en web para ejecutar consultas interactivas de Spark SQL en el clúster Spark.


   ![Introducción al uso de Apache Spark en HDInsight](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.GetStartedFlow.Spark.png "Tutorial de introducción al uso de Apache Spark en HDInsight. Pasos que se muestran: crear una cuenta de almacenamiento. aprovisionar un clúster; ejecutar instrucciones Spark SQL")

**Requisitos previos:**

Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="provision"></a>Aprovisionamiento de un clúster Spark de HDInsight

En esta sección, se aprovisiona un clúster de HDInsight versión 3.2, que se basa en la versión 1.3.1 de Spark. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md).

>[AZURE.NOTE]En este artículo, se proporcionan pasos para crear un clúster Apache Spark en HDInsight con los valores de configuración básica. Si quiere información sobre otras opciones de configuración del clúster (como usar almacenamiento adicional, una red virtual de Azure o una tienda de metadatos para Hive), consulte [Aprovisionamiento de clústeres Apache Spark en HDInsight mediante opciones personalizadas](hdinsight-apache-spark-provision-clusters.md).


**Para aprovisionar un clúster Spark**

1. Inicie sesión en el [Portal de vista previa de Azure](https://ms.portal.azure.com/). 

2. Haga clic en **NUEVO**, haga clic en **Análisis de datos** y, a continuación, haga clic en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba un **Nombre de clúster**, seleccione **Hadoop** para el **Tipo de clúster** y, en el menú desplegable **Sistema operativo de clúster**, seleccione **Windows Server 2012 R2 Datacenter**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.

	![Especifique el tipo y el nombre del clúster](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y, a continuación, seleccionar en el que desea crear el clúster. También puede hacer clic en **Crear nuevo** y, a continuación, escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE]Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y, a continuación, especifique un **Nombre de usuario de inicio de sesión de clúster** y una **Contraseña de inicio de sesión de clúster**. Si desea habilitar Escritorio remoto en el nodo de clúster, para **Habilitar Escritorio remoto**, haga clic en **Sí** y, a continuación, especifique los valores necesarios. Este tutorial no requiere Escritorio remoto, por lo que puede omitir esto. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o cree uno nuevo. Cuando aprovisiona un clúster de Hadoop en HDInsight, especifica una cuenta de Almacenamiento de Azure. Se designa un contenedor de almacenamiento de blobs desde esa cuenta como el sistema de archivos predeterminado, como en el sistema de archivos distribuido de Hadoop (HDFS). De forma predeterminada, el clúster de HDInsight se aprovisiona en el mismo centro de datos que la cuenta de almacenamiento que especifica. Para obtener más información, vea [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]

	![Hoja Origen de datos](./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establezca esto en **Tecla de acceso** si desea especificar el **Nombre de almacenamiento** y la **Tecla de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento / Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para examinar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.

		> [AZURE.IMPORTANT]Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos del clúster y predeterminado deben encontrarse en la misma región.
	
	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Niveles de precios de nodo** para mostrar información acerca de los nodos que se crearán para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")

	Haga clic en **Seleccionar** para guardar la configuración de los precios del nodo.

9. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y, a continuación, haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el aprovisionamiento.

	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/provisioned.png) |

	> [AZURE.NOTE]El clúster tardará algún tiempo en crearse, normalmente unos 15 minutos. Use el icono del panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

10. Una vez que termine el aprovisionamiento, haga clic en el icono del clúster de Spark desde el panel de inicio para iniciar la hoja del clúster.


##<a name="zeppelin"></a>Ejecución de consultas Spark SQL interactivas con un cuaderno de Zeppelin

Después de haber aprovisionado un clúster, puede usar un cuaderno de Zeppelin basado en web para ejecutar consultas interactivas de Spark SQL en el clúster Spark de HDInsight. En esta sección, se usará un archivo de datos de ejemplo (hvac.csv) disponible de manera predeterminada en el clúster para ejecutar algunas consultas Spark SQL interactivas.

>[AZURE.NOTE]El cuaderno que crea cuando sigue las instrucciones más abajo también está disponible de manera predeterminada en el clúster. Después de iniciar Zeppelin, encontrará el cuaderno con el nombre **Zeppelin HVAC tutorial**.

1. Inicie el cuaderno de Zeppelin. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, a continuación, desde la hoja **Panel de clúster**, haga clic en **Equipo portátil ligero Zeppelin**. Cuando se le pida, escriba las credenciales de administrador para el clúster. Siga las instrucciones de la página que se abre para iniciar el cuaderno.

2. Cree un nuevo cuaderno. En el panel de encabezado, haga clic en **Notebook** y luego en **Create New Note** (Crear nueva nota).

	![Crear un nuevo cuaderno de Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.CreateNewNote.png "Crear un nuevo cuaderno de Zeppelin")

	En la misma página, en el encabezado **Notebook**, debería ver un nuevo cuaderno con un nombre que empiece por **Note XXXXXXXXX** (Nota XXXXXXXXX). Haga clic en el nuevo cuaderno.

3. En la página web del nuevo cuaderno, haga clic en el encabezado y cambie el nombre del cuaderno si quiere. Presione ENTRAR para guardar el cambio de nombre. Además, asegúrese de que el encabezado del cuaderno muestre el estado **Connected** (Conectado) en la esquina superior derecha.

	![Estado del cuaderno de Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.NewNote.Connected.png "Estado del cuaderno de Zeppelin")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando aprovisiona un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\SensorSampleData\\hvac**.

	En el párrafo vacío que se crea de manera predeterminada en el nuevo cuaderno, pegue el siguiente fragmento.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Presione **MAYÚS + ENTRAR** o haga clic en el botón **Reproducir** del párrafo para ejecutar el fragmento. El estado en la esquina derecha del párrafo debería avanzar de READY (Listo), PENDING (Pendiente) o RUNNING (En ejecución) a FINISHED (Finalizado). El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla es similar a la siguiente:

	![Crear una tabla temporal de datos sin procesar](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.LoadDataIntoTable.png "Crear una tabla temporal de datos sin procesar")

	También puede proporcionar un título para cada párrafo. En la esquina derecha, haga clic en el icono **Settings** (Configuración) y luego haga clic en **Show title** (Mostrar título).

5. Ahora puede ejecutar instrucciones Spark SQL en la tabla **hvac**. Pegue la siguiente consulta en un nuevo párrafo. La consulta recupera el identificador del edificio y la diferencia entre la temperatura objetivo y la real para cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	La instrucción **%sql** al principio indica al cuaderno que use el intérprete Spark SQL. Puede consultar los intérpretes definidos en la pestaña **Interpreter** (Intérprete) en el encabezado del cuaderno.

	En la captura de pantalla siguiente se muestra el resultado.

	![Ejecutar una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery1.png "Ejecutar una instrucción Spark SQL mediante el cuaderno")

	 Haga clic en las opciones de presentación (resaltadas con el rectángulo) para alternar entre diferentes representaciones del mismo resultado. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. La captura de pantalla anterior usa **buildingID** como clave y la media de **temp\_diff** como valor.

	
6. También puede ejecutar instrucciones Spark SQL usando variables en la consulta. El siguiente fragmento muestra cómo definir una variable, **Temp**, en la consulta con los valores posibles con los que quiere hacer la consulta. Cuando ejecuta la consulta por primera vez, se rellena una lista desplegable automáticamente con los valores especificados para la variable.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Pegue este fragmento en un nuevo párrafo y presione **MAYÚS + ENTRAR**. En la captura de pantalla siguiente se muestra el resultado.

	![Ejecutar una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.SparkSQLQuery2.png "Ejecutar una instrucción Spark SQL mediante el cuaderno")

	Para las consultas posteriores, puede seleccionar un nuevo valor en la lista desplegable y después volver a ejecutar la consulta. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. La captura de pantalla anterior usa **buildingID** como clave, la media de **temp\_diff** como valor y **targettemp** como grupo.

7. Reinicie el intérprete Spark SQL para salir de la aplicación. Haga clic en la pestaña **Interpreter** (Intérprete) en la parte superior y, para el intérprete Spark, haga clic en **Restart** (Reiniciar).

	![Reiniciar el intérprete Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Zeppelin.Restart.Interpreter.png "Reiniciar el intérprete Zeppelin")

##<a name="jupyter"></a>Ejecución de consultas Spark SQL mediante un cuaderno de Jupyter

En esta sección, utilice un cuaderno de Jupyter para ejecutar consultas Spark SQL en un clúster Spark.

>[AZURE.NOTE]El cuaderno que crea cuando sigue las instrucciones más abajo también está disponible de manera predeterminada en el clúster. Después de iniciar Jupyter, encontrará el cuaderno con el nombre **HVACTutorial.ipynb**.

1. Inicie el cuaderno de Jupyter. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, a continuación, desde la hoja **Panel de clúster**, haga clic en **Equipo portátil ligero Jupyter**. Cuando se le pida, escriba las credenciales de administrador del clúster Spark.

2. Cree un nuevo cuaderno. Haga clic en **New** (Nuevo) y, a continuación, en **Python 2**.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.CreateNotebook.png "Crear un nuevo cuaderno de Jupyter")

3. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

	![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Note.Jupyter.Notebook.Name.png "Proporcionar un nombre para el cuaderno")

4. Importe los módulos requeridos y cree los contextos Spark y SQL. Pegue el siguiente fragmento en una celda vacía y presione **MAYÚS + ENTRAR**.

		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *

		# Create Spark and SQL contexts
		sc = SparkContext('spark://headnodehost:7077', 'pyspark')
		sqlContext = SQLContext(sc)

	Cada vez que se ejecuta un trabajo en Jupyter, el título de la ventana del explorador web mostrará el estado **(Busy)** (Ocupado) junto con el título del cuaderno. También verá un círculo sólido junto al texto **Python 2** en la esquina superior derecha. Una vez completado el trabajo, cambiará a un círculo hueco.

	 ![Estado de un trabajo de cuaderno de Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Job.Status.png "Estado de un trabajo de cuaderno de Jupyter")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando aprovisiona un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\SensorSampleData\\hvac**.

	En una celda vacía, pegue el siguiente fragmento y presione **MAYÚS + ENTRAR**. Este fragmento registra los datos en una tabla temporal llamada **hvac**.


		# Load the data
		hvacText = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerAsTable("hvac")
		
		# Run queries against the table and display the data
		data = sqlContext.sql("select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"")
		data.show()

5. Una vez que el trabajo se completa correctamente, se muestra el resultado siguiente.

		buildingID temp_diff date  
		4          8         6/1/13
		3          2         6/1/13
		7          -10       6/1/13
		12         3         6/1/13
		7          9         6/1/13
		7          5         6/1/13
		3          11        6/1/13
		8          -7        6/1/13
		17         14        6/1/13
		16         -3        6/1/13
		8          -8        6/1/13
		1          -1        6/1/13
		12         11        6/1/13
		3          14        6/1/13
		6          -4        6/1/13
		1          4         6/1/13
		19         4         6/1/13
		19         12        6/1/13
		9          -9        6/1/13
		15         -10       6/1/13

6. Reinicie el kernel para salir de la aplicación. En la barra de menús superior, haga clic en **Kernel**, en **Restart** (Reiniciar) y luego en **Restart** de nuevo cuando se le pida.

	![Reiniciar el kernel de Jupyter](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql/HDI.Spark.Jupyter.Restart.Kernel.png "Reiniciar el kernel de Jupyter")


##<a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)
* [Aprovisionamiento de un clúster Spark de HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools.md)
* [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Streaming con Spark: procesamiento de eventos desde el Centro de eventos de Azure con Apache Spark en HDInsight](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=August15_HO8-->