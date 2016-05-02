<properties
	pageTitle="Puntuación de modelos de aprendizaje automático creados con Spark | Microsoft Azure"
	description="Cómo puntuar modelos de aprendizaje almacenados en Almacenamiento de blobs de Azure (WASB)."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="deguhath;bradsev" />

# Puntuación de modelos de aprendizaje automático creados con Spark 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]


## Introducción

Este tema describe cómo cargar modelos de aprendizaje automático creados con Spark MLlib y almacenados en Almacenamiento de blobs de Azure (WASB), y cómo puntuarlos con conjuntos de datos que también se han almacenado en WASB. Muestra cómo procesar previamente los datos de entrada, transformar las características mediante las funciones de indexación y codificación en el kit de herramientas MLlib, y cómo crear un objeto de datos de punto con etiqueta que se puede usar como entrada para la puntuación con los modelos de aprendizaje automático. Los modelos usados para la puntuación son regresión lineal, regresión logística, modelos de bosque aleatorio y modelos de árboles impulsados por gradiente.


## Requisitos previos

1. Necesita una cuenta de Azure y un clúster de Spark en HDInsight para comenzar este tutorial. Consulte [Información general sobre la ciencia de los datos con Spark en HDInsight de Azure](machine-learning-data-science-spark-overview.md) para ver estos requisitos, obtener una descripción de los datos de taxis de Nueva York de 2013 usados aquí, y obtener instrucciones sobre cómo ejecutar código de un Notebook de Jupyter en el clúster de Spark. El Notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb**, que contiene los ejemplos de código de este tema, está disponible en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).

2. También debe crear los modelos de aprendizaje automático que se puntuarán aquí; para ello, consulte el tema [Exploración y modelado de datos con Spark](machine-learning-data-science-spark-data-exploration-modeling.md).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## Instalación de Spark y rutas a los directorios de los datos y modelos almacenados 

Spark puede leer y escribir en un blob de Almacenamiento de Azure (WASB), por lo que cualquiera de los datos existentes almacenados allí pueden procesarse mediante Spark y volver a almacenarse en WASB.

Para guardar modelos o archivos en WASB, la ruta de acceso debe especificarse correctamente. Se puede hacer referencia el contenedor predeterminado asociado al clúster Spark con un ruta que comience con: *"wasb//"*. El ejemplo de código siguiente especifica la ubicación de los datos que se van a leer y la ruta de acceso del directorio de almacenamiento del modelo donde se guardará la salida del modelo.


### Establecimiento de rutas de directorio para las ubicaciones de almacenamiento de WASB

Los modelos se guardan en: "wasb:///user/remoteuser/NYCTaxi/Models". Si esta ruta de acceso no está configurada correctamente, no se cargará los modelos para su puntuación.

Los resultados puntuados se guardan en: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Si la ruta de acceso a la carpeta es incorrecta, los resultados no se guardarán en esa carpeta.

>AZURE.NOTA: La ruta de acceso a las ubicaciones de los archivos se puede copiar y pegar en los marcadores de posición en este código, en la salida de la última celda del Notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb**.

	# LOCATION OF DATA TO BE SCORED (TEST DATA)
	taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
	
	# SET SCORDED RESULT DIRECTORY PATH
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
	
	# FILE LOCATIONS FOR THE MODELS TO BE SCORED
	logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
	linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
	randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
	randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
	BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
	BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

	# RECORD START TIME
	import datetime
	datetime.datetime.now()

**SALIDA:**

datetime.datetime(2016, 4, 19, 17, 21, 28, 379845)


### Importación de las bibliotecas necesarias y establecimiento del contexto de Spark 

Establezca el contexto de Spark e importe las bibliotecas necesarias con el siguiente código:

	#IMPORT LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	
	# SET SPARK CONTEXT
	sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
	sqlContext = SQLContext(sc)
	atexit.register(lambda: sc.stop())
	
	sc.defaultParallelism

**SALIDA:**

4


## Inserción de datos y creación de una trama de datos limpia

Esta sección contiene el código de una serie de tareas necesarias para incorporar los datos que se van a puntuar. Lee una muestra del 0,1 % combinado del archivo de tarifas y carreras de taxi (almacenado como un archivo .tsv), da formato a los datos y, después, crea un marco de datos limpio.

Los archivos de tarifas y carreras de taxi se combinaron según el procedimiento descrito en el tema [Proceso de análisis de Cortana en acción: uso de clústeres de Hadoop de HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

	# INGEST DATA AND CREATE A CLEANED DATA FRAME

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_test_file = sc.textFile(taxi_test_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	    
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_test_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# CREATE DATA FRAME
	taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_cleaned.cache()
	taxi_df_test_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_test_cleaned.registerTempTable("taxi_test")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SALIDA:**

Time taken to execute above cell: 15.36 seconds


## Preparación de los datos para la puntuación en Spark 

Esta sección muestra cómo indexar, codificar y ajustar la escala de características categóricas para prepararlas para su uso en algoritmos de aprendizaje supervisado de MLlib para clasificación y regresión.

### Transformación de características: indexación y codificación de características categóricas para su incorporación en modelos para puntuación 

Esta sección muestra cómo indexar datos categóricos mediante `StringIndexer` y cómo codificar características con la entrada de `OneHotEncoder` en los modelos.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica una columna de cadena de etiquetas en una columna de índices de etiqueta. Los índices se ordenan por frecuencias de etiqueta.

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) asigna una columna de índices de etiqueta a una columna de vectores binarios, con un solo valor uno como máximo. Esta codificación permite aplicar algoritmos que esperan características con valores continuos, como la regresión logística, a características categóricas.
	
	#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
	
	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_test 
	"""
	taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_with_newFeatures.cache()
	taxi_df_test_with_newFeatures.count()
	
	# INDEX AND ONE-HOT ENCODING
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_test_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND ENCODE TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SALIDA:**

Time taken to execute above cell: 4.88 seconds


### Creación de objetos RDD con matrices de características para su entrada en los modelos

Esta sección contiene código que muestra cómo indexar datos de texto categóricos como un objeto RDD y cómo codificarlos como “uno de n” para poder usarlos para entrenar y probar la regresión logística de MLlib y otros modelos basados en árboles. Los datos indexados se almacenan en objetos de [conjunto de datos distribuido resistente (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html). Esta es la abstracción básica en Spark. Un objeto RDD representa una colección inmutable con particiones de los elementos con los que se puede trabajar en paralelo con Spark.

También contiene código que muestra cómo ajustar la escala de los datos con `StandardScalar`, proporcionado por MLlib para su uso en la regresión lineal con descenso de gradiente estocástico (SGD), un popular algoritmo para entrenar una amplia variedad de modelos de aprendizaje automático. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) se usa para ajustar la escala de las características a la varianza de la unidad. El ajuste de la escala de las características, también conocido como normalización de los datos, garantiza que características con valores situados muy en los extremos no tengan un peso excesivo en la función objetivo.


	# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# IMPORT LIBRARIES
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	from numpy import array
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features

	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
	oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
	
	# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
	indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
	oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
	
	# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
	scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
	oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
	
	# CACHE RDDS IN MEMORY
	indexedTESTbinary.cache();
	oneHotTESTbinary.cache();
	indexedTESTreg.cache();
	oneHotTESTreg.cache();
	oneHotTESTregScaled.cache();
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**SALIDA:**

Time taken to execute above cell: 9.94 seconds


## Puntuación con el modelo de regresión logística y guardado de la salida en un blob

El código de esta sección muestra cómo cargar un modelo de regresión logística guardado en Almacenamiento de blobs de Azure, y cómo usarlo para predecir si se dio o no una propina en una carrera de taxi, puntuarlo con métricas de clasificación estándar y, después, guardar y trazar los resultados en un almacenamiento de blobs. Los resultados puntuados se almacenan en objetos RDD.


	# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	## LOAD SAVED MODEL
	savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
	predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
	
	## SAVE SCORED RESULTS (RDD) TO BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
	dirfilename = scoredResultDir + logisticregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SALIDA:**

Time taken to execute above cell: 32.46 seconds


## Puntuación de un modelo de regresión lineal

Usamos [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) para entrenar un modelo de regresión lineal que usa descenso de gradiente estocástico (SGD) para la optimización para predecir los importes de las propinas.

El código de esta sección muestra cómo cargar un modelo de regresión lineal desde Almacenamiento de blobs de Azure, puntuarlo mediante variables con ajuste de la escala y, después, guardar los resultados en el blob.

	#SCORE LINEAR REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	#LOAD LIBRARIES​
	from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
	
	# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
	savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
	predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = scoredResultDir + linearregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL​
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**SALIDA:**

Time taken to execute above cell: 25.00 seconds


## Puntuación de los modelos de bosque aleatorio de clasificación y regresión

El código de esta sección muestra cómo cargar los modelos de bosque aleatorio de clasificación y regresión guardados en Almacenamiento de blobs de Azure, puntuar su rendimiento con medidas estándar de clasificación y regresión y, después, guardar los resultados en Almacenamiento de blobs.

Los [bosques aleatorios](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) son conjuntos de árboles de decisión. Combinan varios árboles de decisión para reducir el riesgo de sobreajuste. Los bosques aleatorios pueden controlar características categóricas, amplían la configuración de clasificación multiclase, no requieren ajustar la escala de las características y pueden capturar errores de alineación e interacciones de las características. Los bosques aleatorios son uno de los modelos de aprendizaje automático de más éxito para clasificación y regresión.

[spark.mllib](http://spark.apache.org/mllib/) admite bosques aleatorios para clasificación binaria y multiclase, y para regresión, con características categóricas y continuas.

	# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES	
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfregressionfilename;
	predictions.saveAsTextFile(dirfilename)

	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**SALIDA:**

Time taken to execute above cell: 52.2 seconds


## Puntuación de modelos de árboles impulsados por gradiente de clasificación y regresión

El código de esta sección muestra cómo cargar los modelos de árboles impulsados por gradiente de clasificación y regresión guardados en Almacenamiento de blobs de Azure, puntuar su rendimiento con medidas estándar de clasificación y regresión y, después, guardar los resultados en Almacenamiento de blobs.

**spark.mllib** admite GBT para clasificación binaria y para regresión, con características categóricas y continuas.

Los [árboles impulsados por gradiente](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) son conjuntos de árboles de decisión. Los GBT entrenan árboles de decisión de forma iterativa para minimizar una función de pérdida. Los GBT pueden controlar características categóricas, no requieren ajustar la escala de las características y pueden capturar errores de alineación e interacciones de las características. También se pueden usar en una configuración de clasificación multiclase.


	# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# CLASSIFICATION:LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	#LOAD AND SCORE THE MODEL
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	# LOAD AND SCORE MODEL 
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btregressionfilename;
	predictions.saveAsTextFile(dirfilename)


	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
	
**SALIDA:**

Time taken to execute above cell: 27.73 seconds

## Limpieza de objetos de la memoria e impresión de las ubicaciones de los archivos puntuados

	# UNPERSIST OBJECTS CACHED IN MEMORY
	taxi_df_test_cleaned.unpersist()
	indexedTESTbinary.unpersist();
	oneHotTESTbinary.unpersist();
	indexedTESTreg.unpersist();
	oneHotTESTreg.unpersist();
	oneHotTESTregScaled.unpersist();


	# PRINT OUT PATH TO SCORED OUTPUT FILES
	print "logisticRegFileLoc: " + logisticregressionfilename;
	print "linearRegFileLoc: " + linearregressionfilename;
	print "randomForestClassificationFileLoc: " + rfclassificationfilename;
	print "randomForestRegFileLoc: " + rfregressionfilename;
	print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
	print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**SALIDA:**

logisticRegFileLoc: LogisticRegressionWithLBFGS\_2016-04-1917\_22\_36.354603.txt

linearRegFileLoc: LinearRegressionWithSGD\_2016-04-1917\_23\_06.083178

randomForestClassificationFileLoc: RandomForestClassification\_2016-04-1917\_23\_33.994108.txt

randomForestRegFileLoc: RandomForestRegression\_2016-04-1917\_24\_00.352683.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification\_2016-04-1917\_24\_21.465683.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression\_2016-04-1917\_24\_32.371641.txt



## Uso de modelos de Spark mediante una interfaz web

Spark proporciona un mecanismo para el envío remoto de trabajos por lotes o consultas interactivas mediante una interfaz REST con un componente llamado Livy. Livy está habilitado de forma predeterminada en el clúster de Spark en HDInsight. Para más información, consulte [Envío de trabajos de Spark de manera remota mediante Livy](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md).

Puede usar Livy para enviar de forma remota un trabajo que puntúa por lotes un archivo almacenado en un blob de Azure y, después, escribe los resultados en otro blob. Para ello, cargue el script de Python desde [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) en el blob del clúster de Spark. Puede usar una herramienta como **Microsoft Azure Storage Explorer** o **AzCopy** para copiar el script en el blob del clúster. En nuestro caso, cargamos el script en ***wasb:///example/python/ConsumeGBNYCReg.py***.

>AZURE.NOTA: Las claves de acceso que necesita se encuentran en el portal de la cuenta de almacenamiento asociada con el clúster de Spark.

Una vez cargado en esta ubicación, el script se ejecutará dentro del clúster de Spark en un contexto distribuido. Cargará el modelo y ejecutará predicciones en los archivos de entrada basándose en el modelo.

Para invocar este script de forma remota con una sencilla solicitud HTTPS/REST en Livy. Este es un comando de CURL para construir la solicitud HTTP para invocar el script de Python de forma remota. Tiene que reemplazar CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME por los valores adecuados para su clúster de Spark.


	# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Puede usar cualquier lenguaje en el sistema remoto para invocar el trabajo de Spark a través de Livy con una sencilla llamada HTTPS con autenticación básica.

>AZURE.NOTA: Sería conveniente usar la biblioteca de solicitudes de Python para realizar esta llamada HTTP, pero actualmente no está instalada de forma predeterminada en Funciones de Azure. Por ese motivo se usan bibliotecas HTTP más antiguas.

Este es el código de Python para la llamada HTTP:

	#MAKE AN HTTPS CALL ON LIVY. 

	import os

	# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
	import httplib, urllib, base64
	
	# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
	host = '<spark cluster name>.azurehdinsight.net:443'
	username='<username>'
	password='<password>'
	
	#AUTHORIZATION
	conn = httplib.HTTPSConnection(host)
	auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
	headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
	
	# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
	# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
	r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
	response = conn.getresponse().read()
	print(response)
	conn.close()


También puede agregar este código de Python a [Funciones de Azure](../functions/) para desencadenar el envío de un trabajo de Spark que puntúa un blob en función de diversos eventos tales como un temporizador o la creación o actualización de un blob.

Si prefiere usar un cliente sin código, use [Aplicaciones lógicas de Azure](../app-service/logic/) para invocar la puntuación por lotes de Spark; para ello, defina una acción HTTP en el **Diseñador de aplicaciones lógicas** y establezca sus parámetros.

- En el Portal de Azure, para crear una nueva aplicación lógica, seleccione **+Nuevo** -> **Web y móvil** -> **Aplicación lógica**. 
- Escriba el nombre de la aplicación lógica y del plan de Servicio de aplicaciones para que aparezca el **Diseñador de aplicaciones lógicas**.
- Seleccione una acción HTTP y especifique los parámetros mostrados en la ilustración siguiente:

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)

<!---HONumber=AcomDC_0420_2016-->