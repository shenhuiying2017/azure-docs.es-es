---
title: Uso de ScaleR y SparkR con Azure HDInsight | Microsoft Docs
description: Uso de ScaleR y SparkR con R Server y HDInsight
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: b84c365defbaadbc83c86e6e387c15a63e0f17ce
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Combinación de ScaleR y SparkR en HDInsight

En este artículo se muestra cómo predecir retrasos en la llegada de los vuelos con un modelo de regresión logística de **ScaleR** a partir de los datos de retrasos de vuelos y la meteorología combinados con **SparkR**. Este escenario muestra la funcionalidad de ScaleR para la manipulación de datos en Spark que se usa con Microsoft R Server para el análisis. La combinación de estas tecnologías permite aplicar la funcionalidad más reciente del procesamiento distribuido.

Aunque ambos paquetes se procesan en el motor de ejecución de Spark de Hadoop, se les impide compartir datos en memoria, ya que cada uno de ellos requiere sus correspondientes sesiones de Spark. Hasta que este problema se solucione en una próxima versión de R Server, la solución alternativa consiste en mantener sesiones de Spark no superpuestas e intercambiar datos mediante archivos intermedios. Las siguientes instrucciones muestran que estos requisitos son fáciles de lograr.

Usaremos un ejemplo compartido inicialmente en una charla en Strata 2016 por Mario Inchiosa y Roni Burd, que también está disponible en el seminario web [Building a Scalable Data Science Platform with R](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio) (Creación de una plataforma escalable de ciencia de datos con R). En el ejemplo se utiliza SparkR para combinar el conjunto de datos de retraso de llegada de las líneas aéreas conocido con los datos meteorológicos en aeropuertos de salida y llegada. Acto seguido, los datos combinados se utilizan como entrada para un modelo de regresión logística de ScaleR para predecir los retrasos en vuelos llegada.

El código utilizado se escribió originalmente para R Server que se ejecuta en Spark en un clúster de HDInsight en Azure. Pero el concepto de mezclar el uso de SparkR y ScaleR en un script también es válido en el contexto de entornos locales. En las siguientes secciones, se presupone un nivel de conocimiento intermedio de R y la biblioteca [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) de R Server. También presentamos el uso de [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) al recorrer este escenario.

## <a name="the-airline-and-weather-datasets"></a>Los conjuntos de datos de líneas aéreas y meteorológicos

El conjunto de datos público de líneas aéreas **AirOnTime08to12CSV** contiene información detallada sobre las llegadas y salidas de todos los vuelos comerciales dentro de EE. UU., desde octubre de 1987 a diciembre de 2012. Se trata de un conjunto de datos grande: hay casi 150 millones de registros en total. Es algo inferior a 4 GB desempaquetado. Está disponible en los [archivos del gobierno de EE. UU](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Para su conveniencia, está disponible como archivo zip (AirOnTimeCSV.zip) que contiene un conjunto de 303 archivos CSV separados por meses del [repositorio de conjuntos de datos de Revolution Analytics](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/).

Para ver los efectos de la meteorología en el retraso de los vuelos, necesitamos también los datos meteorológicos de cada uno de los aeropuertos. Estos datos se pueden descargar como archivos zip sin procesar por mes del [repositorio de la Administración Nacional Oceánica y Atmosférica](http://www.ncdc.noaa.gov/orders/qclcd/). Para los fines de este ejemplo, extraemos datos meteorológicos del período comprendido entre mayo de 2007 y diciembre de 2012 y usamos los archivos de datos por hora dentro de cada uno de los 68 archivos zip mensuales. Los archivos zip mensuales también contienen una asignación (AAAAMMstation.txt) entre el identificador de la estación meteorológica (WBAN), el aeropuerto con el que está asociada (CallSign) y el ajuste de la zona horaria del aeropuerto de UTC (TimeZone). Toda esta información es necesaria cuando se realiza la combinación con los datos de retraso de las líneas aéreas y meteorológicos.

## <a name="setting-up-the-spark-environment"></a>Configuración del entorno de Spark

El primer paso es configurar el entorno de Spark. Empezamos por apuntar al directorio que contiene nuestros directorios de datos de entrada, crear un contexto de proceso de Spark y crear una función de registro para el registro informativo en la consola:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAcccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Luego, agregamos "Spark_Home" a la ruta de acceso de búsqueda de paquetes R de forma que podamos usar SparkR e inicializamos una sesión de SparkR:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Preparación de los datos meteorológicos

Para preparar los datos meteorológicos, los subagrupamos en las columnas necesarias para el modelado: 

- "Visibility"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

A continuación, agregamos un código de aeropuerto asociado a la estación meteorológica y convertimos las mediciones de la hora local a UTC.

Para comenzar, creamos un archivo para asignar la información de la estación meteorológica (WBAN) a un código de aeropuerto. Podríamos obtener esta correlación del archivo de asignación incluido con los datos meteorológicos. Para ello, asignaríamos el campo *CallSign* (por ejemplo, LAX) del archivo de datos meteorológicos a *Origin* de los datos de la compañía aérea. Sin embargo, resulta que tenemos otra asignación que se asigna *WBAN* a *AirportID* (por ejemplo, 12892 para LAX) e incluye *TimeZone* que se ha guardado en un archivo CSV denominado “wban-to-airport-id-tz.CSV” que podemos usar. Por ejemplo:

| AirportID | WBAN | TimeZone
|-----------|------|---------
| 10685 | 54831 | -6
| 14871 | 24232 | -8
| .. | .. | ..

El código siguiente lee cada uno de los archivos de datos meteorológicos sin procesar por hora, los subagrupa en las columnas que necesitamos, combina el archivo de asignación de la estación meteorológica, ajusta las horas y fechas de medidas a UTC y luego escribe una nueva versión del archivo:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importación de los datos de líneas aéreas y meteorológicos en Spark DataFrames

Ahora usamos la función [read.df()](https://docs.databricks.com/spark/latest/sparkr/functions/read.df.html) de SparkR para importar los datos de líneas aéreas y meteorológicos en Spark DataFrames. Esta función, al igual que muchos otros métodos de Spark, se ejecuta de forma diferida, lo que significa que se pone en la cola para la ejecución pero no se ejecuta hasta que es necesario.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Limpieza y transformación de datos

Después, realizamos alguna limpieza en los datos de la línea aérea que hemos importado para cambiar el nombre de las columnas. Solo mantenemos las variables necesarias y redondeamos a la baja las horas de salida programadas a la hora más cercana para habilitar la combinación con los datos meteorológicos más recientes a la salida:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Ahora se llevan a cabo operaciones similares en los datos meteorológicos:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Combinación de los datos de líneas aéreas y meteorológicos

Ahora usamos la función [join()](https://docs.databricks.com/spark/latest/sparkr/functions/join.html) de SparkR para realizar una combinación externa izquierda de los datos de líneas áreas y meteorológicos por AirportID y datetime de salida. La combinación externa nos permite conservar todos los registros de datos de líneas aéreas incluso si no hay ningún dato meteorológico coincidente. Tras la combinación, se quitan algunas columnas redundantes y se cambia el nombre de las columnas que quedan para quitar el prefijo DataFrame entrante introducido por la combinación.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

De igual forma, unimos los datos de líneas áreas y meteorológicos según el valor de AirportID y datetime de llegada:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Guardado de los resultados en CSV para el intercambio con ScaleR

Así finalizan las combinaciones que necesitamos hacer con SparkR. Guardamos los datos del elemento "joinedDF5" final de Spark DataFrame en un archivo CSV para la entrada en ScaleR y luego cerramos la sesión de SparkR. Indicaremos explícitamente a SparkR que guarde el archivo CSV resultante en 80 particiones distintas para permitir suficiente paralelismo en el procesamiento de ScaleR:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importación en XDF para su uso por ScaleR

Podríamos usar el archivo CSV de los datos de líneas aéreas y meteorológicos combinados como está para el modelado a través de un origen de datos de texto de ScaleR. Sin embargo, lo importamos a XDF primero, ya que es más eficaz cuando se ejecutan varias operaciones en el conjunto de datos:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for R Server 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>División de los datos para entrenamiento y pruebas

Usaremos rxDataStep para dividir los datos de 2012 con fines de prueba y mantendremos el resto para entrenamiento:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Entrenamiento y prueba de un modelo de regresión logístico

Ya estamos listos para crear un modelo. Para ver el efecto de los datos meteorológicos en el retraso en la hora de llegada, usamos la rutina de regresión logística de ScaleR. La utilizamos para modelar si un retardo de llegada de más de 15 minutos se ve afectado por la meteorología en los aeropuertos de salida y de llegada:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Ahora veamos cómo se hace esto en los datos de prueba; para ello, realizamos algunas predicciones y examinamos ROC y AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Puntuación en otros lugares

También se puede usar el modelo para puntuar datos en otra plataforma. Para ello, se guardan en un archivo RDS y luego se transfiere e importa dicho archivo a un entorno de puntuación de destino, como SQL Server R Services. Es importante asegurarse de que los niveles de factor de los datos que se van a puntuar coincidan con aquellos sobre los que se creó el modelo. Esa coincidencia se puede lograr extrayendo y guardando la información de columna asociada a los datos de modelado mediante la función `rxCreateColInfo()` de ScaleR y luego aplicando esa información de columna al origen de datos de entrada para realizar la predicción. Ahora guardamos algunas filas del conjunto de datos de prueba y extraemos y usamos la información de columna de este ejemplo en el script de predicción:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Resumen

En este artículo hemos mostrado cómo es posible combinar el uso de SparkR para la manipulación de datos con ScaleR para el desarrollo de modelos de Hadoop Spark. Este escenario requiere que se mantengan sesiones de Spark independientes, ejecutando solamente una sesión a la vez, y se intercambien datos a través de archivos CSV. Aunque sencillo, este proceso debe ser mucho más fácil en una próxima versión de R Server, cuando SparkR y ScaleR puedan compartir una sesión de Spark y, por tanto, compartir Spark DataFrames.

## <a name="next-steps-and-more-information"></a>Pasos siguientes y más información

- Para más información sobre el uso de R Server en Spark, consulte la [guía de introducción en MSDN](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).

- Para información general sobre R Server, consulte el artículo [Get started with R](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node) (Introducción a R).

- Para obtener información sobre R Server en HDInsight, consulte [Introducción a las funcionalidades de R de código abierto de R Server en HDInsight](r-server/r-server-overview.md) e [Introducción al uso de R Server en HDInsight](r-server/r-server-get-started.md).

Para más información sobre el uso de SparkR, consulte:

- [Documento de Apache SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html)

- [SparkR Overview](https://docs.databricks.com/spark/latest/sparkr/overview.html) (Información general de SparkR) de Databricks
