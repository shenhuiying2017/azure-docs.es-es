---
title: 'Ciencia de datos escalables con Azure Data Lake: tutorial completo | Microsoft Docs'
description: "Uso de Azure Data Lake para realizar tareas de exploración de datos y clasificación binaria en un conjunto de datos."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 91a8207f-1e57-4570-b7fc-7c5fa858ffeb
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev; weig
ms.openlocfilehash: b18b454d1fcdfb2b6e8ea77508f779aeabdc87a0
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Ciencia de datos escalables con Azure Data Lake: tutorial completo
En este tutorial se muestra cómo utilizar Azure Data Lake para realizar las tareas de exploración de datos y clasificación binaria en un ejemplo del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York para predecir si se dará una propina por tarifa. Le guía por los pasos de todo el [proceso de la ciencia de datos en equipos](http://aka.ms/datascienceprocess), desde la adquisición de los datos al entrenamiento del modelo y, a continuación, a la implementación de un servicio web que publique el modelo.

### <a name="azure-data-lake-analytics"></a>Análisis con Azure Data Lake
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) ofrece todas las funcionalidades necesarias para facilitar a los científicos de datos el almacenamiento de datos de cualquier tamaño, forma y velocidad, y llevar a cabo el procesamiento de datos, análisis avanzado y modelado del aprendizaje automático con alta escalabilidad de una manera rentable.   Se paga por trabajo, solo cuando se procesan los datos. Análisis de Azure Data Lake incluye U-SQL, un lenguaje que mezcla la naturaleza declarativa de SQL con la potencia expresiva de C# para proporcionar una funcionalidad de consulta distribuida escalable. Permite procesar datos sin estructura mediante la aplicación de un esquema al leer, la inserción de lógica personalizada y funciones definidas por el usuario (UDF) e incluye extensibilidad para habilitar un control más preciso sobre la ejecución a escala. Para más información acerca de la filosofía de diseño tras U-SQL, consulte esta [entrada del blog de Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Análisis de Data Lake es también una parte fundamental del conjunto de aplicaciones Cortana Analytics y funciona con SQL Data Warehouse, Power BI y Data Factory. Esto proporciona una plataforma completa de análisis avanzado y macrodatos en la nube.

Este tutorial comienza con la descripción de la instalación de los requisitos previos y recursos necesarios para completar las tareas del proceso de ciencia de datos. A continuación, se describen los pasos de procesamiento de datos mediante U-SQL y se concluye con una demostración de cómo usar Python y Hive con Azure Machine Learning Studio para generar e implementar los modelos predictivos. 

### <a name="u-sql-and-visual-studio"></a>U-SQL y Visual Studio
En este tutorial se recomienda usar Visual Studio para editar scripts U-SQL para procesar el conjunto de datos. Los scripts U-SQL se describen aquí y se proporcionan en un archivo independiente. El proceso incluye el consumo, la exploración y el muestreo de los datos. También se muestra cómo ejecutar un trabajo con scripts U-SQL desde el Portal de Azure. Se crean tablas de Hive para los datos en un clúster de HDInsight asociado para facilitar la generación e implementación de un modelo de clasificación binaria en Azure Machine Learning Studio.  

### <a name="python"></a>Python
Este tutorial también contiene una sección que muestra cómo generar e implementar un modelo predictivo con Azure Machine Learning Studio mediante Python. Proporciona un cuaderno de Jupyter Notebook con los scripts de Python para los pasos del proceso. El cuaderno de Jupyter Notebook incluye código para varios pasos de ingeniería de características adicionales y construcción de modelos, como la clasificación multiclase y los modelos de regresión, además del modelo de clasificación binaria descrito aquí. La tarea de la regresión consiste en predecir el importe de la propina en función de otras características de la propina. 

### <a name="azure-machine-learning"></a>Azure Machine Learning
Se usa Azure Machine Learning Studio para generar e implementar los modelos predictivos. Para esto se aplican dos enfoques: primero con scripts Python y después con tablas de Hive en un clúster de HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
En este tutorial solo se describen los principales pasos. Tanto el **script U-SQL** como **Jupyter Notebook** se pueden descargar de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar estos temas, debe tener lo siguiente:

* Una suscripción de Azure. Si aún no tiene una, consulte [Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)(Obtener una evaluación gratuita de Azure).
* [Recomendado] Visual Studio 2013 o posterior. Si aún no tiene ninguna de estas versiones instaladas, puede descargar una versión gratuita de Community desde [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> En lugar de Visual Studio, también puede usar Azure Portal para enviar las consultas de Azure Data Lake. Se proporcionan instrucciones sobre cómo hacerlo con Visual Studio y en el portal, en la sección titulada **Procesamiento de datos con U-SQL**. 
> 
> 


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Preparación del entorno de la ciencia de datos para Azure Data Lake
Para preparar el entorno de la ciencia de datos para este tutorial, cree los siguientes recursos:

* Almacén de Azure Data Lake (ADLS) 
* Análisis de Azure Data Lake (ADLA)
* Cuenta de Almacenamiento de blobs de Azure
* Cuenta de Azure Machine Learning Studio
* Herramientas de Azure Data Lake para Visual Studio (se recomienda)

Esta sección proporciona instrucciones sobre cómo crear cada uno de estos recursos. Si opta por usar tablas de Hive con Azure Machine Learning, en lugar de Python, para generar un modelo, también necesita aprovisionar un clúster de HDInsight (Hadoop). Este procedimiento alternativo se describe en la sección de la opción 2.


> [!NOTE]
> Se puede crear **Azure Data Lake Store** por separado o cuando se crea **Azure Data Lake Analytics** como almacenamiento predeterminado. Se hace referencia a las instrucciones para crear cada uno de estos recursos por separado, pero no es preciso crear la cuenta de almacenamiento de Data Lake de forma independiente.
>
> 

### <a name="create-an-azure-data-lake-store"></a>Creación de un Almacén de Azure Data Lake


Cree un ADLS desde [Azure Portal](http://portal.azure.com). Para más información, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de configurar la identidad de AAD del clúster en la hoja **Origen de datos** de la hoja **Configuración opcional** descrita allí. 

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Azure Data Lake
Cree una cuenta de ADLA desde [Azure Portal](http://portal.azure.com). Para más información, consulte [Tutorial: Introducción a Azure Data Lake Analytics mediante Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Creación de una cuenta de Almacenamiento de blobs de Azure
Cree una cuenta de Azure Blob Storage desde [Azure Portal](http://portal.azure.com). Para más información, consulte la sección Crear una cuenta de almacenamiento de [Acerca de las cuentas de almacenamiento de Azure](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Configuración de una cuenta de Azure Machine Learning Studio
Suscríbase a Azure Machine Learning Studio o inicie sesión en él desde la página [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Haga clic en el botón **Empiece ahora** y elija "Free Workspace" (Área de trabajo libre) o "Standard Workspace" (Área de trabajo estándar). Ahora está preparado para crear experimentos en Azure Machine Learning Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Instalación de las herramientas de Azure Data Lake [recomendación]
Instale las herramientas de Azure Data Lake para su versión de Visual Studio desde [Azure Data Lake Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)(Herramientas de Azure Data Lake para Visual Studio).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Después de que la instalación finalice correctamente, abra Visual Studio. Debería ver la pestaña Data Lake en el menú superior. Los recursos de Azure deben aparecer en el panel izquierdo al iniciar sesión en su cuenta de Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>El conjunto de datos NYC Taxi Trips
El conjunto de datos utilizado aquí está disponible públicamente, el [conjunto de datos NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/). El conjunto de datos NYC Taxi Trip consta de aproximadamente 20 GB de archivos CSV comprimidos (aproximadamente, 48 GB sin comprimir), que registran más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. Cada registro de carrera incluye la hora y el lugar de recogida y llegada, el número de licencia del taxista anonimizado y el número de placa (número de identificación único del taxi). Los datos cubren todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

El archivo CSV 'trip_data' contiene información detallada de las carreras, como el número de pasajeros, los puntos de recogida y destino, la duración de las carreras y la longitud del recorrido. Estos son algunos registros de ejemplo:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



El archivo CSV 'trip_fare' contiene información detallada de la tarifa que se paga en cada carrera, como el tipo de pago, el importe de la tarifa, los suplementos e impuestos, las propinas y peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clave única para unir trip\_data y trip\_fare se compone de los siguientes campos: medallion, hack\_license y pickup\_datetime. A los archivos CSV sin formato se puede acceder desde un blob de Almacenamiento de Azure público. El script U-SQL para esta combinación está en la sección [Combinación de las tablas de carreras y tarifas](#join) .

## <a name="process-data-with-u-sql"></a>Procesamiento de datos con U-SQL
Las tareas de procesamiento de datos que se ilustran en esta sección incluyen el consumo, la comprobación de la calidad, la exploración y el muestreo de los datos. También se muestra cómo combinar las tablas de carreras y tarifas. La sección final muestra cómo ejecutar un trabajo con scripts U-SQL desde el Portal de Azure. Estos son los vínculos a cada subsección:

* [Ingesta de datos: leer datos de un blob público](#ingest)
* [Comprobaciones de la calidad de los datos](#quality)
* [Exploración de datos](#explore)
* [Combinación de las tablas de carreras y tarifas](#join)
* [Muestreo de datos](#sample)
* [Ejecución de trabajos U-SQL](#run)

Los scripts U-SQL se describen aquí y se proporcionan en un archivo independiente. Los **scripts U-SQL** completos se pueden descargar de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para ejecutar U-SQL, abra Visual Studio, haga clic en **Archivo --> Nuevo --> Proyecto**, elija **Proyecto U-SQL**, asígnele un nombre y guárdelo en una carpeta.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Se puede usar Azure Portal para ejecutar U-SQL en lugar de Visual Studio. Puede ir hasta al recurso de Azure Data Lake Analytics en el portal y enviar consultas directamente, como se muestra en la ilustración siguiente:
> 
> 

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Ingesta de datos: leer datos de un blob público
A la ubicación de los datos en el blob de Azure se hace referencia como **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** y puede extraerse mediante **Extractors.Csv()**. Sustituya el nombre de su propio contenedor y el nombre de la cuenta de almacenamiento en los siguientes scripts por container_name@blob_storage_account_name en la dirección wasb. Dado que los nombres de archivo están en el mismo formato, podemos usar **trip\_data_{\*\}.csv** para leer los 12 archivos de carreras. 

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

Dado que hay encabezados en la primera fila, es preciso quitar los encabezados y cambiar los tipos de columna por los apropiados. Puede guardar los datos procesados en Azure Data Lake Storage mediante **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ o en la cuenta de Azure Blob Storage mediante **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

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
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

Del mismo modo se puede leer en los conjuntos de datos de tarifas. Haga clic con el botón derecho en Azure Data Lake Store; puede elegir ver los datos en **Azure Portal --> Explorador de datos** o en **Explorador de archivos** dentro de Visual Studio. 

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

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

Busque los registros no válidos en términos de pickup_longitude:

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
Podemos realizar una exploración de datos con los siguientes scripts para comprenderlos mejor.

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
Las tablas de carreras y tarifas pueden combinarse por placa de licencia, hack_license y pickup_time.

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
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
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

A continuación, se realiza un muestreo estratificado por la variable binaria tip_class:

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
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Ejecución de trabajos U-SQL
Cuando termine la edición de los scripts U-SQL, puede enviarlos al servidor mediante su cuenta de Análisis de Azure Data Lake. Haga clic en **Data Lake**, **Enviar trabajo**, seleccione su **cuenta de Analytics**, elija **Paralelismo** y haga clic en el botón **Enviar**.  

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Si el trabajo se cumple correctamente, su estado se muestra en Visual Studio para su supervisión. Cuando finalice la ejecución del trabajo, incluso puede reproducir el proceso de ejecución del trabajo y descubrir los pasos del cuello de botella para mejorar la eficacia del trabajo. También puede ir a Azure Portal para comprobar el estado de los trabajos U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Ahora puede comprobar los archivos de salida en Azure Blob Storage o en Azure Portal. En el paso siguiente, se usan los datos de ejemplo estratificado para nuestro modelo.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Generación e implementación de modelos en Azure Machine Learning
Hay dos opciones disponibles para extraer datos e introducirlos en Azure Machine Learning para generar y 

* En la primera opción, usar los datos muestreados que se han escrito en un blob de Azure (en el paso anterior, **Muestreo de datos** ) y usar Python para generar e implementar modelos desde Azure Machine Learning. 
* En la segunda opción, consultar los datos en Azure Data Lake directamente mediante una consulta de Hive. Para esta opción es necesario que cree un clúster de HDInsight o use uno existente en el que las tablas de Hive apunten a los datos de los taxis de Nueva York en el almacenamiento de Azure Data Lake.  Ambas opciones se describen en las siguientes secciones. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opción 1: Usar Python para generar e implementar modelos de aprendizaje automático
Para generar e implementar modelos de aprendizaje automático con Python, cree un cuaderno de Jupyter Notebook en el equipo local o en Azure Machine Learning Studio. El cuaderno de Jupyter Notebook en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contiene el código completo que se explora, los datos que se visualizan y la ingeniería, el modelado y la implementación de características. En este artículo se mostrarán solo los pasos de implementación y modelado. 

### <a name="import-python-libraries"></a>Importación de bibliotecas de Python
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


### <a name="read-in-the-data-from-blob"></a>Lectura de los datos del blob
* Cadena de conexión   
  
        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Leer como texto
  
        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  
  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)    
* Agregar nombres de columna y separar las columnas
  
        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Cambiar algunas columnas a numérico
  
        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Generación de modelos de aprendizaje automático
Aquí se crea un modelo de clasificación binaria para predecir si un viaje va a tener propina, o no. En Jupyter Notebook puede encontrar otros dos modelos: clasificación multiclase y modelos de regresión.

* En primer lugar, es preciso crear variables ficticias que se puedan utilizar en modelos de scikit-learn
  
        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Crear la trama de datos para el modelado
  
        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
  
        X = data.iloc[:,1:]
        Y = data.tipped
* División 60 - 40 de entrenamiento y pruebas
  
        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regresión logística en conjunto de pruebas
  
        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)
  
       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Puntuar conjunto de datos de pruebas
  
        Y_test_pred = logit_fit.predict(X_test)
* Calcular métricas de evaluación
  
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
  
       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Generación de API de servicio web y su consumo en Python
Querrá poner en operación el modelo de aprendizaje automático una vez que se haya compilado. Aquí se usa el modelo de logística binaria como ejemplo. Asegúrese de que la versión de scikit-learn del equipo local es la 0.15.1. Si usa el servicio Estudio de aprendizaje automático de Azure, no es preciso que se preocupe de este tema.

* Busque las credenciales del área de trabajo en la configuración de Estudio de aprendizaje automático de Azure. En Azure Machine Learning Studio, haga clic en **Configuración** --> **Nombre** --> **Tokens de autorización**. 
  
    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Cree un servicio web
  
        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Obtenga las credenciales del servicio web
  
        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
  
        print url
        print api_key
  
        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Llame a una API de servicio web. Después del paso anterior tendrá que esperar entre 5 y 10 segundos.
  
        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  
       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opción 2: Crear e implementar modelos directamente en Azure Machine Learning
Azure Machine Learning Studio puede leer datos directamente desde Azure Data Lake Store y después usarse para crear e implementar modelos. Este enfoque usa una tabla de Hive que apunta al Almacén de Azure Data Lake. Para esto, es necesario aprovisionar un clúster de HDInsight de Azure independiente, en el que se crea la tabla de Hive. Se muestra cómo hacerlo en las secciones siguientes. 

### <a name="create-an-hdinsight-linux-cluster"></a>Creación de un clúster de HDInsight Linux
Cree un clúster de HDInsight (Linux) desde [Azure Portal](http://portal.azure.com). Para más información, consulte la sección **Creación de un clúster de HDInsight con acceso a Azure Data Lake Store** del artículo [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Creación de una tabla de Hive en HDInsight
Ahora se crean tablas de Hive que se usarán en Azure Machine Learning Studio en el clúster de HDInsight con los datos que se guardaron en Azure Data Lake Store en el paso anterior. Vaya al clúster de HDInsight que acaba de crear. Haga clic en **Configuración** --> **Propiedades** --> **Identidad de AAD del clúster** --> **Acceso a ADLS** y asegúrese de que su cuenta de Azure Data Lake Store se agrega a la lista con derechos de lectura, escritura y ejecución. 

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Luego, haga clic en **Panel** junto al botón **Configuración** y emergerá una ventana. Haga clic en **Vista de Hive** en la esquina superior derecha de la página y verá el **Editor de consultas**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Para crear una tabla, pegue los siguientes scripts de Hive. La ubicación del origen de datos está en la referencia de Azure Data Lake Store de esta forma: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

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
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Cuando finalice la consulta, verá los resultados similares a los siguientes:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Generación e implementación de modelos en Azure Machine Learning Studio
Ahora está preparado para generar e implementar con Azure Machine Learning un modelo que prediga si se paga o no propina. Los datos de ejemplo estratificados están listos para usarse en este problema de clasificación binaria (propina o no). Los modelos predictivos que utilizan la clasificación de varias clases (tip_class) y la regresión (tip_amount) también se pueden generar e implementar con Azure Machine Learning Studio. Sin embargo, aquí solo se muestra cómo hacerlo con el modelo de clasificación binaria.

1. Obtenga los datos e introdúzcalos en Azure ML mediante el módulo **Importar datos**, que se encuentra disponible en la sección **Entrada y salida de datos**. Para obtener más información, consulte la página de referencia sobre el módulo [Importar datos](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) .
2. Seleccione **Consulta de Hive** como **Origen de datos** en el panel **Propiedades**.
3. Pegue el siguiente script de Hive en el editor **Hive database query** (Consulta de base de datos de Hive).
   
        select * from nyc_stratified_sample;
4. Escriba el identificador URI del clúster de HDInsight (se encuentra en Azure Portal), las credenciales de Hadoop, la ubicación de los datos de salida y el nombre de contenedor, la clave o el nombre de la cuenta de Azure Storage.
   
   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)  

En la ilustración siguiente se muestra un ejemplo de un experimento de clasificación binaria que lee los datos de la tabla de Hive.

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Una vez creado el experimento, haga clic en **Configurar servicio web** --> **Servicio web predictivo**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Ejecute el experimento de puntuación creado automáticamente y, cuando haya terminado, haga clic en **Deploy Web Service**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Poco después se muestra el panel del servicio web:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Resumen
Al completar este tutorial, ha creado un entorno de ciencia de datos para generar soluciones completas escalables en Azure Data Lake. Este entorno se usó para analizar un conjunto de datos público grande. Para ello se recorrieron los pasos canónicos del proceso de ciencia de datos, desde la adquisición de datos y el entrenamiento del modelo hasta la implementación del modelo como servicio web. Se usó U-SQL para procesar, explorar y muestrear los datos. Se utilizaron Python y Hive con Azure Machine Learning Studio para generar e implementar modelos predictivos.

## <a name="whats-next"></a>Pasos siguientes
La ruta de aprendizaje del [proceso de ciencia de datos en equipos (TDSP)](http://aka.ms/datascienceprocess) proporciona vínculos a temas que describen cada paso del proceso de análisis avanzado. Hay una serie de tutoriales en la página [Tutoriales del proceso de ciencia de datos en equipos](walkthroughs.md) que muestran cómo usar los recursos y servicios en diversos escenarios de análisis predictivo:

* [Proceso de ciencia de datos en equipos en acción: uso de SQL Data Warehouse](sqldw-walkthrough.md)
* [Proceso de ciencia de datos en equipos en acción: uso de clústeres de Hadoop de HDInsight](hive-walkthrough.md)
* [Proceso de ciencia de datos en equipos: uso de SQL Server](sql-walkthrough.md)
* [Información general sobre el proceso de ciencia de datos con Spark en HDInsight de Azure](spark-overview.md)
