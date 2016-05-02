<properties
	pageTitle="Información general sobre la ciencia de los datos con Spark en HDInsight de Azure | Microsoft Azure"
	description="El kit de herramientas MLlib de Spark ofrece importantes funciones de modelado de aprendizaje automático para el entorno distribuido de HDInsight."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="bradsev" />

# Información general sobre la ciencia de los datos con Spark en HDInsight de Azure

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## Introducción

Spark en HDInsight de Azure puede manipular los datos en memoria mientras se procesan de forma distribuida en un clúster de HDInsight (Hadoop). Por lo tanto, este clúster de Spark combina velocidad y capacidad. También incluye compatibilidad con Notebooks de Jupyter en el clúster Spark, que pueden ejecutar consultas interactivas de Spark SQL para transformar, filtrar y visualizar los datos almacenados en blobs de Azure (WASB).

[Spark](http://spark.apache.org/) es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. [MLlib](http://spark.apache.org/mllib/) es la biblioteca de aprendizaje automático escalable de Spark.

Spark en HDInsight es la oferta de Spark de código abierto hospedada por Azure. Los pasos de instalación y el código proporcionado en este tutorial son para Spark en HDInsight. Sin embargo, el código es genérico y debería funcionar en cualquier clúster de Spark. Los pasos de configuración y administración del clúster pueden ser ligeramente diferentes de los que se muestran aquí si no está usando Spark en HDInsight.

El kit de herramientas MLlib de Spark ofrece importantes funciones de modelado de aprendizaje automático para este entorno distribuido. La colección de temas vinculados en el menú muestra este aspecto y, para ello, trabaja mediante tareas de regresión y clasificación binaria en una muestra del conjunto de datos de tarifas y carreras de taxi de la ciudad de Nueva York de 2013. Después, almacena los resultados del modelo en WASB. Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente. También se indica cómo usar los resultados de estos modelos para puntuarlos y evaluarlos con relación a otros conjuntos de datos almacenados en WASB. Los temas más avanzados describen cómo entrenar los modelos mediante validación cruzada y barrido de hiperparámetros.

En estos temas, los pasos de modelado también contienen código que muestra cómo entrenar, evaluar, guardar y usar cada tipo de modelo. Se ha usado Python para codificar la solución y para mostrar los trazados relevantes, y se proporcionan Notebooks de Python que se pueden ejecutar en los Notebooks de Jupyter instalados en los clústeres de Spark.


## Requisitos previos

1\. Antes de comenzar estos temas, debe tener una suscripción a Azure. Si aún no tiene una, consulte cómo [obtener una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\. Para crear el clúster de Spark en HDInsight, consulte [Introducción: creación de clústeres Apache Spark en HDInsight de Azure](../hdinsight/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Datos de taxis de Nueva York de 2013

Los datos de carreras de taxi de Nueva York son aproximadamente 20 GB de archivos comprimidos de valores separados por comas (CSV) (~48 GB sin comprimir), que incluyen más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. Cada registro de carrera incluye la hora y la ubicación de recogida y de entrega, el número de licencia de (del conductor) anónimo y el número de ida y vuelta incluye la ubicación de entrega y recogida y el tiempo, la número de licencia y el número de identificador único del taxi. Los datos cubren todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

1. Los archivos CSV 'trip\_data' contienen información detallada de las carreras, como el número de pasajeros, los puntos de recogida y destino, la duración de las carreras y la longitud del recorrido. Estos son algunos registros de ejemplo:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Los archivos CSV 'trip\_fare' contienen información detallada de la tarifa que se paga en cada carrera, como el tipo de pago, el importe de la tarifa, los suplementos e impuestos, las propinas y peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


La clave única para unir trip\\_data y trip\\_fare se compone de los campos: medallion, hack\\_licence y pickup\\_datetime.


Hemos tomado una muestra del 0,1 % de estos archivos y los hemos combinado en un único conjunto de datos que se usará como conjunto de datos de entrada en este tutorial. La clave única para unir trip\\_data y trip\\_fare se compone de los campos: medallion, hack\\_licence y pickup\\_datetime. Cada registro del conjunto de datos contiene los siguientes atributos que representan una carrera de taxi de Nueva York:


|Campo| Breve descripción
|------|---------------------------------
| medallion |Licencias de taxi anónimas (identificador único del taxi)
| hack\_license |	Número de licencia anónima de taxi londinense
| vendor\_id |	Identificador del proveedor del taxi
| rate\_code | Categoría de tarifa de taxi de Nueva York
| store\_and\_fwd\_flag | Indicador de “guardar y reenviar”
| pickup\_datetime |	Fecha y hora de subida
| dropoff\_datetime | Fecha y hora de bajada
| pickup\_hour |	Hora de subida
| pickup\_week |	Semana del año de subida
| weekday |	Día de la semana (intervalo de 1 a 7)
| passenger\_count |	Número de pasajeros en una carrera de taxi
| trip\_time\_in\_secs | Tiempo de la carrera, en segundos
| trip\_distance | Distancia recorrida en la carrera, en millas
| pickup\_longitude | Longitud del punto de subida
| pickup\_latitude |	Latitud del punto de subida
| dropoff\_longitude | Longitud del punto de bajada
| dropoff\_latitude | Latitud del punto de bajada
| direct\_distance |	Distancia directa entre las ubicaciones de subida y bajada
| payment\_type | Tipo de pago (efectivo, tarjeta de crédito, etc.)
| fare\_amount | Importe de la tarifa en
| surcharge | Suplemento
| mta\_tax |	Impuestos de MTA
| tip\_amount | Importe de la propina
| tolls\_amount | Importe de los peajes
| total\_amount | Importe total
| tipped | Con propina (0 o 1 para no o sí)
| tip\_class | Clase de propina (0: 0 $, 1: 0-5 $, 2: 6-10 $, 3: 11-20 $, 4: > 20 $)


## Ejecución del código desde un Notebook de Jupyter en el clúster de Spark 

Puede iniciar el Notebook de Jupyter desde el Portal de Azure: busque el clúster de Spark y haga clic en él para entrar en la página de administración de clúster detallada. Ahí puede hacer clic en **Paneles de clúster**, donde el icono del Notebook de Jupyter está asociado con el clúster de Spark.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

También puede ir a ******https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acceder a los Notebooks de Jupyter. Necesitará la contraseña de su cuenta de administrador para acceder a los Notebooks.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Vaya a Python para ver los Notebooks existentes que ejecutan scripts de Python. Verá un directorio que contiene algunos ejemplos de Notebooks preempaquetados. El Notebook que contiene los ejemplos de código de este tema están disponibles en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).

Puede cargar los Notebooks directamente desde Github en el servidor de Notebooks de Jupyter, en su clúster de Spark. En la página principal de su instancia de Jupyter, haga clic en el botón **Load** (Cargar) en la parte derecha de la pantalla. Se abrirá un explorador de archivos. Pegue aquí la dirección URL de Github (contenido sin procesar) del Notebook y haga clic en **Open** (Abrir). Verá el nombre de archivo en la lista de archivos de Jupyter de nuevo con un botón **Load** (Cargar). Haga clic en este botón **Upload** (Cargar). Ahora ya ha importado el Notebook. Repita estos pasos para cargar los Notebooks siguientes de este tutorial.

1.	machine-learning-data-science-spark-data-exploration-modeling.ipynb
2.	machine-learning-data-science-spark-model-consumption.ipynb
3.	machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb

Ahora puede:

- Hacer clic en el Notebook para ver el código.
- Ejecutar cada celda presionando **MAYÚS+ENTRAR**.
- Ejecutar todo el Notebook haciendo clic en **Cell** (Celda) -> **Run** (Ejecutar).


## Pasos siguientes

Ahora que ya ha configurado un clúster de HDInsight Spark y ha cargado los Notebooks de Jupyter, está listo para trabajar con los temas correspondientes a estos tres Notebooks, que muestran cómo crear, usar y modelar mediante validación cruzada y barrido de hiperparámetros.

**Creación de modelos:** para crear los modelos de aprendizaje automático que se puntuarán y evaluarán aquí, siga el tema sobre cómo [crear modelos de regresión y clasificación binaria para datos con el kit de herramientas MLlib de Spark](machine-learning-data-science-spark-data-exploration-modeling.md).

**Consumo de modelos:** para saber cómo puntuar y evaluar los modelos de clasificación y regresión creados en este tema, consulte [Puntuación de modelos de aprendizaje automático creados con Spark](machine-learning-data-science-spark-model-consumption.md).

<!---HONumber=AcomDC_0420_2016-->