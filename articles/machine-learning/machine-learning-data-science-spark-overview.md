<properties
	pageTitle="Información general sobre la ciencia de los datos con Spark en HDInsight de Azure | Microsoft Azure"
	description="El kit de herramientas MLlib de Spark ofrece importantes funciones de modelado de aprendizaje automático para el entorno distribuido de HDInsight."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath,gokuma"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/05/2016"
	ms.author="bradsev" />

# Información general sobre la ciencia de los datos con Spark en HDInsight de Azure

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

En este conjunto de temas se muestra cómo utilizar Spark en HDInsight para completar tareas comunes de ciencia de datos como la ingesta de datos, el diseño de características, el modelado y la evaluación de modelos. Los datos que se utilizan son un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York en 2013 Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente: En los temas también se muestra cómo almacenar estos modelos en el almacenamiento de blobs de Azure (WASB) y cómo puntuar y evaluar su rendimiento predictivo. En los temas más avanzados se describe cómo se pueden entrenar los modelos mediante validación cruzada y barrido de hiperparámetros. En este tema introductorio también se describe cómo configurar un clúster de Spark que se necesita para completar los pasos en los tres tutoriales proporcionados.

[Spark](http://spark.apache.org/) es una plataforma de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de macrodatos. El motor de procesamiento Spark se ha creado para ofrecer velocidad, facilidad de uso y análisis sofisticados. Las capacidades de cálculo distribuido en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático. [MLlib](http://spark.apache.org/mllib/) es la biblioteca de aprendizaje automático escalable de Spark que ofrece capacidades de modelado en este entorno distribuido.

[Spark en HDInsight](../hdinsight/hdinsight-apache-spark-overview.md) es la oferta de Spark de código abierto hospedada por Azure. También incluye compatibilidad con **cuadernos de PySpark para Jupyter** en el clúster de Spark, que pueden ejecutar consultas interactivas de Spark SQL para transformar, filtrar y visualizar los datos almacenados en blobs de Azure (WASB). PySpark es la API de Python para Spark. Los fragmentos de código que proporcionan las soluciones y muestran los trazados relevantes para visualizar los datos aquí se ejecutan en cuadernos de Jupyter instalados en los clústeres de Spark. En estos temas, los pasos de modelado también contienen código que muestra cómo entrenar, evaluar, guardar y usar cada tipo de modelo.

Los pasos de instalación y el código proporcionados en este tutorial son para Spark 1.6 en HDInsight 3.4. Sin embargo, este código y en los Notebooks son genéricos y debería funcionar en cualquier clúster de Spark. Los pasos de configuración y administración del clúster pueden ser ligeramente diferentes de los que se muestran aquí si no está usando Spark en HDInsight.

## Requisitos previos

1\. Antes de comenzar estos temas, debe tener una suscripción a Azure. Si aún no tiene una, consulte [Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtener una evaluación gratuita de Azure).

2\. Necesita un clúster de Spark 1.6 en HDInsight 3.4 para completar este tutorial. Para crear uno, consulte las instrucciones proporcionadas en [Introducción: creación de clústeres de Apache Spark en HDInsight para Linux y ejecución de consultas interactivas mediante Spark SQL (versión preliminar)](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). En el menú **Seleccionar tipo de clúster** se especifica el tipo de clúster y la versión.


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


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

Puede iniciar Jupyter Notebook desde el portal de Azure. Busque el clúster de Spark en el panel y haga clic en él para entrar en la página de administración del clúster. Después haga clic en **Paneles de clúster** -> **Jupyter Notebook**, para abrir el cuaderno asociado al clúster de Spark.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

También puede ir a ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acceder a los cuadernos de Jupyter. Simplemente reemplace la parte CLUSTERNAME de esta dirección URL por el nombre de su propio clúster. Necesitará la contraseña de su cuenta de administrador para acceder a los Notebooks.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Seleccione PySpark para ver un directorio que contiene algunos ejemplos de Notebooks previamente empaquetados que utilizan la API de PySpark. Los Notebooks que contienen los ejemplos de código para este conjunto de aplicaciones de tema de Spark están disponibles en [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)


Puede cargar los Notebooks directamente desde Github en el servidor de Notebooks de Jupyter, en su clúster de Spark. En la página principal de su instancia de Jupyter, haga clic en el botón **Cargar** en la parte derecha de la pantalla. Se abrirá un explorador de archivos. Pegue aquí la dirección URL de Github (contenido sin procesar) del Notebook y haga clic en **Abrir**. Los Notebooks de PySpark están disponibles en las direcciones URL siguientes:

1.	[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.	[pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.	[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Verá el nombre de archivo en la lista de archivos de Jupyter de nuevo con un botón **Cargar**. Haga clic en este botón **Cargar**. Ahora ya ha importado el Notebook. Repita estos pasos para cargar los Notebooks siguientes de este tutorial.

> [AZURE.TIP] Puede hacer clic con el botón derecho en los vínculos siguientes en el explorador y seleccionar **Copiar vínculo** para obtener la dirección URL del contenido sin procesar de Github que puede pegar en el cuadro de diálogo de carga de explorador de archivos de Jupyter.

Ahora puede:

- Hacer clic en el Notebook para ver el código.
- Ejecutar cada celda presionando **MAYÚS+INTRO**
- Ejecutar todo el Notebook haciendo clic en **Celda** -> **Ejecutar**
- Usar la visualización automática de consultas

> [AZURE.TIP] El kernel de PySpark visualiza automáticamente la salida de las consultas de SQL (HiveQL). Tiene la opción de seleccionar entre diferentes tipos de visualizaciones (tabla, circular, línea, área o barra) mediante los botones del menú **Tipo** del Notebook.

![Curva ROC de regresión logística para el enfoque genérico](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## Pasos siguientes

Ahora que ya ha configurado un clúster de HDInsight Spark y ha cargado los cuadernos de Jupyter, está listo para trabajar con los temas correspondientes a estos tres cuadernos de PySpark, que muestran cómo explorar los datos y crear y utilizar los modelos. El Notebook de exploración y modelado de datos avanzado profundiza más en la inclusión de la validación cruzada, el barrido de los hiperparámetros y en la evaluación de modelos.

**Exploración de datos y modelado con Spark:** Explore el conjunto de datos y cree los modelos de aprendizaje automático que se puntuarán y evaluarán aquí mediante el tema [Exploración y modelado de datos con Spark](machine-learning-data-science-spark-data-exploration-modeling.md).

**Consumo de modelos:** Para saber cómo puntuar los modelos de clasificación y regresión creados en este tema, consulte [Puntuación de modelos de aprendizaje automático creados con Spark](machine-learning-data-science-spark-model-consumption.md).

**Validación cruzada y barrido de hiperparámetros**: Consulte [Exploración y modelado avanzados de datos con Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) sobre cómo pueden prepararse los modelos con el barrido de hiperparámetros y la validación cruzada.

<!---HONumber=AcomDC_0518_2016-->