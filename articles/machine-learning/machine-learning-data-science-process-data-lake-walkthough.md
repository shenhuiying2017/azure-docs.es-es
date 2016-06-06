<properties
	pageTitle="Ciencia de datos escalables en Azure Data Lake: tutorial completo | Microsoft Azure"
	description="Uso de Azure Data Lake para realizar tareas de exploración de datos y clasificación binaria en un conjunto de datos."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,wguo123"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="bradsev;weig"/>


# Ciencia de datos escalables en Azure Data Lake: tutorial completo

En este tutorial se muestra cómo utilizar Azure Data Lake para realizar las tareas de exploración de datos y clasificación binaria en un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York para predecir si se dará una propina por tarifa. Le guía por los pasos de todo el [proceso de la ciencia de datos](http://aka.ms/datascienceprocess), desde la adquisición de los datos al entrenamiento del modelo y, a continuación, a la implementación de un servicio web que publique el modelo.

**Análisis con Azure Data Lake**

[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) tiene todas las funcionalidades necesarias para facilitar a los científicos de datos el almacenamiento de datos de cualquier tamaño, forma y velocidad, y llevar a cabo el procesamiento de datos, análisis avanzado y modelado del aprendizaje automático con alta escalabilidad de una manera rentable. Se paga por trabajo, solo cuando se procesan los datos. Análisis de Azure Data Lake incluye U-SQL, un lenguaje que mezcla la naturaleza declarativa de SQL con la potencia expresiva de C# para proporcionar una funcionalidad de consulta distribuida escalable. Permite procesar datos sin estructura mediante la aplicación de un esquema al leer, insertar lógica personalizada y UDF, e incluye extensibilidad para habilitar un control más preciso sobre la ejecución a escala. Para más información acerca de la filosofía de diseño que hay detrás de U-SQL, consulte esta [entrada del blog de Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Análisis de Data Lake es también una parte fundamental del conjunto de aplicaciones Cortana Analytics y funciona con Almacenamiento de datos SQL de Azure, Power BI y Data Factory. Esto proporciona una plataforma completa de análisis avanzado y macrodatos en la nube.

Este tutorial comienza con la descripción de los requisitos previos y recursos necesarios para completar con Análisis de Data Lake las tareas que forman el proceso de ciencia de datos y cómo instalarlos. Después, se describen los pasos de este proceso que se llevan a cabo mediante SQL U y Aprendizaje automático de Azure y, seguidamente, se explica cómo utilizar Python para realizar tareas similares.


**U-SQL y Aprendizaje automático de Azure**

Este tutorial utiliza Visual Studio para editar scripts U-SQL para procesar el conjunto de datos. Los scripts U-SQL se describen aquí y se proporcionan en un archivo independiente. El proceso incluye el consumo, la exploración y el muestreo de los datos. A continuación, muestra cómo ejecutar un trabajo con scripts U-SQL desde el Portal de Azure. Se crean tablas de Hive para los datos en un clúster de HDInsight asociado para facilitar la generación e implementación de un modelo de clasificación binaria en Estudio de aprendizaje automático de Azure.


**Python**

Este tutorial también contiene una sección que muestra cómo realizar estas tareas de ciencia de datos mediante Python en un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York. Ofrecemos un cuaderno de Jupyter Notebook con los scripts de Python para cada uno de los pasos de este proceso. El cuaderno de Jupyter Notebook incluye código para varios pasos de ingeniería de características adicionales y construcción de modelos, como la clasificación multiclase y los modelos de regresión, además del modelo de clasificación binaria descrito aquí. La tarea de la regresión consiste en predecir el importe de la propina en función de otras características de la propina.


**Scripts**

En este tutorial solo se describen los principales pasos. Tanto el **script U-SQL** como **Jupyter Notebook** se pueden descargar de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## Requisitos previos

Antes de empezar estos temas, debe tener lo siguiente:

- Una suscripción de Azure. Si aún no tiene una, consulte [Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtener una evaluación gratuita de Azure).

- [Recomendado] Visual Studio 2013 o 2015. Si ya tiene una de estas versiones instaladas, puede descargar una versión gratuita de la Edición Community desde [aquí](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Haga clic en el botón **Descargar Community 2015** en la sección de Visual Studio.

>[AZURE.NOTE] En lugar de Visual Studio, también puede usar el Portal de Azure para enviar las consultas de Azure Data Lake. Proporcionaremos instrucciones sobre cómo hacerlo con Visual Studio y en el portal en la sección titulada **Proceso de datos con U-SQL**.

- Suscripción a la versión preliminar de Azure Data Lake

>[AZURE.NOTE] Para usar Almacén de Azure Data Lake (ADLS) y Análisis de Azure Data Lake (ADLA), ya que estos servicios están en versión preliminar. Cuando cree su primer ADLS o ADLA, se le solicitará que se registre. Para registrarse, haga clic en **Registrarse para obtener la versión preliminar**, lea el contrato y haga clic en **Aceptar**. Por ejemplo, esta es la página de registro de ADLS:

 ![2](./media/machine-learning-data-science-process-data-lake-walkthough/ADLA-preview-signup.PNG)
 


## Preparación del entorno de la ciencia de datos para Azure Data Lake
Para preparar el entorno de la ciencia de datos para este tutorial, cree los siguientes recursos:

- Almacén de Azure Data Lake (ADLS) 
- Análisis de Azure Data Lake (ADLA)
- Cuenta de Almacenamiento de blobs de Azure
- Cuenta de Estudio de aprendizaje automático de Azure
- Herramientas de Azure Data Lake para Visual Studio (se recomienda)

Esta sección proporciona instrucciones sobre cómo crear cada uno de estos recursos. Tenga en cuenta que el Almacén de Azure Data Lake se puede crear de manera independiente por separado o al crear Análisis de Azure Data Lake como almacenamiento predeterminado y, de forma similar, la cuenta de Almacenamiento de blobs de Azure puede crearse por separado o como almacenamiento predeterminado cuando se crea un clúster de HDInsight Linux. Se hace referencia a las instrucciones para crear cada uno de estos recursos a continuación por separado, pero no es preciso crear dos cuentas de almacenamiento en pasos independientes.

### Creación de un Almacén de Azure Data Lake

Creación de un ADLS desde el [Portal de Azure](http://ms.portal.azure.com). Para más información, consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de configurar la identidad de AAD del clúster en la hoja **DataSource** de la hoja **Configuración opcional** descrita allí.

 ![3](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLS.PNG)


### Creación de una cuenta de Análisis de Azure Data Lake
Creación de una cuenta ADLA desde el [Portal de Azure](http://ms.portal.azure.com). Para más información, consulte [Tutorial: Introducción a Análisis de Azure Data Lake mediante el Portal de Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLA_new.PNG)


### Creación de una cuenta de Almacenamiento de blobs de Azure
Cree una cuenta de Almacenamiento de blobs de Azure desde el [Portal de Azure](http://ms.portal.azure.com). Para más información, consulte la sección Crear una cuenta de almacenamiento de [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md).
	
 ![5](./media/machine-learning-data-science-process-data-lake-walkthough/Create_Azure_Blob.PNG)


### Configuración de una cuenta de Estudio de aprendizaje automático de Azure
Suscríbase o conéctese a Estudio de aprendizaje automático de Azure desde la página [Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/). Haga clic en el botón **Empiece ahora** y elija "Free Workspace (Área de trabajo libre)" o "Standard Workspace (Área de trabajo estándar)". Una vez hecho esto podrá crear experimentos en Estudio de aprendizaje automático de Azure.

### Instalación de las herramientas de Azure Data Lake 
Instale las herramientas de Azure Data Lake para su versión de Visual Studio desde [Azure Data Lake Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (Herramientas de Azure Data Lake para Visual Studio).

 ![10](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS.PNG)

Después de que la instalación finalice correctamente, abra Visual Studio. Debería ver la pestaña Data Lake en el menú superior. Los recursos de Azure deben aparecer en el panel izquierdo al iniciar sesión en su cuenta de Azure.

 ![11](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS_done.PNG)


## El conjunto de datos NYC Taxi Trips
El conjunto de datos que usamos aquí está disponible públicamente, el [conjunto de datos NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) (Carreras de taxi en la ciudad de Nueva York). El conjunto de datos NYC Taxi Trips consta de aproximadamente 20 GB de archivos de valores separados por comas (CSV) comprimidos (aproximadamente, 48 GB sin comprimir), que registran más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. Cada registro de carrera incluye la hora y el lugar de recogida y llegada, el número de licencia del taxista anonimizado y el número de placa (número de identificación único del taxi). Los datos cubren todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

 - El archivo CSV 'trip\_data' contiene información detallada de las carreras, como el número de pasajeros, los puntos de recogida y destino, la duración de las carreras y la longitud del recorrido. Estos son algunos registros de ejemplo:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - El archivo CSV 'trip\_fare' contiene información detallada de la tarifa que se paga en cada carrera, como el tipo de pago, el importe de la tarifa, los suplementos e impuestos, las propinas y peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clave única para unir trip\_data y trip\_fare se compone de los tres campos siguientes: medallion (placa), hack\_licence (licencia del taxi) y pickup\_datetime (fecha y hora de recogida). A los archivos CSV sin formato se puede acceder desde un blob de Almacenamiento de Azure público.

## Procesamiento de datos con U-SQL

Las tareas de procesamiento de datos que se ilustran en esta sección incluyen el consumo, la comprobación de la calidad, la exploración y el muestreo de los datos. También se muestra cómo combinar las tablas de carreras y tarifas. La sección final muestra cómo ejecutar un trabajo con scripts U-SQL desde el Portal de Azure. Estos son los vínculos a cada subsección:

- [Ingesta de datos: leer datos de un blob público](#ingest)
- [Comprobaciones de la calidad de los datos](#quality)
- [Exploración de datos](#explore)
- [Combinación de las tablas de carreras y tarifas](#join)
- [Muestreo de datos](#sample)
- [Ejecución de trabajos U-SQL](#run)

Los scripts U-SQL se describen aquí y se proporcionan en un archivo independiente. Los **scripts U-SQL** completos se pueden descargar de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para ejecutar U-SQL, abra Visual Studio, haga clic en **Archivo--> Nuevo--> Proyecto**, elija **U-SQL Project** (Proyecto U-SQL), asígnele un nombre y guárdelo en una carpeta.

![12](./media/machine-learning-data-science-process-data-lake-walkthough/create_USQL_project.PNG)

>[AZURE.NOTE] Si usa el Portal de Azure para ejecutar U-SQL, en lugar de Visual Studio, puede navegar al recurso de Análisis de Azure Data Lake y seguir los pasos ilustrados para enviar las consultas.


![29](./media/machine-learning-data-science-process-data-lake-walkthough/portal_submit_job.PNG)

### <a name="ingest"></a>Ingesta de datos: leer datos de un blob público

A la ubicación de los datos en el blob de Azure se hace referencia como ****wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** y puede extraerse mediante **Extractors.Csv()**. Sustituya el nombre de su propio contenedor y el nombre de la cuenta de almacenamiento en los siguientes scripts por container_name@blob_storage_account_name en la dirección wasb. Dado que los nombres de archivo están el mismo formato, podemos usar **trip\_data\_ {*} .csv** para leer los doce archivos de carreras.

	///Read in Trip data
	@trip0 =
	    EXTRACT 
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Dado que hay encabezados en la primera fila, es preciso quitar los encabezados y cambiar los tipos de columna por los apropiados. Puede guardar los datos procesados en Almacenamiento de Azure Data Lake mediante ****swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ o en una cuenta de Almacenamiento de blobs de Azure mediante ****wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**.

	// change data types
	@trip =
	    SELECT 
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    DateTime.Parse(pickup_datetime) AS pickup_datetime,
	    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
	    Int32.Parse(passenger_count) AS passenger_count,
	    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
	    Double.Parse(trip_distance) AS trip_distance,
	    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
	    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
	    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
	    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

	////output data to ADL
	OUTPUT @trip   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_trip.csv"
	USING Outputters.Csv(); 

	////Output data to blob
	OUTPUT @trip   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
	USING Outputters.Csv();  

Del mismo modo podemos leer en los conjuntos de datos de tarifas. Haga clic con el botón derecho en Almacén de Azure Data Lake, puede elegir ver los datos en **Portal de Azure--> Explorador de datos** o **Explorador de archivos** dentro de Visual Studio.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL_VS.PNG)

 ![14](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL.PNG)


### <a name="quality"></a>Comprobaciones de la calidad de los datos

Después de que se hayan leído las tablas de carreras y tarifas, las comprobaciones de la calidad de los datos pueden realizarse de la manera siguiente. Los archivos CSV resultantes pueden ser la salida de Almacenamiento de blobs de Azure o de Almacén de Azure Data Lake.

Busque el número de placas de licencia y un número único de placas de licencia:

	///check the number of medallions and unique number of medallions
	@trip2 =
	    SELECT
	    medallion,
	    vendor_id,
	    pickup_datetime.Month AS pickup_month
	    FROM @trip;
	
	@ex_1 =
	    SELECT
	    pickup_month, 
	    COUNT(medallion) AS cnt_medallion,
	    COUNT(DISTINCT(medallion)) AS unique_medallion
	    FROM @trip2
	    GROUP BY pickup_month;
	    OUTPUT @ex_1   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
	USING Outputters.Csv(); 

Busque las placas de licencia que tenían más de 100 carreras:

	///find those medallions that had more than 100 trips
	@ex_2 =
	    SELECT medallion,
	           COUNT(medallion) AS cnt_medallion
	    FROM @trip2
	    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
	    GROUP BY medallion
	    HAVING COUNT(medallion) > 100;
	    OUTPUT @ex_2   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
	USING Outputters.Csv(); 

Busque los registros no válidos en términos de pickup\_longitude:

	///find those invalid records in terms of pickup_longitude
	@ex_3 =
	    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
	    FROM @trip
	    WHERE
	    pickup_longitude <- 90 OR pickup_longitude > 90;
	    OUTPUT @ex_3   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
	USING Outputters.Csv(); 

Busque los valores que faltan en algunas variables:

	//check missing values
	@res =
	    SELECT *,
	           (medallion == null? 1 : 0) AS missing_medallion
	    FROM @trip;
	
	@trip_summary6 =
	    SELECT 
	        vendor_id,
	    SUM(missing_medallion) AS medallion_empty, 
	    COUNT(medallion) AS medallion_total,
	    COUNT(DISTINCT(medallion)) AS medallion_total_unique  
	    FROM @res
	    GROUP BY vendor_id;
	OUTPUT @trip_summary6
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
	USING Outputters.Csv();



### <a name="explore"></a>Exploración de datos

Podemos realizar una exploración de datos para comprenderlos mejor.

Busque la distribución de las carreras con y sin propina:

	///tipped vs. not tipped distribution
	@tip_or_not =
	    SELECT *,
	           (tip_amount > 0 ? 1: 0) AS tipped
	    FROM @fare;
	
	@ex_4 =
	    SELECT tipped,
	           COUNT(*) AS tip_freq
	    FROM @tip_or_not
	    GROUP BY tipped;
	    OUTPUT @ex_4   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
	USING Outputters.Csv(); 

Busque la distribución del importe de las propinas con valores límite: 0, 5, 10 y 20 dólares.

	//tip class/range distribution
	@tip_class =
	    SELECT *,
	           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
	    FROM @fare;
	@ex_5 =
	    SELECT tip_class,
	           COUNT(*) AS tip_freq
	    FROM @tip_class
	    GROUP BY tip_class;
	    OUTPUT @ex_5   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
	USING Outputters.Csv(); 

Busque las estadísticas básicas de la distancia de las carreras:

	// find basic statistics for trip_distance
	@trip_summary4 =
	    SELECT 
	        vendor_id,
	        COUNT(*) AS cnt_row,
	        MIN(trip_distance) AS min_trip_distance,
	        MAX(trip_distance) AS max_trip_distance,
	        AVG(trip_distance) AS avg_trip_distance 
	    FROM @trip
	    GROUP BY vendor_id;
	OUTPUT @trip_summary4
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
	USING Outputters.Csv();

Busque los percentiles de la distancia de las carreras:

	// find percentiles of trip_distance
	@trip_summary3 =
	    SELECT DISTINCT vendor_id AS vendor,
	                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
	    FROM @trip;
	   // group by vendor_id;
	OUTPUT @trip_summary3
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
	USING Outputters.Csv(); 


### <a name="join"></a>Combinación de las tablas de carreras y tarifas

Las tablas de carreras y tarifas pueden combinarse por placa de licencia, hack\_license y pickup\_time.

	//join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

	//// output to blob
	OUTPUT @model_data_full   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 

	////output data to ADL
	OUTPUT @model_data_full   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 


Para cada nivel de recuento de pasajeros, calcule el número de registros, el importe medio de la propina, la varianza del importe de la propina y el porcentaje de carreras con propina.

	// contigency table
	@trip_summary8 =
	    SELECT passenger_count,
	           COUNT(*) AS cnt,
	           AVG(tip_amount) AS avg_tip_amount,
	           VAR(tip_amount) AS var_tip_amount,
	           SUM(tipped) AS cnt_tipped,
	           (float)SUM(tipped)/COUNT(*) AS pct_tipped
	    FROM @model_data_full
	    GROUP BY passenger_count;
	    OUTPUT @trip_summary8
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
	USING Outputters.Csv();


### <a name="sample"></a>Muestreo de datos

En primer lugar, se selecciona aleatoriamente un 0,1 % de los datos de la tabla combinada:

	//random select 1/1000 data for modeling purpose
	@addrownumberres_randomsample =
	SELECT *,
	        ROW_NUMBER() OVER() AS rownum
	FROM @model_data_full;
	
	@model_data_random_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_randomsample
	WHERE rownum % 1000 == 0;
	
	OUTPUT @model_data_random_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
	USING Outputters.Csv(); 

A continuación, se realiza un muestreo estratificado por la variable binaria tip\_class:

	//stratified random select 1/1000 data for modeling purpose
	@addrownumberres_stratifiedsample =
	SELECT *,
	        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
	FROM @model_data_full;
	
	@model_data_stratified_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_stratifiedsample
	WHERE rownum % 1000 == 0;
	//// output to blob
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 
	////output data to ADL
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 


### <a name="run"></a>Ejecución de trabajos U-SQL

Cuando termine la edición de los scripts U-SQL, puede enviarlos al servidor mediante su cuenta de Análisis de Azure Data Lake. Haga clic en **Data Lake**, **Submit Job** (Enviar trabajo), seleccione su cuenta en **Analytics Account** (Cuenta de Análisis), elija **Parallelism** (Paralelismo), y haga clic en el botón **Submit** (Enviar).

 ![15](./media/machine-learning-data-science-process-data-lake-walkthough/submit_USQL.PNG)

Si el trabajo se cumple correctamente, su estado se mostrará en Visual Studio para su supervisión. Cuando finalice la ejecución del trabajo, incluso puede reproducir el proceso de ejecución del trabajo y descubrir los pasos del cuello de botella para mejorar la eficacia del trabajo. También puede ir al Portal de Azure para comprobar el estado de los trabajos U-SQL.

 ![16](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_running_v2.PNG)


 ![17](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_jobs_portal.PNG)


Ahora puede comprobar los archivos de salida en Almacenamiento de blobs de Azure o en el Portal de Azure. En el paso siguiente, utilizaremos los datos de ejemplo estratificado para nuestro modelo.

 ![18](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv.PNG)

 ![19](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv-portal.PNG)

## Generación e implementación de modelos en Aprendizaje automático de Azure
Se mostrarán dos opciones para extraer datos e introducirlos en Aprendizaje automático de Azure. En la primera opción, puede usar los datos muestreados que se escriben en un Blob de Azure (del paso de **muestreo de datos** paso anterior) y usar Python para generar un modelo e implementarlo en Aprendizaje automático de Azure. En la segunda opción, puede consultar los datos en Azure Data Lake directamente mediante una consulta de Hive. Esta opción necesitará crear un nuevo clúster de HDInsight o usar uno existente en donde se encuentran las tablas de Hive que apuntan a los datos de Almacenamiento de Azure Data Lake. Las dos opciones se explican a continuación.

### Opción 1: Usar Python para generar e implementar modelos de aprendizaje automático

Vamos a generar e implementar modelos de aprendizaje automático mediante Python. Podemos usar Python para leer los datos procesados de Almacenamiento de blobs de Azure (guardados con U-SQL en el paso de **muestreo de datos** anterior) y generar modelos. Creación de un cuaderno de Jupyter Notebook en un equipo local o en Estudio de aprendizaje automático de Azure. En este artículo se mostrarán solo los pasos de implementación y modelas. El cuaderno de Jupyter Notebook contiene el código completo que se explora, los datos que se visualizan y la ingeniería, modelado e implementación de características.

#### Importación de bibliotecas de Python

Para ejecutar el cuaderno de Jupyter Notebook de ejemplo o el archivo de scripts de Python, se necesitan los siguientes paquetes Python. Si usa el servicio Notebook de Aprendizaje automático de Azure, estos paquetes ya están preinstalados.

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random
	import sklearn
	from sklearn.linear_model import LogisticRegression
	from sklearn.cross_validation import train_test_split
	from sklearn import metrics
	from __future__ import division
	from sklearn import linear_model
	from azureml import services


#### Lectura de los datos del blob

- Cadena de conexión   

		CONTAINERNAME = 'test1'
		STORAGEACCOUNTNAME = 'XXXXXXXXX'
		STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
		BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
		blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	
- Leer como texto

		t1 = time.time()
		data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
		t2 = time.time()
		print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![29](./media/machine-learning-data-science-process-data-lake-walkthough/python_readin_csv.PNG)
 
- Agregar nombres de columna y separar las columnas

		colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
		'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
		df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
	


- Cambiar algunas columnas a numérico

		cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
		,'tipped','tip_class','rownum']
		for col in cols_2_float:
		    df1[col] = df1[col].astype(float)

#### Generación de modelos de aprendizaje automático

Aquí se crea un modelo de clasificación binaria para predecir si un viaje va a tener propina, o no. En Jupyter Notebook puede encontrar otros dos modelos: clasificación multiclase y modelos de regresión.

- En primer lugar, es preciso crear variables ficticias que se puedan utilizar en modelos de scikit-learn

		df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
		df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Crear la trama de datos para el modelado

		cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
		data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
		
		X = data.iloc[:,1:]
		Y = data.tipped

- División 60 - 40 de entrenamiento y pruebas

		X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Regresión logística en conjunto de pruebas

		model = LogisticRegression()
		logit_fit = model.fit(X_train, Y_train)
		print ('Coefficients: \n', logit_fit.coef_)
		Y_train_pred = logit_fit.predict(X_train)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_coefficient.PNG)

- Puntuar conjunto de datos de pruebas

		Y_test_pred = logit_fit.predict(X_test)

- Calcular métricas de evaluación

		fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
		print fpr_train, tpr_train, thresholds_train
		
		fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
		print fpr_test, tpr_test, thresholds_test
		
		#AUC
		print metrics.auc(fpr_train,tpr_train)
		print metrics.auc(fpr_test,tpr_test)
		
		#Confusion Matrix
		print metrics.confusion_matrix(Y_train,Y_train_pred)
		print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_evaluation.PNG)


 
#### Generación de API de servicio web y su consumo en Python

Deseamos aplicar el modelo de aprendizaje automático una vez que se haya compilado. Aquí usamos el modelo de logística binaria como ejemplo. Asegúrese de que la versión de scikit-learn del equipo local es la 0.15.1. Si usa el servicio Estudio de aprendizaje automático de Azure, no es preciso que se preocupe de este tema.

- Busque las credenciales del área de trabajo en la configuración de Estudio de aprendizaje automático de Azure. En Estudio de aprendizaje automático de Azure, haga clic en **Settings** (Configuración) --> **Name** (Nombre) --> **Authorization Tokens** (Tokens de autorización). 

	![](./media/machine-learning-data-science-process-data-lake-walkthough/workspace_id.PNG)


		workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
		auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Cree un servicio web

		@services.publish(workspaceid, auth_token) 
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(int) #0, or 1
		def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
		    return logit_fit.predict(inputArray)

- Obtenga las credenciales del servicio web

		url = predictNYCTAXI.service.url
		api_key =  predictNYCTAXI.service.api_key
		
		print url
		print api_key

		@services.service(url, api_key)
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(float)
		def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    pass

- Llame a una API de servicio web. Después del paso anterior tendrá que esperar entre 5 y 10 segundos.

		NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/call_API.PNG)

### Opción 2: Crear e implementar modelos directamente en Aprendizaje automático de Azure

Estudio de aprendizaje automático de Azure se puede usar para leer datos directamente desde el Almacén de Azure Data Lake, crear un modelo e implementarlo. Para que Aprendizaje automático de Azure lea datos directamente del Almacén de Azure Data Lake, es preciso crear una tabla de Hive. Para ello, se debe aprovisionar un clúster de HDInsight de Azure independiente en el que se cree una tabla de Hive que apunte al Almacén de Azure Data Lake. En las subsecciones siguientes se muestra cómo realizar esta operación.

#### Creación de un clúster de HDInsight Linux
Cree un clúster de HDInsight (Linux) desde el [Portal de Azure](http://ms.portal.azure.com). Para más información, consulte la sección Creación de un clúster de HDInsight con acceso al Almacén de Azure Data Lake en [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthough/create_HDI_cluster.PNG)

#### Creación de una tabla de Hive en HDInsight

A continuación se crean tablas de Hive en un clúster de HDInsight con los datos que se guardaron en el Almacén de Azure Data Lake en el paso anterior. Las tablas de Hive se usarán en Estudio de aprendizaje automático de Azure en el paso siguiente. Vaya al clúster de HDInsight que se creó al principio del tutorial. Haga clic en **Configuración** --> **Propiedades** --> **Identidad de AAD del clúster** --> **Acceso a ADLS**, asegúrese de que su cuenta de Almacén de Azure Data Lake se agrega a la lista con derechos de lectura, escritura y ejecución.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_cluster_add_ADLS.PNG)


Luego, haga clic en **Panel**, junto al botón Configuración, y emergerá una ventana. Haga clic en **Vista de Hive** en la esquina superior derecha de la página y verá el **Editor de consultas**.

 ![21](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_dashboard.PNG)

 ![22](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_Editor_v2.PNG)


Para crear una tabla, pegue los siguientes scripts de Hive. La ubicación del origen de datos está en la referencia de Almacén de Azure Data Lake de esta manera: ****adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

	CREATE EXTERNAL TABLE nyc_stratified_sample
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string,
	  payment_type string,
	  fare_amount string,
	  surcharge string,
	  mta_tax string,
	  tolls_amount string,
	  total_amount string,
	  tip_amount string,
	  tipped string,
	  tip_class string,
	  rownum string
	  )
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	LOCATION 'adl://cdsp.azuredatalakestore.net:443/nyctaxi_weig/demo_ex_9_stratified_1_1000_copy.csv';

Cuando finalice la consulta, verá los resultados similares a los siguientes:

 ![23](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_results.PNG)



#### Generación e implementación de modelos en Estudio de aprendizaje automático de Azure

Ya está todo listo para pasar a la creación del modelo y la implementación del mismo en Aprendizaje automático de Azure. Los datos están listos para usarse en los problemas de predicción con los datos de muestreo: clasificación binaria (propina o no), clasificación multiclase (tip\_class) y regresión (tip\_amount). Aquí se describe cómo generar e implementar un modelo de clasificación binaria en Estudio de aprendizaje automático de Azure.

1. Obtenga los datos e introdúzcalos en Aprendizaje automático de Azure mediante el módulo **Lector**, que se encuentra disponible en la sección **Entrada y salida de datos**. Para más información, consulte la página de referencia del [módulo Lector](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/).
2. Seleccione **Consulta de Hive** en **Origen de datos** en el panel **Propiedades**.
3. Pegue el siguiente script de Hive en el editor de **consultas de base de datos de Hive**

    	select * from nyc_stratified_sample;

4. Escriba el identificador URI del clúster de HDInsight (se encuentra en el Portal de Azure), las credenciales de Hadoop, la ubicación de los datos de salida y el nombre de contenedor, la clave o el nombre de la cuenta de Almacenamiento de Azure.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthough/reader_module_v3.PNG)

En la ilustración siguiente se muestra un ejemplo de un experimento de clasificación binaria que lee los datos directamente de la tabla de Hive.

 ![25](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp.PNG)

Una vez creado el experimento, haga clic en **Set Up Web Service** (Configurar servicio web) --> **Predictive Web Service** (Servicio web predictivo)

 ![26](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy.PNG)

Ejecute el experimento de puntuación creado automáticamente y, cuando haya terminado, haga clic en **Implementar servicio web**

 ![27](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy_web.PNG)

Poco después se mostrará el panel del servicio web:

 ![28](./media/machine-learning-data-science-process-data-lake-walkthough/AML_web_api.PNG)


## Resumen

Recapitulación de lo que se ha realizado en este tutorial:

- se ha creado un entorno de ciencia de datos para generar soluciones completas escalables en Azure Data Lake;
- se ha tomado un conjunto de datos público grande a través de los pasos canónicos del proceso de ciencia de datos mediante U-SQL y Python, desde la adquisición de datos a través de entrenamiento del modelo y, a continuación, a la implementación del modelo como servicio web.

<!---HONumber=AcomDC_0525_2016-->