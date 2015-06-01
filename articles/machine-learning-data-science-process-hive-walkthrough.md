<properties 
	pageTitle="Proceso de ciencia de datos de Azure en acción: uso de clústeres de Hadoop de HDInsight | Azure" 
	description="El proceso de ciencia de datos de Azure integral que usa un clúster de Hadoop de HDInsight para compilar e implementar un modelo que usa un conjunto de datos disponible públicamente." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Proceso de ciencia de datos de Azure en acción: uso de clústeres de Hadoop de HDInsight

En este tutorial, seguirá todo el proceso de ciencia de datos de Azure con un clúster de Hadoop de HDInsight de Azure para compilar e implementar un modelo que funcione con un conjunto de datos disponible públicamente, el conjunto de datos [Carreras de taxi de Nueva York](http://www.andresmh.com/nyctaxitrips/). 


## <a name="dataset"></a>Descripción del conjunto de datos de carreras de taxi de Nueva York

Los datos de carreras de taxi de Nueva York son aproximadamente 20 GB de archivos comprimidos de valores separados por comas (CSV) (~48 GB sin comprimir), que incluyen más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. Cada registro de carrera incluye la hora y la ubicación de recogida y de entrega, el número de licencia (del conductor) anónimo y el número de placa distintiva. Los datos cubren todas las carreras del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

1. Los 'trip_data' archivos CSV contienen detalles de las carreras, como el número de pasajeros, los puntos de recogida y entrega, la duración de las carreras, y la duración del viaje. Estos son algunos registros de ejemplo:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Los 'trip_fare' archivos CSV contienen detalles de la carrera pagada para cada viaje, como el tipo de pago, el importe de la tarifa, el suplemento y los impuestos, las sugerencias y los peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clave única para unir trip_data y trip_fare se compone de los campos: medallion, hack_licence y pickup_datetime.

Hay 12 carreras  'trip_data' y 'trip_fare' archivos, respectivamente. En los nombres de archivo, los números del 1 al 12 representan los meses en que se realizan los viajes. 

## <a name="mltasks"></a>Ejemplos de tareas de predicción
Al trabajar con datos, determinar el tipo de predicciones que desea realizar en función de su análisis ayuda a aclarar las tareas que necesitará incluir en el proceso.
A continuación presentamos tres ejemplos de problemas de predicción que abordaremos en este tutorial cuya formulación se basa en la  *tip_amount*:

1. **Clasificación binaria**: Predecir o no si se pagó una propina para una carrera, es decir, una *tip_amount* que es mayor que $0 es un ejemplo positivo, mientras que una *tip_amount* de $ 0 es un ejemplo negativo.

2. **Clasificación de multiclase**: para predecir el intervalo de los importes de propinas pagados para el viaje. Dividimos el *tip_amount* en cinco ubicaciones o clases:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Tarea de regresión**: para predecir la cantidad de propina pagada para un viaje.  


## <a name="setup"></a>Configurar el entorno de ciencia de datos de Azure

Como puede ver en la guía [Planear su entorno](machine-learning-data-science-plan-your-environment.md), hay varios enfoques que se podrían tomar al trabajar con el conjunto de datos de las carreras de taxi de Nueva York en Azure:

- Trabajar con los datos en blobs de Azure y, a continuación, modelar en Aprendizaje automático de Azure.
- Cargar los datos en una base de datos de SQL Server y, a continuación, modelar en Aprendizaje automático de Azure.
- Cargar los datos en las tablas de subárbol de HDInsight y, a continuación, modelar en el aprendizaje automático de Azure

En función del tamaño del conjunto de datos, la ubicación del origen de datos y el entorno de destino de Azure seleccionado, este escenario es similar al [Escenario n.º 7: Conjunto de datos grande en archivos locales, subárbol de destino en clústeres de Hadoop de HDInsight de Azure](machine-learning-data-science-plan-sample-scenarios.md#largedbtohive).

Para configurar el entorno de ciencia de datos de Azure para este enfoque que usa clústeres de Hadoop de HDInsight de Azure y, de manera más específica, las consultas y las tablas de subárbol, complete los pasos siguientes.

1. [Crear una cuenta de almacenamiento](storage-whatis-account.md)

2. [Crear un área de trabajo de aprendizaje automático de Azure](machine-learning-create-workspace.md)

3. [Aprovisionar una máquina virtual de **Windows** ciencia de datos](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] Los scripts de ejemplo se descargarán en la máquina virtual de ciencia de los datos durante el proceso de instalación de la máquina virtual. Cuando se completa el script posterior a la instalación de máquina virtual, los ejemplos estarán en la biblioteca de documentos de la máquina virtual que se encuentra en  *C:\Users<user_name>\Documents\Data Science Scripts*  Las carpetas de ejemplo se denominan **Scripts de muestra** a continuación. Las consultas de subárbol de ejemplo se encuentran en los archivos con la extensión .hql de la carpeta **Scripts de muestra**. Tenga en cuenta que el *<nombre_usuario>* al que se hace referencia en la ruta de acceso a esta carpeta es el nombre de inicio de sesión de Windows de la máquina virtual, no su nombre de usuario de Azure.

4. [Personalizar clústeres de Hadoop de HDInsight de Azure de ciencia de datos](machine-learning-data-science-customize-hadoop-cluster.md). Este paso creará un clúster de HDInsight de Hadoop de Azure con Anaconda Python 2.7 de 64 bits instalado en todos los nodos. Después de que se cree el clúster de Hadoop personalizado, habilite el acceso remoto en el nodo principal del clúster de Hadoop en el portal de Azure mediante el procedimiento descrito en este tema de personalización.


## <a name="getdata"></a>Obtener los datos de un origen público

Para obtener el conjunto de datos de [carreras de Nueva York](http://www.andresmh.com/nyctaxitrips/) desde su ubicación pública, puede usar cualquiera de los métodos descritos en [Mover datos hacia y desde el almacenamiento de blobs de Azure](machine-learning-data-science-move-azure-blob.md) para copiar los datos en su nueva máquina virtual.

Para copiar los datos mediante AzCopy:

1. Inicie sesión en la máquina virtual (VM)

2. Cree un nuevo directorio en el disco de datos de la máquina virtual (Nota: No use el disco temporal que se incluye con la máquina virtual como disco de datos).

3. Desde una ventana de símbolo del sistema, ejecute los siguientes comandos de AzCopy, reemplazando <ruta_de_acceso_a_carpeta_de_datos> con la ruta de acceso a la carpeta de datos creada en (2):

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	Cuando se complete la operación AzCopy, debe haber un total de 24 archivos CSV comprimidos (12 para trip_data y 12 para trip_fare) en la carpeta de datos.

	>[AZURE.NOTE] *Origen: https://getgoing.blob.core.windows.net/nyctaxitrip* y *origen: https://getgoing.blob.core.windows.net/nyctaxifare* especifican dos contenedores de Azure públicos que se utilizan para compartir los datos de taxis de Nueva York Taxi descomprimidos con los usuarios. La lectura de estos dos contenedores de Azure públicos no requiere una clave de acceso. 

## <a name="upload"></a>Cargar los datos en el contenedor predeterminado del clúster de Hadoop de HDInsight de Azure

Desde un símbolo del sistema o una ventana de Windows PowerShell en la máquina virtual, ejecute el siguiente comando de AzCopy, reemplazando los parámetros siguientes por los valores que especificó al crear el clúster de Hadoop. Por tanto, reemplace:

* ***&#60;ruta_de_acceso_a_carpeta_de_datos>*** por la carpeta de datos que creó en la sección anterior 
* ***& 60;nombre de la cuenta de almacenamiento del clúster de Hadoop>*** por la cuenta de almacenamiento utilizada por el clúster
* ***&#60;contenedor predeterminado del clúster de Hadoop>*** por el contenedor predeterminado utilizado por el clúster
* ***& 60;clave de la cuenta de almacenamiento >*** por la clave para la cuenta de almacenamiento utilizada por el clúster

Tenga en cuenta que si no existen ***nyctaxitripraw*** o ***nyctaxifareraw***, a los que se hace referencia en este comando, se crearán en el contenedor. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Crear tablas y base de datos de subárbol con particiones por mes

Para acceder y ejecutar las consultas de subárbol usadas en este tutorial, inicie sesión en el nodo principal del clúster de Hadoop, abra la línea de comandos de Hadoop en el escritorio del nodo principal y especifique el directorio de subárbol escribiendo el comando

    cd %hive_home%\bin

Si necesita ayuda adicional con estos procedimientos u otros alternativos, vea la sección [Enviar consultas de subárbol directamente desde la línea de comandos de Hadoop](machine-learning-data-science-process-hive-tables.md#submit). 

Si ha creado una máquina virtual de Azure siguiendo las instrucciones [Configurar el entorno de ciencia de datos de Azure](#setup), las consultas de subárbol de ejemplo deberían haberse descargado en la máquina virtual de la carpeta **Scripts de muestra**. Como alternativa, los usuarios pueden desproteger los archivos .hql de [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). 

Para enviar las consultas de subárbol en archivos .hql, deberá transferir los archivos .hql a un directorio local del nodo principal del clúster de Hadoop. En los ejemplos siguientes, se supone que se han transferido los archivos .hql al directorio ***C:\temp&#92;*** del nodo principal.

Este es el contenido del archivo ***C:\temp\sample_hive_create_db_and_tables.hql*** que crea la base de datos de subárbol ***nyctaxidb*** y las tablas ***carrera*** y ***tarifa***.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Escriba el siguiente comando en la línea de comandos de Hadoop del nodo principal para enviar estas consultas de subárbol:
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>Cargar datos en tablas por particiones
Ejecute los siguientes comandos de PowerShell en la línea de comandos de Hadoop para cargar datos en las tablas de subárbol mediante particiones por mes:

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Este es el contenido del archivo  *sample_hive_load_data_by_partitions.hql* para inspección.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>Mostrar bases de datos en clúster de Hadoop de HDInsight

Para mostrar las bases de datos creadas en el clúster de Hadoop de HDInsight dentro de la ventana de la línea de comandos de Hadoop, ejecute el siguiente comando en la línea de comandos de Hadoop:

	hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar las tablas de la base de datos nyctaxidb 
	
Para mostrar las tablas de la base de datos nyctaxidb, ejecute el siguiente comando en la línea de comandos de Hadoop:
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Exploración de datos e ingeniería de características en el subárbol
Las tareas de exploración de datos e ingeniería de características para los datos cargados en las tablas de subárbol se pueden lograr mediante consultas de subárbol. Estos son los ejemplos de dichas tareas por que las que le guiaremos en esta sección:

- Ver los diez registros principales en ambas tablas.
- Explorar distribuciones de datos de algunos campos en diferentes ventanas de tiempo.
- Investigar la calidad de los datos de los campos de longitud y latitud.
- Generar etiquetas de clasificación binaria y multiclase según la **tip_amount**.
- Generar características calculando las distancias de las carreras directas.
- Combinar las dos tablas y extraer una muestra aleatoria que se usará para generar modelos.

Como anticipación, cuando completa esta exploración de datos y la ingeniería de características en el subárbol, está listo para continuar con el aprendizaje automático de Azure. Puede guardar la consulta de subárbol final para extraer y muestrear los datos y, a continuación, copiar y pegar la consulta directamente en un módulo de lector del aprendizaje automático de Azure. A continuación, estará listo para crear un modelo para estos datos.

### Exploración: Ver los diez registros principales en carreras de tabla

Para aprovechar la naturaleza del esquemas de datos y el aspecto de los datos, los usuarios pueden extraer los 10 registros principales de cada tabla. Ejecute las dos consultas siguientes por separado desde la consola de la línea de comandos de Hadoop para inspeccionar los registros.

Para obtener los diez registros principales en *table _trip_*:

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
Para obtener los diez registros principales en *table _fare_*:
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### Exploración: Ver el número de registros en cada una de las 12 particiones

Ejecute el siguiente comando en la consola de la línea de comandos de Hadoop para ver el número de registros de cada una de las 12 particiones mensuales.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### Exploración: Distribución de carreras por licencia

Este ejemplo identifica la licencia (números de taxis) con más de 100 carreras dentro de un período de tiempo. La consulta se beneficia del acceso a la tabla con particiones puesto que está condicionada por la variable de partición **mes**. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

Este es el contenido del archivo  *sample_hive_trip_count_by_medallion.hql* para inspección.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### Exploración: Distribución de viajes por licencia y hack_license

Este es un ejemplo de la agrupación por varias columnas de la tabla, en este caso, por las columnas de licencia y hack_license. Ejecute el siguiente comando desde la consola de la línea de comandos de Hadoop:

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

Este es el contenido del archivo  *sample_hive_trip_count_by_medallion_license.hql* para inspección.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### Evaluación de la calidad de los datos: Comprobar registros con latitud y longitud no válida

En este ejemplo se investiga si alguno de los campos de longitud y latitud contiene un valor no válido (los grados radianos deben encontrarse entre -90 y 90) o tienen coordenadas (0, 0).

Ejecute el siguiente comando desde la consola de la línea de comandos de Hadoop:

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

El argumento *-S* incluido en este comando suprime la impresión de la pantalla de estado de los trabajos de asignación/reducción de subárbol. Esto es útil porque hace que la impresión de la pantalla de la salida de la consulta de subárbol sea más legible. 

Este es el contenido del archivo  *sample_hive_quality_assessment.hql* para inspección.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### Exploración: Frecuencias de carreras con y sin propinas

Este ejemplo busca el número de carreras en las que se han dado propinas frente a aquellas en las que no se han dado en un período de tiempo determinado (o en el conjunto de datos completo si se abarca todo el año). Esta distribución refleja la distribución de etiquetas binaria que se utilizará posteriormente para los modelos de clasificación binaria.

Ejecute el siguiente comando desde la consola de la línea de comandos de Hadoop:

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

Este es el contenido del archivo  *sample_hive_tipped_frequencies.hql* para inspección.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### Exploración: Frecuencias de intervalos de propinas

Este ejemplo calcula la distribución de los intervalos de propina dentro de un período de tiempo determinado (o en el conjunto de datos completo si abarca todo el año). Esta es la distribución de las clases de etiquetas que se usarán posteriormente para el modelado de clasificación multiclase.

Ejecute el siguiente comando desde la consola de la línea de comandos de Hadoop:

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

Este es el contenido de  *sample_hive_tip_range_frequencies.hql* para inspección.

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### Exploración: Calcular la distancia directa y compararla con la distancia de la carrera

En este ejemplo se calcula la distancia de carrera directa (en millas). En el ejemplo se limitan los resultados a coordenadas válidas solo usando la consulta de evaluación de calidad de datos tratada anteriormente.

Ejecute el siguiente comando desde la consola de la línea de comandos de Hadoop:

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

Este es el contenido del archivo  *sample_hive_trip_direct_distance.hql* para inspección.

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

Cuando finalice la consulta, los resultados se escriben en un blob de Azure ***queryoutputdir/000000_0*** bajo el contenedor predeterminado del clúster de Hadoop. Si utiliza el Explorador de almacenamiento de Azure, debe ser capaz de ver este blob en el contenedor predeterminado del clúster de Hadoop, como se muestra en la siguiente ilustración.

![Create workspace][2]

Tenga en cuenta que puede aplicar el filtro (resaltado con un cuadro rojo) para recuperar solo el blob con letras especificadas en los nombres. 

Cuando finalice la consulta, puede usar el SDK de Azure para leer el resultado de la consulta del blob de Azure en un marco de datos de pandas para llevar a cabo exploraciones adicionales y procesos. Consulte [Procesar datos de blob de Azure de proceso en su entorno de ciencia de datos](machine-learning-data-science-process-data-blob.md) para obtener instrucciones acerca de cómo leer blobs de Azure en marcos de datos de pandas. 
	
### Preparar datos para la generación de modelos

La consulta proporcionada en esta sección combina las tablas **nyctaxidb.trip** y **nyctaxidb.fare**, genera una etiqueta de clasificación binaria **con propinas** y una etiqueta de clasificación de clase **tip_class**. Esta consulta se puede copiar y pegar directamente en el módulo del lector de[Estudio de aprendizaje automático de Azure](https://studio.azureml.net) para la ingesta directa de datos desde la **consulta de subárbol** en Azure. Esta consulta también excluye los registros con las coordenadas de latitud y longitud incorrectas.

Esta consulta aplica los UDF incrustados de subárbol directamente para generar varias características de los registros de subárbol, incluida la hora, la semana del año y el día de la semana (1 indica lunes y 7, domingo) del campo _pickup_datetime_. Los usuarios pueden hacer referencia a [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) para obtener una lista completa de UDF de subárbol incrustados.

Esta consulta también muestrea los datos para que los resultados de consulta quepan en el Estudio de aprendizaje automático de Azure. Solo cerca del 1 % se importa en el Estudio.

Envíe la consulta ejecutando el comando siguiente desde la consola de línea de comandos de Hadoop:

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

Este es el contenido del archivo  *sample_hive_prepare_for_aml.hql* para inspección.
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>Crear modelos en el aprendizaje automático de Azure

Ahora estamos preparados para continuar con la generación de modelos y la implementación de modelos en el [Aprendizaje automático de Azure](https://studio.azureml.net). Los datos ahora están preparados para los usemos para abordar los problemas de predicción identificados anteriormente:

1. **Clasificación binaria**: para predecir si se dio propina en una carrera o no.

2. **Clasificación de multiclase**: para predecir el intervalo de importes de propinas para la carrera, usando las clases definidas anteriormente. 

3. **Tarea de regresión**: para predecir la cantidad de propina pagada para un viaje.  

Para iniciar el ejercicio de modelado, inicie sesión en el área de trabajo de Aprendizaje automático de Azure. Si aún no ha creado un área de trabajo de aprendizaje automático, consulte [Crear un área de trabajo de Aprendizaje automático de Azure](machine-learning-create-workspace.md).

1. Para empezar a usar el Aprendizaje automático de Azure, consulte [¿Qué es el Estudio de aprendizaje automático de Azure?](machine-learning-what-is-ml-studio.md)

2. Inicie sesión en el [Estudio de aprendizaje automático de Azure](https://studio.azureml.net).

3. La página principal del Estudio ofrece una gran cantidad de información, vídeos, tutoriales, vínculos a referencias de módulos y otros recursos. Para obtener más información acerca de Aprendizaje automático de Azure, consulte el [Centro de documentación de Aprendizaje automático de Azure](http://azure.microsoft.com/documentation/services/machine-learning/).

Un experimento de entrenamiento típico consta de las siguientes acciones:

1. Crear un experimento **+NUEVO**.
2. Proporcionar los datos a Aprendizaje automático de Azure.
3. Preprocesar, transformar y manipular los datos según sea necesario.
4. Generar características según sea necesario.
5. Dividir los datos en conjuntos de datos de entrenamiento, validación y pruebas (o disponer de conjuntos de datos independientes para cada uno).
6. Seleccionar uno o varios algoritmos de aprendizaje automático, según el problema de aprendizaje que se quiera resolver. Por ejemplo: clasificación binaria, clasificación multiclase, regresión.
7. Entrenar uno o varios modelos utilizando el conjunto de datos de entrenamiento.
8. Puntuar el conjunto de datos de validación con los modelos entrenados.
9. Evaluar los modelos para calcular las métricas relevantes para el problema de aprendizaje.
10. Ajustar los modelos y seleccionar el mejor para su implementación.

En este ejercicio, ya hemos explorado y diseñado los datos en subárbol (pasos 1-4) y hemos decidido sobre el tamaño de muestra para la ingesta en Aprendizaje automático de Azure. Para crear uno o varios de los modelos de predicción:

1. Proporcionar los datos a Aprendizaje automático de Azure con el módulo **Lector**, disponible en la sección **Entrada y salida de datos**. Para obtener más información, consulte la página de referencia del módulo [Lector](http://msdn.microsoft.com/library/dn784775).

	![Create workspace][15]

2. Seleccione la **consulta de subárbol** como el **origen de datos** del panel **Propiedades**.

3. Escriba la información del clúster de Hadoop de HDInsight de la siguiente manera. El **nombre de la cuenta de almacenamiento de Azure** tiene que ser la cuenta de almacenamiento que se usa para crear el clúster de Hadoop de HDInsight y el **nombre del contenedor de Azure** tiene que ser el contenedor predeterminado del clúster de Hadoop. 

	![Create workspace][11]

4. En el área de texto editable **Consulta de base de datos de subárbol**, pegue la consulta que extrae los campos de la base de datos necesarios (incluidos los campos calculados, como las etiquetas) y reduce la muestra de los datos al tamaño de muestra deseado.

En la ilustración siguiente se muestra un ejemplo de un experimento de clasificación binaria que lee datos directamente de la consulta de subárbol. Se pueden construir experimentos similares para problemas de clasificación multiclase y de regresión.

![Create workspace][12]

> [AZURE.IMPORTANT] En los ejemplos de consultas de extracción y muestreo de datos de las secciones anteriores, **todas las etiquetas de los tres ejercicios de modelado se incluyen en la consulta**. Un paso importante (obligatorio) en cada uno de los ejercicios de modelado consiste en **excluir** las etiquetas innecesarias de los dos problemas y cualquier otra **fugas de destino**. Por ejemplo., cuando se use clasificación binaria, se utiliza la etiqueta **tipped** y se excluyen los campos **tip_class**, **tip_amount** y **total_amount**. Estos últimos son fugas de destino ya que implican que se pagó propina.

> En este experimento, el primer módulo **Editor de metadatos** agrega nombres de columnas a los datos de salida desde el módulo del lector. Este módulo es necesario puesto que el módulo del lector que lee datos desde la consulta de subárbol no crea nombres de columnas para los datos de salida. 

> Para excluir columnas innecesarias o fugas de destino, puede usar el módulo **Proyectar columnas** o el **Editor de metadatos**. Para obtener más información, consulte las páginas de referencia [Proyectar columnas](http://msdn.microsoft.com/library/dn784740) y [Editor de metadatos](http://msdn.microsoft.com/library/dn784761).

## <a name="mldeploy"></a>Implementar modelos en Aprendizaje automático de Azure

Cuando el modelo esté listo, podrá implementarlo como un servicio web directamente desde el experimento. Para obtener más información sobre la publicación de servicios web de Aprendizaje automático de Azure, vea [Operaciones de servicio de la API de Aprendizaje automático de Azure](machine-learning-overview-of-azure-ml-process.md).

Para implementar un nuevo servicio web, deberá:

1. Crear un experimento de puntuación.
2. Publicar el servicio web.

Para crear un experimento de puntuación a partir de un experimento de entrenamiento **Finalizado**, haga clic en **CREAR EXPERIMENTO DE PUNTUACIÓN** en la barra de acciones inferior.

![Azure Scoring][13]

Aprendizaje automático de Azure intentará crear un experimento de puntuación en función de los componentes del experimento de entrenamiento. En concreto, hará lo siguiente:

1. Guardar el modelo entrenado y quitar los módulos de entrenamiento del modelo.
2. Identificar un **puerto de entrada** lógico para representar el esquema de datos de entrada esperado.
3. Identificar un **puerto de salida** lógico para representar el esquema de salida del servicio web.

Cuando se crea el experimento de puntuación, revíselo y ajústelo según sea necesario. Un ajuste común consiste en reemplazar la consulta o el conjunto de datos de entrada por uno que excluya los campos de etiqueta, ya que estos no estarán disponibles cuando se llame al servicio. También es una buena práctica reducir el tamaño de la consulta o del conjunto de datos de entrada a unos pocos registros, los suficientes para indicar el esquema de entrada. Para el puerto de salida, es habitual excluir todos los campos de entrada y solo incluir las **Etiquetas puntuadas** y las **Probabilidades puntuadas** en la salida mediante el módulo **Proyectar columnas**.

En la ilustración siguiente se muestra un ejemplo de experimento de puntuación. Cuando todo esté listo para publicar, haga clic en el botón **PUBLICAR SERVICIO WEB** de la barra de acciones inferior.

![Create workspace][14]

Resumiendo, en este tutorial, ha creado un entorno de ciencia de datos de Azure que funciona con un conjunto de datos público grande. Empezando por la adquisición de datos, y continuando por las tareas de exploración e ingeniería de características en el proceso de ciencia de datos, para modelar el entrenamiento y publicación de un servicio web del Aprendizaje automático de Azure.

## Información de licencia

Microsoft comparte este tutorial de ejemplo y sus scripts adjuntos bajo la licencia MIT. Consulte el archivo LICENSE.txt que se encuentra en el directorio del código de ejemplo en GitHub para obtener más detalles.

## Referencias

*	[Página de descarga de NYC Taxi Trips de Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing NYC's Taxi Trip Data de Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[Estadísticas e investigación de la Comisión de taxis y limusinas de la Ciudad de Nueva York](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49-->