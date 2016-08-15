<properties
	pageTitle="Ciencia de datos mediante Scala con Spark de Azure | Microsoft Azure"
	description="En este tema se muestra cómo utilizar Scala para realizar tareas supervisadas de aprendizaje automático con la biblioteca de aprendizaje automático escalable de Spark (MLlib) y los paquetes de SparkML en un clúster Spark de HDInsight de Azure."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Ciencia de datos mediante Scala con Spark de Azure

En este tema se muestra cómo utilizar Scala para realizar tareas supervisadas de aprendizaje automático con la biblioteca de aprendizaje automático escalable de Spark (MLlib) y los paquetes de SparkML en un clúster Spark de HDInsight de Azure. Además, explicaremos cuáles son las tareas que constituyen el [proceso de ciencia de datos](http://aka.ms/datascienceprocess): exploración e ingesta de datos, visualización, ingeniería de características, modelado y consumo de modelos. Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente: Abordaremos dos de las tareas supervisadas de aprendizaje automático comunes:

- Problema de regresión: predicción de propinas (en dólares)
- Clasificación binaria: predicción de si se dará propina o no (1/0) en una carrera de taxi

Para llevar a cabo el proceso de modelado hay que realizar entrenamientos y evaluaciones en conjuntos de datos de pruebas con métricas de precisión pertinentes. También mostraremos cómo almacenar estos modelos en el Almacenamiento de blobs de Azure (WASB), además de puntuar y evaluar su rendimiento predictivo. En un tema más avanzado se describe cómo pueden optimizarse los modelos mediante validación cruzada y barrido de hiperparámetros. Los datos que se utilizan son un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York en 2013

[Scala](http://www.scala-lang.org/) es un lenguaje basado en la máquina virtual de Java que integra los conceptos del lenguaje funcional y la programación orientada a objetos. Se trata de un lenguaje escalable apropiado para efectuar el procesamiento distribuido en la nube; además, se ejecuta en los clústeres Spark de Azure.

[Spark](http://spark.apache.org/) es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo distribuido en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. El paquete [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) proporciona un conjunto uniforme de API de alto nivel creadas a partir de tramas de datos que ayudan a los usuarios a crear y ajustar canalizaciones prácticas de aprendizaje automático. [MLlib](http://spark.apache.org/mllib/) es la biblioteca de aprendizaje automático escalable de Spark que ofrece funcionalidades de modelado en este entorno distribuido.

[Spark para HDInsight](../hdinsight/hdinsight-apache-spark-overview.md) es la oferta de Spark de código abierto que hospeda Azure. Asimismo, incluye compatibilidad con los **cuadernos de Scala de Jupyter Notebook** del clúster Spark, que pueden ejecutar consultas interactivas de Spark SQL para transformar, filtrar y visualizar los datos almacenados en blobs de Azure (WASB). Los fragmentos de código de Scala que proporcionan las soluciones y muestran los trazados pertinentes para visualizar los datos se ejecutan en cuadernos de Jupyter Notebook instalados en los clústeres Spark. En estos temas, los pasos de modelado también contienen código que muestra cómo entrenar, evaluar, guardar y usar cada tipo de modelo.

Los pasos de instalación y el código proporcionados en este tutorial son para Spark 1.6 en HDInsight 3.4. Sin embargo, este código y el de los [cuadernos de Scala de Jupyter Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) de este tutorial son genéricos y debería funcionar en cualquier clúster Spark. Los pasos de configuración y administración del clúster pueden ser ligeramente diferentes de los que se muestran aquí si no está usando Spark en HDInsight.

> [AZURE.NOTE] Si quiere leer un tema que muestre cómo utilizar Python en lugar de Scala para completar las tareas de un proceso de ciencia de datos de un extremo a otro, consulte [Información general sobre la ciencia de los datos con Spark en HDInsight de Azure](machine-learning-data-science-spark-overview.md).


## Requisitos previos

1\. Antes de comenzar estos temas, debe tener una suscripción a Azure. Si aún no tiene una, consulte [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtención de una evaluación gratuita de Azure para probar Hadoop en HDInsight).

2\. Necesita un clúster de Spark 1.6 en HDInsight 3.4 para completar este tutorial. Para crear uno, consulte las instrucciones de [Introducción: creación de clústeres de Apache Spark en HDInsight para Linux y ejecución de consultas interactivas mediante Spark SQL (versión preliminar)](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). En el menú **Seleccionar tipo de clúster** se especifica el tipo de clúster y la versión.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Para ver una descripción de los datos de taxis de Nueva York y las instrucciones para ejecutar código desde un cuaderno de Jupyter Notebook en el clúster Spark, consulte las secciones que correspondan de [Información general sobre la ciencia de los datos con Spark en HDInsight de Azure](machine-learning-data-science-spark-overview.md).


## Ejecución del código de Scala desde un cuaderno de Jupyter Notebook del clúster Spark 

Puede iniciar Jupyter Notebook desde el portal de Azure. Busque el clúster de Spark en el panel y haga clic en él para entrar en la página de administración del clúster. Después, haga clic en **Paneles de clúster** -> **Jupyter Notebook** para abrir el cuaderno asociado al clúster Spark.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

También puede ir a ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acceder a los cuadernos de Jupyter Notebook. Simplemente reemplace la parte CLUSTERNAME de esta dirección URL por el nombre de su propio clúster. Necesitará la contraseña de su cuenta de administrador para acceder a los Notebooks.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Seleccione Scala para ver un directorio que contiene algunos ejemplos de cuadernos previamente empaquetados que utilizan la API de PySpark. El **cuaderno de puntuación y modelado de exploración mediante Scala.ipynb** que contiene los ejemplos de código para este conjunto de temas sobre Spark está disponible en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).


Puede cargar el cuaderno directamente desde GitHub en el servidor de Jupyter Notebook del clúster Spark. En la página principal de su instancia de Jupyter, haga clic en el botón **Cargar** de la parte derecha de la pantalla. Se abrirá un explorador de archivos. Pegue aquí la dirección URL de GitHub (contenido sin procesar) del cuaderno de Scala y haga clic en **Abrir**. El cuaderno de Scala está disponible en la siguiente dirección URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## Configuración: contextos preestablecidos, instrucciones mágicas de Spark y bibliotecas

### Contexto preestablecido de Spark y Hive

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


Los kernels de Spark que se proporcionan con los cuadernos de Jupyter Notebook tienen un contexto preestablecido, por lo que no tiene que definir de forma explícita los contextos de Spark o Hive para poder empezar a trabajar con la aplicación que esté desarrollando, ya que están disponibles de forma predeterminada. Estos contextos son:

- sc: para el contexto de Spark
- sqlContext: para el contexto de Hive


### Instrucciones mágicas de Spark

El kernel de Spark proporciona algunas instrucciones "mágicas" predefinidas, que son comandos especiales a los que pueden llamarse con %%. Hay dos comandos de este tipo que se utilizan en estos ejemplos de código.

- **%%local**: especifica que el código de las líneas siguientes se ejecutará localmente. El código debe estar en el lenguaje Scala.
- **%%sql -o <nombre de la variable>**: ejecuta una consulta de Hive en sqlContext. Si se pasa el parámetro -o, el resultado de la consulta se conserva en el contexto %%local de Scala como trama de datos de Spark.

Para obtener más información sobre los kernels de cuadernos de Jupyter Notebook y las “instrucciones mágicas” predefinidas llamadas con %% (por ejemplo, %%local) que proporcionan, consulte [Kernels disponibles para cuadernos de Jupyter con clústeres de Spark en HDInsight basados en Linux en HDInsight (versión preliminar)](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### Importación de bibliotecas

Importe las bibliotecas de Spark, MLlib y otras que necesite con el siguiente código.

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## Ingesta de datos
 
El primer paso del proceso de ciencia de datos consiste en incorporar la información que se va a analizar desde los orígenes o sistemas externos donde residen, a su entorno de exploración y modelado de datos. En este tutorial, vamos a leer una muestra combinada del 0,1 % del archivo de tarifas y carreras de taxi (almacenado como archivo .tsv). El entorno de exploración y modelado de datos es Spark. Esta sección contiene el código para completar una serie de tareas:

- Establecer rutas de acceso a directorios para almacenar datos y modelos
- leer el conjunto de datos de entrada (almacenado como un archivo .tsv)
- Definir un esquema para los datos y limpiarlos
- Crear una trama de datos limpia y almacenarla en la memoria caché
- Registrarla como tabla temporal en el contexto de SQL
- Consultar la tabla e importar los resultados en una trama de datos


### Establecimiento de rutas de directorio para las ubicaciones de almacenamiento de WASB

Spark puede leer y escribir en un blob de Almacenamiento de Azure (también conocido como WASB), por lo que cualquiera de los datos existentes almacenados allí pueden procesarse mediante Spark y volver a almacenarse en WASB.

Para guardar modelos o archivos en WASB, la ruta de acceso debe especificarse correctamente. Se puede hacer referencia al contenedor predeterminado asociado al clúster de Spark con un ruta que comience con: "wasb///". Para hacer referencia a otras ubicaciones, se usa "wasb://".

El siguiente ejemplo de código especifica la ubicación de los datos de entrada que se leerán y la ruta de acceso al blob de Azure que está conectado al clúster Spark donde se guardará el modelo.

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### Importación de datos, creación de conjuntos de datos distribuidos resistentes (RDD) y definición de tramas de datos según esquema 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 8 segundos.


### Consulta de tablas e importación de resultados en una trama de datos 

Consulte la tabla para obtener datos de tarifas, pasajeros y propinas, filtrar la información dañada y no relevante, e imprimir varias filas.

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**SALIDA:**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13\.5| 1\.0| 2\.9| 1\.0
 16\.0| 2\.0| 3\.4| 1\.0
 10\.5| 2\.0| 1\.0| 1\.0


## Visualización y exploración de datos 

Una vez incorporados los datos en Spark, el siguiente paso del proceso de la ciencia de los datos es conocer mejor los datos mediante la exploración y la visualización. En esta sección, examinaremos los datos de taxi mediante consultas SQL y, luego, importamos los resultados en una trama de datos para trazar las variables de destino y las posibles características para inspeccionarlas de manera visual. Con este fin, utilizaremos la funcionalidad de visualización automática de Jupyter.

### Uso de instrucciones mágicas de SQL y locales para trazar datos

De forma predeterminada, el resultado de cualquier fragmento de código que se ejecuta desde un cuaderno de Jupyter Notebook está disponible en el contexto de la sesión que se conserva en los nodos de trabajo. Si quiere guardar un viaje en los nodos de trabajo de cada cálculo y todos los datos que necesita para dichos cálculos están disponibles de forma local en el nodo del servidor de Jupyter (el nodo principal), puede utilizar la instrucción mágica local %% para ejecutar el fragmento de código en el servidor de Jupyter.

- **Instrucciones mágicas de SQL (`%%sql`)** El kernel de Spark para HDInsight admite consultas sencillas de HiveQL en línea en sqlContext. El argumento (-o VARIABLE\_NAME) conserva la salida de la consulta SQL como una trama de datos de Pandas en el servidor de Jupyter. Esto significa que estará disponible en el modo local.
- La **instrucción mágica `%%local`** se utiliza para ejecutar código de forma local en el servidor de Jupyter, que es el nodo principal del clúster de HDInsight. Normalmente, se utilizan juntas las instrucciones mágicas `%%local` y `%%sql` con el parámetro -o. El parámetro -o conservaría la salida de la consulta SQL localmente y luego la instrucción mágica %%local desencadenaría el siguiente conjunto de fragmento de código para ejecutarse localmente en la salida de las consultas SQL que se conserva localmente.

### Consulta de datos mediante SQL
Esta consulta recupera los viajes por importe de la tarifa, número de pasajeros y propina.

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

En el código siguiente, la instrucción mágica local %% crea una trama datos local, sqlResults, que puede utilizarse para trazar información con matplotlib.

> [AZURE.TIP] Esta instrucción mágica local se emplea varias veces en este tutorial. Si la cantidad de datos es elevada, realice un muestreo para crear una trama de datos que pueda caber en la memoria local.

### Trazado de datos

Se pueden trazar datos con código Python cuando la trama de datos esté en el contexto local como trama de datos de Pandas.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 Tras ejecutar el código, el kernel de Spark visualiza automáticamente el resultado de las consultas SQL (HiveQL). Tiene la posibilidad de elegir entre diferentes tipos de visualizaciones:

- Tabla
- Circular
- Línea
- Ámbito
- Barra

Este es el código para trazar los datos:

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**SALIDA:**

![Histograma del importe de la propina](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Importe de las propinas por número de pasajeros](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Importe de las propinas por importe de la tarifa](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## Creación de características y preparación de datos para modelas funciones 

Para utilizar las funciones de modelado de árbol en SparkML y MLlib, el destino y las características se deben preparar mediante diferentes técnicas adecuadas, como discretización, indexación, codificación "one-hot" y vectorización. Estos son los procedimientos que explicamos en esta sección:

- Creación de una nueva característica mediante la **discretización** de horas en cubos de tiempo de tráfico
- **Indexación y codificación "one-hot"** de características categóricas
- **Muestreo y división de conjuntos de datos** en fracciones de entrenamiento y prueba
- **Especificación de variables de entrenamiento y características**, y creación de tramas de datos o RDD de punto etiquetados de entrada para entrenamientos y pruebas mediante indexación o codificación "one-hot"
- **Categorización y vectorización automáticas de características y destinos** con el objetivo de utilizarlas como entradas para los modelos de aprendizaje automático


### Creación de una nueva característica mediante la discretización de horas en cubos de tiempo de tráfico 

Este código muestra cómo crear una nueva característica mediante la discretización de horas en cubos de tiempo de tráfico y, después, cómo almacenar en caché la trama de datos resultante en memoria. Cuando se usan repetidamente conjuntos de datos distribuidos resistentes (RDD) y tramas de datos, el almacenamiento en caché mejora los tiempos de ejecución. Por lo tanto, almacenaremos en caché los RDD y las tramas de datos en varias fases del tutorial.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### Indexación y codificación "one-hot" de características categóricas 

Esta sección muestra cómo indexar o codificar las características categóricas para la entrada en las funciones de modelado. Las funciones de modelado y predicción de MLlib requieren características con datos de entrada categóricos indexados o codificados antes de usarlos.

Dependiendo del modelo, deberá indexarlos o codificarlos de maneras diferentes: Por ejemplo, los modelos de regresión logística y lineal requieren una codificación "uno de n", donde una característica con 3 categorías puede ampliarse a 3 columnas de característica, cada una de las cuales contiene 0 o 1 según la categoría de una observación. MLlib proporciona la función [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para realizar la codificación "one-hot". Este codificador asigna una columna de índices de etiqueta a una columna de vectores binarios, con un solo valor uno como máximo. Esta codificación permite aplicar algoritmos que esperan características con valores numéricos, como la regresión logística, a características categóricas.

Aquí solo transformamos cuatro variables para mostrar los ejemplos, que son cadenas de caracteres. También se pueden indexar otras variables como variables categóricas (por ejemplo, el día de la semana) que se representan mediante valores numéricos.

Para la indexación usamos `StringIndexer()`, y para la codificación "one-hot", funciones `OneHotEncoder()` de MLlib. Este es el código para indexar y codificar características categóricas:


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 4 segundos.



### Muestreo y división de conjuntos de datos en fracciones de entrenamiento y prueba

Este código crea una muestra aleatoria de los datos (aquí se usa el 25 %). Aunque no es necesario para este ejemplo debido al tamaño del conjunto de datos, se muestra cómo realizar la muestra para que sepa cómo hacerlo cuando lo necesite. Cuando las muestras son grandes, esto puede ahorrar mucho tiempo al entrenar modelos. Después, dividimos la muestra en una parte de entrenamiento (75 %) y una parte de pruebas (25 %) para el modelado de clasificación y regresión.

Agregamos un número aleatorio (entre 0 y 1) para llegar a la fila (en una columna llamada "rand") que puede utilizarse para seleccionar subconjuntos de validación cruzada durante el entrenamiento.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 2 segundos.


### Especificación de variables de entrenamiento y características, y creación de tramas de datos o RDD de punto con etiqueta de entrada para entrenamientos y pruebas mediante indexación o codificación "one-hot" 

Esta sección contiene código que muestra cómo indexar datos de texto categóricos como un tipo de datos de punto con etiqueta, y codificarlos para poder usarlos para entrenar y probar la regresión logística de MLlib y otros modelos de clasificación. Los objetos de punto con etiqueta son conjuntos de datos distribuidos resistentes (RDD) con el formato de datos de entrada que necesita la mayoría de los algoritmos de aprendizaje automático de MLlib. Un [punto con etiqueta](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) es un vector local, denso o disperso, asociado con una etiqueta o respuesta.

En este código, especificamos las características y las variables de destino (dependiente) que se utilizarán para los entrenamientos. Además, crearemos tramas de datos o RDD de punto con etiqueta de entrada para entrenamientos y pruebas mediante indexación o codificación "one-hot".


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 4 segundos.


### Categorización y vectorización automáticas de características y destinos con el objetivo de utilizarlas como entradas para los modelos de aprendizaje automático

Categorice correctamente las características y el destino para utilizarlos en las funciones de modelado de árbol de SparkML. El código realiza dos tareas:

1. Crea un destino binario para la clasificación mediante la asignación de un valor de 0 o 1 a cada punto de datos entre 0 y 1 con un valor de umbral de 0,5.
2. Categoriza automáticamente las características. Si el número de valores numéricos distintos de cualquiera de las características es menos que 32, se categoriza la característica.

Este es el código para estas dos tareas.

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## Clasificación binaria: predicción de si se dio propina o no

En esta sección, crearemos tres tipos de modelos de clasificación binaria para predecir si se pagó o no propina:

- Un **modelo de regresión logística** con la función `LogisticRession()` de SparkML
- Un **modelo de clasificación de bosque aleatorio** con la función `RandomForestClassifier()` de SparkML
- Un **modelo de clasificación de árboles impulsados por gradientes** con la función `GradientBoostedTrees()` de MLlib

### Creación de un modelo de regresión logística

En esta sección crearemos modelo de regresión logística con la función `LogisticRession()` de SparkML. El código de generación del modelo de esta sección consta de una serie de pasos:

1. Datos de **entrenamiento del modelo** con un conjunto de parámetros
2. **Evaluación del modelo** en un conjunto de datos de prueba con métricas
3. **Guardado del modelo** en un blob para utilizarse en el futuro
4. **Puntuación del modelo** con los datos de prueba
5. **Trazado de los resultados**: las curvas de ROC

Este es el código para estos procedimientos.

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

Después, cargue, puntúe y guarde los resultados.

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**SALIDA:**

ROC de los datos de prueba = 0,9827381497557599


Use Python en las tramas de datos locales de Pandas para trazar la curva de ROC.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()


**SALIDA:**

![Curva de ROC sobre si se dará propina o no](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### Creación de un modelo de clasificación de bosque aleatorio

En esta sección, crearemos un modelo de clasificación de bosque aleatorio mediante la función `RandomForestClassifier()` de SparkML y evaluaremos el modelo en los datos de prueba.


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**SALIDA:**

ROC de los datos de prueba = 0,9847103571552683


### Creación de un modelo de clasificación de árboles impulsados por gradientes

En esta sección, crearemos un modelo de clasificación de árboles impulsados por gradientes mediante la función `GradientBoostedTrees()` de MLlib y evaluaremos el modelo en los datos de prueba.


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**SALIDA:**

Área bajo la curva de ROC = 0,9846895479241554


## Regresión: predicción del importe de la propina 

En esta sección, creamos dos tipos de modelos de regresión para predecir el importe de la propina:

- Un **modelo de regresión lineal regularizada** con la función `LinearRegression()` de SparkML. Luego, lo guardaremos y lo evaluaremos en los datos de prueba.
- Un **modelo de regresión de árboles impulsados por gradientes** con la función `GBTRegressor() ` de SparkML.


### Creación de un modelo d regresión lineal regularizada

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 13 segundos.


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**SALIDA:**

R-sqr de los datos de prueba = 0,5960320470835743


Después, consultaremos los resultados de prueba como trama de datos y los visualizaremos con autoviz de Jupyter y matplotlib de Python.


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

Este código crea una trama de datos local a partir de los resultados de la consulta y traza los datos. La instrucción mágica `%%local` crea una trama de datos local, `sqlResults`, que puede usarse para trazar la información con matplotlib.

>[AZURE.NOTE] Esta instrucción mágica de Spark se emplea varias veces en este tutorial. Si la cantidad de datos es grande, debe muestrear para crear una trama de datos que pueda caber en la memoria local.

Creación de trazados con matplotlib de Python.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**SALIDA:**

![Importe de la propina: real frente a predicción](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### Creación de un modelo de regresión de árboles impulsados por gradientes

En esta sección, crearemos un modelo de regresión de árboles impulsados por gradientes mediante la función `ML GBTRegressor()` de Spark y evaluaremos el modelo en los datos de prueba.

Los [árboles impulsados por gradientes](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) son conjuntos de árboles de decisión. Los GBT entrenan árboles de decisión de forma iterativa para minimizar una función de pérdida. Los GBT se usan para clasificación y regresión, y pueden controlar características categóricas, no requieren ajustar la escala de las características y pueden capturar errores de alineación e interacciones de las características. También se pueden usar en una configuración de clasificación multiclase.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**SALIDA:**

R-sqr de prueba = 0,7655383534596654



## Utilidades avanzadas de modelado para la optimización

En esta sección se muestra cómo utilizar las utilidades de aprendizaje automático que se emplean frecuentemente para optimizar modelos. En concreto, explicaremos las tres formas que existen de optimizar los modelos de aprendizaje automático mediante barrido de parámetros y validación cruzada:

1\. Dividir los datos en conjuntos de entrenamiento y validación, y optimizar el modelo mediante barrido de hiperparámetros en un conjunto de entrenamiento y mediante evaluación en un conjunto de validación (regresión lineal)

2\. Optimizar el modelo mediante validación cruzada y barrido de hiperparámetros con la función CrossValidator de SparkML (clasificación binaria)

3\. Optimizar modelo usando código personalizado de validación cruzada y barrido de parámetros para usar cualquier función de aprendizaje automático y conjunto de parámetros (regresión lineal)


La **validación cruzada (VC)** es una técnica que evalúa la calidad de la generalización que realizará un modelo entrenado con un conjunto conocido de datos para predecir las características de conjuntos de datos con los que no se haya entrenado. La idea general tras esta técnica es que se entrena un modelo con un conjunto conocido de datos y después se prueba la precisión de sus predicciones con un conjunto de datos independiente. En este caso, se usa una implementación habitual en la que se divide un conjunto de datos en K iteraciones y después se entrena el modelo como round-robin en todas las iteraciones menos una.

La **optimización de los hiperparámetros** es el problema de elegir un conjunto de hiperparámetros para un algoritmo de aprendizaje, normalmente con el fin de optimizar una medida del rendimiento del algoritmo con un conjunto de datos independiente. Los **hiperparámetros** son valores que deben especificarse fuera del procedimiento de entrenamiento del modelo. Las suposiciones que se hagan sobre estos valores pueden afectar a la flexibilidad y la precisión de los modelos. Los árboles de decisión tienen hiperparámetros, como por ejemplo la profundidad que desee y el número de hojas del árbol. Las máquinas de vectores de soporte (SVM) requieren que se establezca un término de penalización para las clasificaciones incorrectas.

Una forma habitual de realizar la optimización de hiperparámetros usada aquí es una búsqueda de cuadrícula o **barrido de parámetros**. Esto consiste en llevar a cabo una búsqueda exhaustiva en los valores de un subconjunto concreto del espacio de hiperparámetros para un algoritmo de aprendizaje. La validación cruzada puede proporcionar una métrica de rendimiento para ordenar los resultados óptimos generados por el algoritmo de búsqueda de cuadrícula. La VC que se usa con el barrido de hiperparámetros ayuda a limitar problemas tales como el sobreajuste del modelo a los datos de aprendizaje, para que el modelo conserve la capacidad de aplicarse al conjunto general de datos del que se extrajeron los datos de aprendizaje.

### Optimización del modelo de regresión lineal mediante barrido de hiperparámetros

En esta sección explicamos cómo dividir los datos en conjuntos de entrenamiento y validación, y optimizar el modelo mediante barrido de hiperparámetros en un conjunto de entrenamiento y mediante evaluación en un conjunto de validación (regresión lineal).

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**SALIDA:**

R-sqr de prueba = 0,6226484708501209


### Optimización del modelo de clasificación binaria mediante barrido de hiperparámetros y validación cruzada

En esta sección mostramos cómo optimizar el modelo de clasificación binaria mediante barrido de hiperparámetros y validación cruzada. Para ello, se emplea la función CrossValidator de SparkML.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 33 segundos.


### Optimización del modelo de regresión lineal mediante código personalizado de validación cruzada y barrido de parámetros

En esta sección explicamos cómo optimizar el modelo usando código personalizado y cómo identificar los mejores parámetros mediante el criterio de mayor precisión. Después, crearemos el modelo final, evaluaremos el modelo con los datos de prueba y lo guardaremos en el almacenamiento de blobs. Finalmente, cargaremos el modelo, puntuaremos los datos de prueba y evaluaremos su precisión.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**SALIDA:**

Tiempo que se tarda en ejecutar la celda anterior: 61 segundos.


## Uso de los modelos de aprendizaje automático incorporados en Spark con Scala

El procedimiento en el que se utiliza el código de Scala para cargar y puntuar automáticamente nuevos conjuntos de datos con modelos de aprendizaje automático creados en Spark y guardados en blobs de Azure se incluye en el tema [Puntuación de modelos de aprendizaje automático creados con Spark](machine-learning-data-science-spark-model-consumption.md). Los usuarios pueden seguir las instrucciones de ese artículo y, simplemente, reemplazar el código de Python por el de Scala proporcionado anteriormente para permitir el uso automatizado.

## Pasos siguientes

Para ver una introducción a los temas que lo guiarán por las tareas que componen el proceso de ciencia de datos en Azure, consulte [Proceso de ciencia de los datos en equipos (TDSP)](http://aka.ms/datascienceprocess).

Para ver una descripción del resto de los tutoriales completos que demuestran los pasos del proceso de ciencia de datos en equipos para **escenarios concretos** y que, además, ilustran cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el objetivo de crear una aplicación inteligente, consulte [Tutoriales del proceso de ciencia de datos en equipos](data-science-process-walkthroughs.md).

<!---HONumber=AcomDC_0803_2016-->