<properties 
	pageTitle="Kernels disponibles para cuadernos de Jupyter con clústeres de HDInsight Spark en Linux | Microsoft Azure" 
	description="Obtenga información sobre los kernels del cuaderno de Jupyter adicionales disponibles con el clúster de HDInsight Spark en Linux." 
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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Kernels disponibles para cuadernos de Jupyter con clústeres Spark en HDInsight (Linux)

El clúster Apache Spark en HDInsight (Linux) incluye cuadernos de Jupyter que puede usar para probar las aplicaciones. De forma predeterminada el cuaderno de Jupyter incorpora un kernel **Python2**. Los clústeres de HDInsight Spark proporcionan dos kernels adicionales que puede utilizar con el cuaderno de Jupyter. Esto son:

1. **Spark** (para aplicaciones escritas con Scala)
2. **PySpark** (para aplicaciones escritas con Python)

En este artículo, obtendrá información sobre cómo usar estos kernels y cuáles son los beneficios que obtiene de uso.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## ¿Cómo puedo usar los kernels? 

1. Desde el [Portal de vista previa de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha anclado al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo cuaderno con los kernels nuevos. Haga clic en **New** (Nuevo) y, luego, en **Pyspark** o **Spark**. Debe utilizar el kernel de Spark para aplicaciones de Scala y kernel PySpark para aplicaciones de Python.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Crear un nuevo cuaderno de Jupyter")

3. Esto debería abrir un nuevo cuaderno con el kernel seleccionado.

## ¿Por qué debo usar los kernels nuevos?

Utilizar los kernels nuevos aporta un par de beneficios.

1. Con el kernel predeterminado **Python2**, debe establecer los contextos Spark, SQL o Hive antes de empezar a trabajar con la aplicación que está desarrollando. Si utiliza los kernels nuevos (**Spark** o **PySpark**), estos contextos están disponibles de forma predeterminada. Estos contextos son:

	* **sc**: para el contexto Spark
	* **sqlContext**: para el contexto SQL
	* **hiveContext**: para el contexto Hive


	Por tanto, no tiene que ejecutar instrucciones como la siguiente para definir los contextos:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	En su lugar, puede utilizar directamente los contextos preestablecidos en la aplicación.
	
2. Puede usar directamente las instrucciones mágicas **%sql** y **%hive** para usar consultas SQL o Hive, respectivamente. Por tanto, algo como esto funcionaría de forma rápida y directa, sin ninguna instrucción de código inicial.

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## Consideraciones al utilizar los kernels nuevos

Sea cual sea el kernel usado (Python2, PySpark o Spark), dejar los cuadernos en ejecución consumirá los recursos del clúster. Con el cuaderno de Python2, como crea los contextos de manera explícita, también puede terminar los contextos al salir de la aplicación.

Sin embargo, con los kernels PySpark y Spark, como los contextos están preestablecidos, tampoco puede cerrar el contexto de manera explícita. Por tanto,si solo sale del cuaderno, es posible que el contexto continúe en ejecución, de manera que utilizará los recursos del clúster. Una buena práctica con los kernels PySpark y Spark sería utilizar la opción **Cerrar y detener** del menú **Archivo** del cuaderno. Esto termina el contexto y luego sale del cuaderno.


## Estos son algunos ejemplos:

Al abrir un cuaderno de Jupyter, verá dos carpetas disponibles en el nivel raíz.

* La carpeta **Python** tiene cuadernos de ejemplo que usan el kernel **Python2** predeterminado.
* La carpeta **Scala** tiene cuadernos de ejemplo que usan el kernel **Spark** nuevo.

Puede abrir el mismo cuaderno (por ejemplo, **LÉAME PRIMERO: aprenda los conceptos básicos de Spark en HDInsight**) de las dos carpetas para ver cómo el cuaderno Python2 siempre empieza con la definición de los contextos necesarios, mientras que el cuaderno Spark solo usa los contextos preestablecidos.

## Comentarios

Los kernels nuevos se encuentran en una fase muy incipiente y evolucionarán con el tiempo. También podría significar que las API podrían cambiar a medida que estos kernels maduran. Agradecemos cualquier comentario que tenga al utilizar estos nuevos kernels. Esto será muy útil para dar forma a la versión final de estos kernels. Puede dejar sus comentarios la sección **Comentarios** al final de este artículo.


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

### Extensiones

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0128_2016-->