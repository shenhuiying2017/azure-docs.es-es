<properties 
	pageTitle="Aplicación de aprendizaje automático: servicio de detección de anomalías | Microsoft Azure" 
	description="La API de detección de anomalías es un ejemplo integrado en Aprendizaje automático de Microsoft Azure que detecta anomalías en los datos de serie temporales con valores numéricos espaciados de manera uniforme en el tiempo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="alokkirpal" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/22/2016" 
	ms.author="alokkirpal"/>


# Servicio de detección de anomalías de aprendizaje automático#

##Información general

La API de detección de anomalías es un ejemplo integrado en Aprendizaje automático de Azure que detecta anomalías en los datos de serie temporales con valores numéricos espaciados de manera uniforme en el tiempo.

Estas API pueden detectar los siguientes tipos de patrones anómalos en datos de series temporales:

* **Tendencias positivas y negativas**: por ejemplo, al supervisar el uso de memoria en informática, una tendencia al alza puede resultar interesante porque puede ser indicativa de una pérdida de memoria.

* **Cambios en el intervalo dinámico de valores**: por ejemplo, al supervisar las excepciones producidas por un servicio en la nube, cualquier cambio en el intervalo dinámico de valores podría indicar inestabilidad en el estado del servicio.

* **Cambios abruptos e interrupciones**: por ejemplo, al supervisar el número de errores de inicio de sesión en un servicio o el número de finalizaciones de compras en un sitio de comercio electrónico, los cambios abruptos y las interrupciones podrían indicar un comportamiento anormal.

Estos detectores de aprendizaje automático realizan un seguimiento de dichos cambios en los valores con el paso del tiempo e informan de los cambios continuados en sus valores con puntuaciones de anomalías. No requieren el ajuste del umbral ad hoc y sus puntuaciones pueden utilizarse para controlar el índice de falsos positivos. La API de detección de anomalías es útil en varios escenarios, por ejemplo, la supervisión de servicios mediante el seguimiento de KPI en el tiempo, la supervisión del uso mediante métricas como el número de búsquedas o el número de clics y la supervisión del rendimiento mediante contadores como la memoria, la CPU, las lecturas de archivos, etc., en el tiempo.

La oferta de detección de anomalías incluye herramientas útiles para comenzar.

* La [aplicación web](http://anomalydetection-aml.azurewebsites.net/) le ayuda a evaluar y visualizar los resultados de las API de detección de anomalías en los datos.

* El [código de ejemplo](http://adresultparser.codeplex.com/) muestra cómo acceder a la API mediante programación y analizar los resultados en C#.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Definición de la API

El servicio proporciona API basadas en REST a través de HTTPS que se pueden consumir de varias formas, como una aplicación web o móvil, R, Python, Excel, etc. Los datos de la serie temporal se envían a este servicio mediante una llamada a la API de REST, y se ejecuta una combinación de los tres tipos de anomalías descritos anteriormente. El servicio se ejecuta en la plataforma de Aprendizaje automático de Azure, que se adapta a las necesidades de su empresa sin problemas y proporciona Acuerdos de Nivel de Servicio (SLA).

###Encabezados
Asegúrese de incluir los encabezados correctos en la solicitud, que debería ser como sigue:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Puede encontrar la clave de su cuenta en [Microsoft Azure Marketplace ](https://datamarket.azure.com/account/keys).

###API de puntuación

La API de puntuación se utiliza para ejecutar la detección de anomalías en datos de series temporales no estacionales. La API ejecuta una serie de detectores de anomalías en los datos y devuelve sus puntuaciones de anomalías. En la siguiente ilustración se muestra un ejemplo de anomalías que puede detectar la API de puntuación. Esta serie temporal tiene 2 cambios de nivel distintos y 3 picos. Los puntos rojos muestran la hora en que se detecta el cambio de nivel, mientras que los puntos negros muestran los picos detectados.

![API de puntuación][1]
	
**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Cuerpo de solicitud de ejemplo**

En la siguiente solicitud, enviamos a la API una serie temporal (aparece truncada) con puntos de datos del 1/3/2016 al 10/3/2016 y parámetros de los detectores de picos con el fin de detectar si alguno de estos puntos de datos es anómalo.

	{
	  "data": [
	    [ "9/21/2014 11:05:00 AM", "3" ],
	    [ "9/21/2014 11:10:00 AM", "9.09" ],
	    [ "9/21/2014 11:15:00 AM", "0" ]
	  ],
	  "params": {
	    "tspikedetector.sensitivity": "3",
	    "zspikedetector.sensitivity": "3",
	    "trenddetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "postprocess.tailRows": "2"
	  }
	}

La respuesta a esto será:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
	  "ADOutput":"{
	    "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
		"ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
		"Values":[
		  ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
		  ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
		]
	  }"
	}

###La API ScoreWithSeasonality

La API ScoreWithSeasonality se utiliza para ejecutar la detección de anomalías en series temporales que tienen patrones estacionales. Esta API es útil para detectar desviaciones en los patrones estacionales.

En la siguiente ilustración se muestra un ejemplo de anomalías detectadas en una serie temporal estacional. La serie temporal tiene un pico (el primer punto negro), dos interrupciones (el segundo punto negro y otro al final) y un cambio de nivel (punto rojo). Tenga en cuenta que tanto la interrupción en el medio de la serie temporal como el cambio de nivel son solo apreciables después de que los componentes estacionales se eliminan de la serie.

![API de estacionalidad][2]

**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Cuerpo de solicitud de ejemplo**

En la siguiente solicitud, enviamos a la API una serie temporal (aparece truncada) con puntos de datos del 1/3/2016 al 10/3/2016 y parámetros para detectar si alguno de estos puntos de datos es anómalo.

	{
	  "data": [
	    [ "9/21/2014 11:10:00 AM", "9" ],
	    [ "9/21/2014 11:15:00 AM", "12" ],
	    [ "9/21/2014 11:20:00 AM", "10" ]
	  ],
	  "params": {
	    "preprocess.aggregationInterval": "0",
	    "preprocess.aggregationFunc": "mean",
	    "preprocess.replaceMissing": "lkv",
	    "postprocess.tailRows": "1",
	    "zspikedetector.sensitivity": "3",
	    "tspikedetector.sensitivity": "3",
	    "upleveldetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "trenddetector.sensitivity": "3.25",
	    "detectors.historywindow": "500",
	    "seasonality.enable": "true",
	    "seasonality.transform": "deseason",
	    "seasonality.numSeasonality": "1"
	  }
	}

La respuesta a esto será:

	{
		"odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
		"ADOutput": "{
			"ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
		  	"ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
		  	"Values":[
		    	["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
		  	]
		}"
	}

###Detectores

La API de detección de anomalías admite detectores en tres categorías generales. En la siguiente tabla se pueden encontrar detalles sobre parámetros de entrada específicos y salidas para cada detector.

|Categoría del detector|Detector|Descripción|Parámetros de entrada|Salidas
|---|---|---|---|---|
|Detectores de pico|Detector de TSpike|Detección de picos e interrupciones según la sensibilidad establecida|*tspikedetector.sensitivity:* toma el valor entero en el intervalo 1-10, valor predeterminado: 3; cuanto más alto, menos sensible|TSpike: valores binarios: '1' si se detecta un pico o una interrupción, '0' en caso contrario|
||Detector de ZSpike|Detección de picos e interrupciones según la sensibilidad establecida|*zspikedetector.sensitivity:* toma el valor entero en el intervalo 1-10, valor predeterminado: 3; cuánto más alto, menos sensible|ZSpike: valores binarios: '1' si se detecta un pico o una interrupción, si no '0'|
|Detector de tendencia lenta|Detector de tendencia lenta|Detección de tendencia positiva lenta según la sensibilidad establecida|*trenddetector.sensitivity:* umbral en la puntuación del detector (valor predeterminado: 3,25, 3,25 - 5 es un intervalo razonable del que seleccionar este valor; cuanto más alto, menos sensible)|TScore: número flotante que representa la puntuación de anomalías en la tendencia|
|Detectores de cambio de nivel|Detector de cambio de nivel unidireccional|Detección de cambio de nivel ascendente según la sensibilidad establecida|*upleveldetector.sensitivity*: umbral en la puntuación del detector (valor predeterminado: 3,25, 3,25 - 5 es un intervalo razonable del que seleccionar este valor; cuanto más alto, menos sensible)|PScore: número flotante que representa la puntuación de anomalía en el cambio de nivel ascendente|
||Detector de cambio de nivel bidireccional|Detección de cambio de nivel ascendente y descendente según la sensibilidad establecida|*bileveldetector.sensitivity*: umbral en la puntuación del detector (valor predeterminado: 3,25, 3,25 - 5 es un intervalo razonable del que seleccionar este valor; cuanto más alto, menos sensible)|RPScore: número flotante que representa la puntuación de anomalía en el cambio de nivel ascendente y descendente

###Parámetros

En la siguiente tabla se muestra información más detallada sobre estos parámetros de entrada:

|Parámetros de entrada|Descripción|Configuración predeterminada|Tipo|Intervalo válido|Intervalo sugerido|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervalo de agregación en segundos para agregar series temporales de entrada|0 (no se realiza ninguna agregación)|integer|0: omitir agregación, de lo contrario, > 0|De 5 minutos a 1 día, dependiente de la serie temporal
|preprocess.aggregationFunc|Función que se usa para agregar datos al objeto AggregationInterval especificado|mean|enumerated|mean, sum, length|N/D|
|preprocess.replaceMissing|Valores que se utiliza para atribuir los datos que faltan|lkv (último valor conocido)|enumerated|zero, lkv, mean|N/D|
|detectors.historyWindow|Historial (en número de puntos de datos) utilizado para el cálculo de la puntuación de anomalía|500|integer|10-2000|Dependiente de la serie temporal|
|upleveldetector.sensitivity|Sensibilidad del detector de cambio de nivel ascendente. |3\.25|double|None|3\.25 5 (cuanto menores sean los valores, mayor es la sensibilidad)|
|bileveldetector.sensitivity|Sensibilidad del detector de cambio de nivel bidireccional. |3\.25|double|None|3\.25 5 (cuanto menores sean los valores, mayor es la sensibilidad)|
|trenddetector.sensitivity|Sensibilidad del detector de tendencia positiva. |3\.25|double|None|3\.25 5 (cuanto menores sean los valores, mayor es la sensibilidad)|
|tspikedetector.sensitivity |Sensibilidad del detector TSpike|3|integer|1-10|3-5 (cuanto menores sean los valores, mayor es la sensibilidad)|
|zspikedetector.sensitivity |Sensibilidad del detector ZSpike|3|integer|1-10 |3-5 (cuanto menores sean los valores, mayor es la sensibilidad)|
|seasonality.enable|Si se realizará el análisis de estacionalidad|true|boolean|true, false|Dependiente de la serie temporal|
|seasonality.numSeasonality |Número máximo de ciclos periódicos para detectar|1|integer|1, 2|1-2|
|seasonality.transform |Si los componentes de tendencia (y) estacionales se eliminarán antes de aplicar la detección de anomalías|deseason|enumerated|none, deseason, deseasontrend|N/D|
|postprocess.tailRows |Número de los puntos de datos más recientes que se van a mantener en los resultados de salida|0|integer|0 (se mantienen todos los puntos de datos), o especifique el número de puntos para mantener en los resultados|N/D|

###Salida
La API ejecuta todos los detectores en los datos de la serie temporal y devuelve puntuaciones de anomalías e indicadores de picos binarios para cada punto en el tiempo. En la tabla siguiente se muestran los resultados de la API.

|Salidas|Descripción|
|---|---|
|Hora|Marcas de tiempo de datos sin procesar, o datos agregados (o) atribuidos si se aplica la agregación (o) atribución de los datos que faltan|
|OriginalData|Valores de datos sin procesar, o datos agregados (o) atribuidos si se aplica la agregación (o) atribución de los datos que faltan|
|ProcessedData|Cualquiera de las siguientes: <ul><li>Serie temporal ajustada estacionalmente si se ha detectado una estacionalidad importante y se ha seleccionado la opción deseason;</li><li>serie temporal ajustada estacionalmente y con anulación de tendencia (detrended) si se ha detectado una estacionalidad importante y se ha seleccionado la opción deseasontrend</li><li>de lo contrario, es igual a OriginalData</li>|
|TSpike|Indicador binario para indicar si el detector TSpike ha detectado un pico|
|ZSpike|Indicador binario para indicar si el detector ZSpike ha detectado un pico|
|Pscore|Número flotante que representa la puntuación de anomalía en el cambio de nivel ascendente|
|Palert|Valor 1/0 que indica que hay una anomalía de cambio de nivel ascendente basada en la sensibilidad de entrada|
|RPScore|Número flotante que representa la puntuación de anomalía en el cambio de nivel bidireccional|
|RPAlert|Valor 1/0 que indica que hay una anomalía de cambio de nivel bidireccional basada en la sensibilidad de entrada|
|TScore|Número flotante que representa la puntuación de anomalía en tendencia positiva|
|TAlert|Valor 1/0 que indica que hay una anomalía de tendencia positiva basada en la sensibilidad de entrada|


Este resultado se puede analizar mediante un [analizador sencillo](https://adresultparser.codeplex.com/); incluye código de ejemplo que muestra cómo conectarse a la API y analizar la salida. Las anomalías detectadas se pueden visualizar en un panel o transferirse a personal experto para que tomen las medidas correctivas oportunas, o se pueden integrar en sistemas de control de vales.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

<!---HONumber=AcomDC_0727_2016-->