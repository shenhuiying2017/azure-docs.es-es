<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning Sample: Flight delay prediction | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts whether a scheduled passenger flight will be delayed by more than 15 minutes." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Ejemplo de Aprendizaje automático de Azure: predicción de retrasos de vuelos

*Puede ver el experimento de ejemplo asociado a este modelo en Estudio de aprendizaje automático, en la sección **EXPERIMENTOS** que se encuentra bajo la pestaña **EJEMPLOS**. El nombre del experimento es:*

    Sample Experiment - Flight Delay Prediction - Development

## Descripción del problema

Pronostique si la llegada de un vuelo de pasajeros se retrasa más de 15 minutos mediante datos de rendimiento históricos sobre puntualidad y meteorológicos.

## Datos

**Datos de rendimiento sobre puntualidad en vuelos de pasajeros recopilados en TranStats por el Departamento de Transporte de EE. UU:** [][]<http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time></a>

El conjunto de datos abarca el período entre abril y octubre de 2013. Antes de cargar la información a Estudio de aprendizaje automático de Azure, se hizo lo siguiente:

-   Se filtró el conjunto de datos para centrarse en los 70 aeropuertos con más tráfico del territorio continental de EE. UU.
-   Se seleccionaron las siguientes columnas: 'Year', 'Month', 'DayofMonth', 'DayOfWeek','Carrier', 'OriginAirportID','DestAirportID','CRSDepTime','DepDelay','DepDel15', 'CRSArrTime','ArrDelay','ArrDel15','Cancelled'
-   Los vuelos cancelados se etiquetaron como retrasados más de 15 minutos.
-   Los vuelos desviados se quitaron de la muestra.

**Observaciones meteorológicas en tierra por hora de la NOAA:** <http://cdo.ncdc.noaa.gov/qclcd_ascii/>

La información climatológica abarca observaciones de estaciones meteorológicas asociadas a cada uno de los aeropiertos entre abril y octubre de 2013. Antes de cargar la información a Estudio de aprendizaje automático, se hizo lo siguiente:

-   Se asignaron los identificadores de las estaciones meteorológicas a los identificadores de aeropuerto correspondientes.
-   Se excluyeron de la muestra las estaciones meteorológicas no asociadas a los 70 aeropuertos con mayor densidad de tráfico
-   La columna Date se separó en columnas independientes con los valores Year, Month y Day
-   Se seleccionaron las siguientes columnas: 'AirportID', 'Year','Month','Day', 'Time', 'TimeZone', 'SkyCondition', 'Visibility','WeatherType','DryBulbFarenheit', 'DryBulbCelsius', 'WetBulbFarenheit', 'WetBulbCelsius', 'DewPointFarenheit','DewPointCelsius', 'RelativeHumidity', 'WindSpeed', 'WindDirection', 'ValueForWindCharacter','StationPressure', 'PressureTendency', 'PressureChange', 'SeaLevelPressure', 'RecordType', 'HourlyPrecip', 'Altimeter'

## Modelo

**Procesado previo de los datos de vuelos**

En primer lugar, las columnas que podrían permitir la pérdida de datos (DepDelay, DepDel15, ArrDelay, Cancelled) se excluyeron del conjunto de datos.

Las columnas DayOfWeek','OriginAirportID' y 'DestAirportID' representan atributos categóricos. Sin embargo, dado que contienen valores enteros, se analizaron inicialmente como valores numéricos continuos, y fue necesario convertirlas a categóricos mediante eel Editor de metadatos.

Nuestro objetivo era unir registros de vuelos con registros climatológicos por horas utilizando la hora de salida programada como uno de los principales valores de unión. Para ello, se redondeó la columna CSRDepTime hacia abajo hasta la hora más cercana.

**Procesado previo de los datos meteorológicos**

En primer lugar, se excluyeron las columnas que tienen una elevada proporción de valores ausentes. Entre ellas se encuentran las siguientes:

-   Todas las columnas con valores de cadena
-   ValueForWindCharacter,WetBulbFarenheit,WetBulbCelsius,PressureTendency,PressureChange,SeaLevelPressure,StationPressure

A continuación, se aplicó la limpieza de valores ausentes a las columnas restantes.

La hora de la observación climatológica se redondeó hacia arriba hasta la hora completa más tardía para poder unirla equitativamente a la hora de salida programada del vuelo. Observe que la hora programada del vuelo y la hora de observación de las condiciones meteorológicas se redondean en direcciones opuestas. Esto se hace así para garantizar que el modelo utilice solo observaciones meteorológicas que tuvieron lugar en el pasado relativo a la hora de vuelo.

**Unión de conjuntos de datos**

Los registros de vuelos se unen a los datos climatológicos en el punto de origen y de destino del vuelo.

Tenga en cuenta que los datos climatológicos se notifican en hora local, aunque es posible que el origen y el destino se encuentren en diferentes zonas horarias. Por tanto, se realiza un ajuste en la diferencia de zona horaria restando las columnas de zona horaria de la hora de salida programada y la hora de observación de las condiciones meteorológicas.

**Preparación de muestras de entrenamiento y validación**

Las muestras de entrenamiento y validación se crean dividiendo los datos en registros de abril y septiembre para entrenamiento y registros de octubre para validación. Las columnas de año y mes se quitan entonces del conjunto de datos.

Además, los datos de entrenamiento se cuantifican mediante la agrupación por misma altura, y esta misma agrupación se aplica a la validación de datos.

**Entrenamiento y validación de modelo**

El modelo de árbol de decisión ampliado de dos clases se entrena en comparación con la muestra de entrenamiento. El modelo está optimizado para la mejor área bajo la curva utilizando una limpieza de parámetros aleatoria de 10 pliegos. Para la comparación, se optimiza del mismo modo un modelo de regresión logística de dos class.

Los modelos entrenados se puntúan entonces sobre el conjunto de validación, y se utiliza el módulo Evaluar modelo para analizar la calidad de los modelos comparados unos con otros.

**Procesado posterior**

Los identificadores de aeropuerto se unen a nombres y ubicaciones de aeropuerto legibles para el ojo humano a fin de simplificar el análisis de los resultados.

## Resultados

El modelo de árbol de decisión aumentado tiene una AUC de 0,697 frente al conjunto de validación, lo cual es ligeramente mejor que el modelo de regresión logística con una AUN de 0,675.

  []: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
